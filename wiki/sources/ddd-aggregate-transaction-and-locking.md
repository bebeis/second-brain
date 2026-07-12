---
title: DDD Start #8 — 애그리거트 트랜잭션과 잠금
type: source
source_url:
source_owner: me
raw_copy: raw/ddd-start/도메인 주도 개발 시작하기 #8 여러 애그리거트가 필요한 기능.md
ingested: 2026-07-10
updated: 2026-07-10
status: reviewed
topics: [ddd-aggregate-design]
---

# DDD Start #8 — 애그리거트 트랜잭션과 잠금

> 최범균 『도메인 주도 개발 시작하기』 8장 정리 노트 (본인 필기).

---

## Summary

한 애그리거트를 여러 사용자가 동시에 수정하면, 각 트랜잭션이 물리적으로 서로 다른 객체를 조회하기 때문에 DBMS 트랜잭션만으로는 일관성이 깨질 수 있다(운영자는 배송 상태 변경, 고객은 배송지 변경). 이를 막는 애그리거트 단위 기법이 **선점 잠금(비관적)**, **비선점 잠금(낙관적)**, 그리고 여러 트랜잭션에 걸친 **오프라인 선점 잠금**이다. 선점 잠금은 한 스레드가 끝날 때까지 다른 스레드를 대기시키며 DBMS 행 잠금(`for update`)으로 구현하지만 교착 상태에 주의해야 한다. 비선점 잠금은 버전 값(`@Version`)으로 반영 시점에 충돌을 감지하며, 조회·수정이 다른 트랜잭션이어도 버전을 화면에 실어 보내 확장할 수 있다. 오프라인 선점 잠금은 수정 폼~수정 요청 전 구간 전체에서 잠금을 유지하며 `LockManager`로 구현한다.

---

## Key Claims

1. **동시 수정 시 일관성 문제**: 운영자·고객이 각각 리포지터리로 같은 주문을 조회하면 개념적으로 같지만 물리적으로 다른 객체다. 각자 커밋하면 한쪽이 옛 상태 기준으로 변경해 일관성이 깨진다.
2. **선점 잠금(Pessimistic Lock)**: 먼저 구한 스레드가 끝낼 때까지 다른 스레드의 수정을 막는다. DBMS 행 잠금으로 구현하며, JPA는 `EntityManager.find()`에 `LockModeType.PESSIMISTIC_WRITE`, 스프링 데이터 JPA는 `@Lock(LockModeType.PESSIMISTIC_WRITE)`.
3. **선점 잠금과 교착 상태**: 두 스레드가 A·B를 엇갈려 잠그면 교착이 발생하고, 사용자가 많을수록 확률이 커진다. **최대 대기 시간**을 지정해 방지한다(JPA `javax.persistence.lock.timeout` 힌트, 스프링 데이터 JPA `@QueryHints`). 단 DBMS·구현체마다 지원 범위가 다르다.
4. **선점 잠금의 한계**: 하나의 트랜잭션 범위에서만 동작하므로, 조회 트랜잭션과 수정 트랜잭션이 분리된 경우(운영자가 조회한 뒤 그 사이 고객이 배송지 변경)를 막지 못한다.
5. **비선점 잠금(Optimistic Lock)**: 동시 접근을 막지 않고 반영 시점에 확인한다. 애그리거트에 버전 값을 두고, 수정 쿼리는 "식별자 일치 AND 버전 일치"일 때만 실행되며 성공하면 버전을 1 증가시킨다. 먼저 수정돼 버전이 바뀌면 이후 수정은 실패한다.
6. **JPA `@Version`**: 버전 필드에 `@Version`을 붙이면 UPDATE에 버전 조건이 함께 걸린다. 수정된 행이 0이면 스프링 `@Transactional` 종료 시점에 `OptimisticLockingFailureException`이 발생한다.
7. **여러 트랜잭션으로 확장**: 조회 시 버전(A)을 화면에 함께 전달하고, 수정 요청 시 버전 A를 되보낸다. 현재 버전 B와 비교해 A≠B면 수정 불가(`VersionConflictException`), A=B면 수행. HTML 폼은 버전을 hidden input으로 전송한다. 두 예외를 구분할 필요 없으면 `OptimisticLockingFailureException`만 써도 된다.
8. **강제 버전 증가**: 루트가 아닌 내부 엔티티만 변경되면 JPA가 루트 버전을 올리지 않을 수 있다. 애그리거트 관점에선 내부가 바뀌면 변경된 것이므로, `LockModeType.OPTIMISTIC_FORCE_INCREMENT`로 조회해 트랜잭션 종료 시 루트 버전을 강제 증가시킨다.
9. **오프라인 선점 잠금**: 수정 기능은 보통 (1) 수정 폼 트랜잭션, (2) 수정 반영 트랜잭션으로 나뉜다. 폼 요청 시 잠금을 선점하고 수정 요청 처리 후 해제해, 그 구간 전체에서 다른 사용자의 수정 폼 접근을 막는다.
10. **잠금 유효 시간**: 사용자가 폼을 열어둔 채 종료하면 잠금이 영원히 안 풀리므로 유효 시간을 두고, 정상 편집 중 만료를 막기 위해 주기적(예: Ajax 1분)으로 연장한다.
11. **`LockManager` 인터페이스**: `tryLock(type,id)→LockId`, `checkLock(lockId)`, `releaseLock(lockId)`, `extendLockExpiration(lockId,inc)`. 수정 요청 시 `checkLock()`을 먼저 실행하는 이유는 (1) 만료돼 다른 사용자가 선점했을 수 있고 (2) 잠금 없이 직접 요청할 수 있어서다.
12. **DB 기반 구현**: `locks` 테이블에 `type,id,lockid,expiration_time`. `(type,id)`를 기본키로 두면 동시 잠금을 막고, `lockid`엔 유니크 인덱스. `tryLock()`은 새 트랜잭션에서 기존 잠금 확인→만료분 삭제→새 LockId(UUID) 삽입, 실패 시 `LockingFailException`.

---

## Caveats

- ⚠️ raw 노트에는 선점/비선점 잠금 시퀀스, `@Lock`·`@QueryHints`·`LockManager`·`LockData`·`JdbcTemplate` 기반 구현 코드와 그림이 다수 있으나 정리에서는 개념·흐름만 남기고 생략했다.
- ⚠️ 잠금 타임아웃 힌트의 실제 적용 여부는 사용하는 DBMS와 JPA 구현체에 따라 다르다고 원문이 명시한다 — 도입 시 직접 확인 필요.
- ⚠️ 원본 파일명은 "#8 여러 애그리거트가 필요한 기능"이지만 내용은 실제로 애그리거트 동시성·잠금이 주제다(7장과 파일명이 겹치는 raw 상의 명명 혼선).

---

## Related Topics

- [애그리거트 설계: 경계·루트·ID 참조·트랜잭션](../topics/ddd-aggregate-design.md)
- 엔티티: [애그리거트 루트](../entities/aggregate-root.md), [리포지터리](../entities/repository-pattern.md)
- 관련 소스: [#3 애그리거트](./ddd-aggregate.md) · [#10 이벤트](./ddd-domain-event.md)(한 트랜잭션 한 애그리거트를 이벤트로 보완)
