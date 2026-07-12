---
title: 애그리거트 설계 — 경계·루트·참조·트랜잭션
type: topic
sources: [ddd-architecture-overview, ddd-aggregate, ddd-repository-and-model-mapping, ddd-aggregate-transaction-and-locking]
updated: 2026-07-10
status: draft
---

# 애그리거트 설계 — 경계·루트·참조·트랜잭션

애그리거트는 『도메인 주도 개발 시작하기』 전반을 관통하는 중심 개념이다. 2장에서 구성요소로 소개되고, 3장에서 본격적으로 다뤄지며, 4장에서 JPA 매핑·영속성으로, 8장에서 동시성 제어로 이어진다. 이 페이지는 그 흐름을 하나로 묶는다.

## 애그리거트란 무엇을 해결하나
도메인 모델이 커지면 개별 엔티티·밸류 관계만으로는 전체 구조를 파악하기 어렵고, 변경이 큰 구조를 망가뜨리기 쉽다. **애그리거트는 관련 객체를 하나의 군집으로 묶어 상위 수준에서 도메인을 보게** 한다. 그러면 개별 객체가 아니라 **애그리거트 간 관계로 도메인을 이해**할 수 있어 복잡도가 낮아진다. ([#3](../sources/ddd-aggregate.md))

## 경계는 라이프 사이클과 도메인 규칙으로 정한다
- 같은 애그리거트의 객체는 보통 **함께 생성·제거되고 유사한 라이프 사이클**을 가진다.
- "A가 B를 가진다"는 표시 관계만으로 묶으면 안 된다. 상품과 리뷰는 함께 보이지만 **생성 시점·변경 주체·라이프 사이클이 달라 서로 다른 애그리거트**다.
- 처음엔 커 보이지만 도메인 규칙을 이해할수록 **실제 크기는 작아지는 경우가 많다**. 엔티티 하나짜리 애그리거트도 흔하다. ([#3](../sources/ddd-aggregate.md))

## 루트가 일관성을 책임진다
애그리거트 루트는 대표 엔티티로, **구성 객체 전체가 일관된 상태를 유지하도록 도메인 기능을 제공**한다. 핵심 습관 두 가지가 이를 뒷받침한다 — [애그리거트 루트](../entities/aggregate-root.md), [밸류 객체](../entities/value-object.md) 참조:
1. 단순 필드 변경용 public set을 만들지 않는다. `changeShippingInfo()`처럼 의미가 드러나는 메서드로 규칙을 코드에 모은다.
2. 밸류를 불변으로 만들어 외부에서 내부 상태를 함부로 바꾸지 못하게 한다.

`order.getShippingInfo().setAddress(...)`처럼 루트를 거치지 않으면 상태 검사 규칙이 적용되지 않는다. 내부 객체를 노출하더라도 **변경 기능은 노출하지 않는다**. ([#2](../sources/ddd-architecture-overview.md), [#3](../sources/ddd-aggregate.md))

## 참조: 객체 참조보다 ID 참조
애그리거트가 다른 애그리거트를 참조할 때 **루트 식별자만 보관(ID 참조)**하는 것이 기본이다. 객체 직접 참조는 (1) 결합도 상승, (2) 지연/즉시 로딩 성능 고민, (3) 저장소 분리 확장 어려움을 낳는다. ID 참조는 경계를 명확히 하고 응집도를 높이며 저장소 확장(주문은 RDBMS, 상품은 NoSQL)에 유리하다.

대가는 **N+1 조회 문제**다. 이를 객체 참조로 되돌려 풀지 말고 **조회 전용 쿼리/DAO**로 푼다 → [CQRS와 조회 모델](ddd-cqrs-and-read-model.md)로 이어진다. ([#3](../sources/ddd-aggregate.md))

## 저장·매핑도 애그리거트 단위
- 리포지터리는 **애그리거트 루트 기준**으로만 만든다. OrderLine용 리포지터리는 두지 않는다.
- 저장·조회·삭제는 모두 애그리거트 전체를 대상으로 하고 **원자적으로** 반영돼야 한다(RDBMS는 트랜잭션, NoSQL은 단일 문서).
- JPA 매핑: 루트는 `@Entity`, 한 테이블 내 밸류는 `@Embeddable`/`@Embedded`, `@Entity`로 매핑한 구성요소는 `cascade`+`orphanRemoval`로 라이프 사이클을 일치시킨다. 로딩 전략은 무조건 즉시/지연이 아니라 **크기·빈도·조회 방식**을 보고 고른다. ([#4](../sources/ddd-repository-and-model-mapping.md), [리포지터리](../entities/repository-pattern.md))

## 트랜잭션은 작게, 한 트랜잭션에 한 애그리거트
트랜잭션 범위는 작을수록 좋고, **한 트랜잭션에서는 가급적 하나의 애그리거트만 수정**한다. 여러 개를 바꿔야 하면 한 애그리거트가 다른 애그리거트를 직접 수정하지 말고 **응용 서비스에서 각각 조회해 각 기능을 호출**한다. 더 느슨하게는 [도메인 이벤트](ddd-domain-events.md)로 다른 애그리거트의 변경을 유도한다. ([#3](../sources/ddd-aggregate.md))

## 동시성: 여러 트랜잭션이 같은 애그리거트를 건드릴 때
개념적으로 같은 애그리거트라도 서로 다른 트랜잭션은 물리적으로 다른 객체를 조회하므로 일관성이 깨질 수 있다. 8장은 그 해법을 정리한다:
- **선점 잠금(비관적)**: `for update` / `LockModeType.PESSIMISTIC_WRITE`. 교착 상태 주의 → 최대 대기 시간 지정.
- **비선점 잠금(낙관적)**: `@Version`. 커밋 시점에 버전 충돌을 감지. 여러 트랜잭션(조회↔수정)으로 확장하려면 화면에 버전을 실어 보낸다. 내부 엔티티만 바뀔 때 루트 버전을 올리려면 `OPTIMISTIC_FORCE_INCREMENT`.
- **오프라인 선점 잠금**: 수정 폼~수정 요청의 여러 트랜잭션 구간 전체를 잠근다. `LockManager`(tryLock/checkLock/releaseLock/extendLockExpiration) + 유효 시간. ([#8](../sources/ddd-aggregate-transaction-and-locking.md))

## 애그리거트를 팩토리로
한 애그리거트의 상태로 다른 애그리거트 생성 가능 여부가 결정되면(예: 차단된 상점은 상품 등록 불가), 그 판단을 응용 서비스에 노출하지 말고 **애그리거트에 팩토리 메서드**(`Store.createProduct()`)로 둔다. 도메인 로직의 응집도가 올라간다. ([#3](../sources/ddd-aggregate.md))

## 관련 토픽
- [계층형 아키텍처와 DIP](ddd-layered-architecture-and-dip.md)
- [CQRS와 조회 모델](ddd-cqrs-and-read-model.md) — ID 참조의 조회 성능 문제를 여기서 해결
- [도메인 이벤트](ddd-domain-events.md) — 트랜잭션 경계를 넘는 변경 유도
