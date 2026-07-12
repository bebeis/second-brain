---
title: DDD Start #5 — 스프링 데이터 JPA를 이용한 조회 기능
type: source
source_url:
source_owner: me
raw_copy: raw/ddd-start/도메인 주도 개발 시작하기 #5 스프링 데이터 JPA를 이용한 조회 기능.md
ingested: 2026-07-10
updated: 2026-07-10
status: reviewed
topics: [ddd-cqrs-and-read-model]
---

# DDD Start #5 — 스프링 데이터 JPA를 이용한 조회 기능

> 최범균 『도메인 주도 개발 시작하기』 5장 정리 노트 (본인 필기).

## Summary

이 노트는 5장 내용을 정리하지 않겠다는 **사용자 본인의 메타 판단**이다. 5장은 상태 변경용 도메인 모델이 아니라 주문 목록·상품 상세 같은 **조회 모델**을 다루는 장으로, DDD 자체와 직접 관련이 크지 않고 실무 핵심 패턴도 아니라고 사용자가 보았다. 대신 사용자는 5장 주제를 실무 활용도 기준으로 세 묶음(자주 씀 / 알아두면 좋지만 대체재가 많음 / 상대적으로 덜 씀)으로 분류하고, 조회 모델은 JPA만 고집하지 말고 MyBatis·JdbcTemplate 같은 기술도 함께 쓸 수 있다는 점을 강조한다.

## Key Claims

1. **자주 쓰는 편**: `Pageable`/`PageRequest`/`Sort`, `Page<T>`와 `List<T>` 차이, `count query`가 언제 나가는지, 조회 전용 DTO 반환, JPQL `select new ...` 같은 DTO projection. 페이징·정렬·목록 조회는 실무 필수에 가깝다.
2. **알아두면 좋지만 대체재를 많이 쓰는 내용**: Spring Data JPA `Specification`, 스펙 조합, 스펙 빌더 클래스. 한국 Spring/JPA 실무에서 복잡한 동적 검색은 QueryDSL을 더 많이 쓰며(혹은 생 쿼리·JPQL), `Specification`은 Criteria 기반이라 장황해 QueryDSL이 있으면 잘 안 쓰게 된다.
3. **상대적으로 덜 쓰는 내용**: Hibernate `@Subselect`, `@Immutable`, `@Synchronize`, 조회 결과를 엔티티처럼 매핑하는 방식. DB View 비슷한 읽기 전용 모델에 쓸 수 있으나 실무에서는 DTO projection·QueryDSL·native query·MyBatis·별도 read model로 해결하는 경우가 더 흔하다.

## Caveats

- ⚠️ **이 페이지는 책 5장 내용의 정리가 아니라 사용자 본인의 실무 관점 판단이다.** "QueryDSL을 더 많이 쓴다", "생 쿼리/JPQL을 더 쓰는 것 같다" 등은 책의 주장이 아니라 사용자 경험에 근거한 견해다.
- ⚠️ raw 노트는 페이징·정렬 등을 "영한님의 JPA 강의"로 대체 학습을 권한다. 실제 API 사용법은 이 페이지에 담기지 않는다.

## Related Topics

- [CQRS와 조회 전용 모델](../topics/ddd-cqrs-and-read-model.md) — 조회 전용 모델은 명령 모델과 분리하며, 5장의 조회 기법이 그 조회 모델 구현 수단이 된다(11장에서 재론).
