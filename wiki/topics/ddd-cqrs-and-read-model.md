---
title: CQRS와 조회 모델
type: topic
sources: [ddd-repository-and-model-mapping, ddd-spring-data-jpa-query, ddd-bounded-context, ddd-cqrs]
updated: 2026-07-10
status: draft
---

# CQRS와 조회 모델

"상태 변경"과 "조회"는 다루는 데이터 범위가 달라서, 하나의 모델로 둘 다 감당하면 조회가 명령 모델을 오염시킨다. 이 긴장은 3·4장에서 예고되고 11장에서 CQRS로 해결된다.

## 문제의 씨앗은 앞 장들에 있다
- **ID 참조의 조회 성능**(3장): 애그리거트를 ID로 참조하면 복잡도는 낮아지지만 목록 화면에서 **N+1 조회**가 난다. 답은 객체 참조로 회귀가 아니라 **조회 전용 쿼리/DAO** → [애그리거트 설계](ddd-aggregate-design.md).
- **매핑의 조회 부담**(4장): `@SecondaryTable`은 조회 시 보조 테이블까지 조인하고, 서로 다른 컬렉션 즉시 로딩은 카타시안 곱을 만든다. 애그리거트가 완전해야 하는 이유는 상태 변경과 화면 표시인데, **화면 표시는 조회 전용 모델이 더 적합**하다 → [리포지터리](../entities/repository-pattern.md). ([#3](../sources/ddd-aggregate.md), [#4](../sources/ddd-repository-and-model-mapping.md))

## 조회 모델은 JPA에 갇히지 않는다 (5장)
5장은 사용자가 "따로 정리하지 않는다"고 밝힌 장이지만 요지는 분명하다 — 조회 모델은 JPA를 고집하지 말고 MyBatis·JdbcTemplate·QueryDSL·네이티브 쿼리를 쓸 수 있다. 실무 필수는 페이징/정렬/목록(`Pageable`, `Page<T>` vs `List<T>`, count 쿼리, DTO projection). 동적 검색은 `Specification`(Criteria 기반)보다 **QueryDSL을 더 많이 쓴다**는 게 사용자 관점 → [#5](../sources/ddd-spring-data-jpa-query.md). ([#5](../sources/ddd-spring-data-jpa-query.md))

## CQRS: 명령 모델과 조회 모델 분리
**CQRS = Command Query Responsibility Segregation.** 상태를 바꾸는 **명령 모델**과 상태를 제공하는 **조회 모델**을 나눈다.
- **명령 모델**: 도메인 로직에 초점. 여러 도메인 객체로 구성. JPA 같은 객체 지향 ORM이 적합.
- **조회 모델**: 화면 데이터를 빠르게. 요약 타입 하나로 구성. SQL 조회에 강한 MyBatis 등이 적합.
- **조회 모델엔 응용 서비스가 없을 수도** 있다(컨트롤러 → DAO 직접) → [응용 서비스 계층](ddd-application-service-layer.md).

도메인이 복잡하고 변경 범위와 조회 범위가 어긋날수록 CQRS가 유리하다. 단일 모델이면 조회 성능을 맞추느라 모델이 불필요하게 복잡해진다. ([#11](../sources/ddd-cqrs.md))

## 저장소 분리와 동기화
명령 모델은 트랜잭션 지원 RDBMS, 조회 모델은 조회 성능이 좋은 메모리 기반 NoSQL을 쓸 수 있다. 두 저장소 동기화는 **10장의 이벤트**로 한다 — 명령 모델 상태 변경 시 이벤트 발생 → 조회 모델에 반영. 즉시 반영은 동기+글로벌 트랜잭션(응답·처리량 저하), 지연 허용은 비동기(성능 저하 완화) → [도메인 이벤트](ddd-domain-events.md). ([#11](../sources/ddd-cqrs.md))

## 웹은 이미 CQRS를 향한다
웹은 변경보다 조회가 훨씬 많다(게시글 1회 등록, 수백만 회 조회). 쿼리 최적화·캐싱·조회 전용 저장소 같은 성능 기법은 결국 **CQRS와 비슷한 효과**다. 화면에 맞춘 캐시 = 조회 전용 모델 캐싱. 대규모 트래픽 서비스는 명시하지 않아도 CQRS 유사 구조를 쓰게 되므로, 명령/조회를 명확히 구분하는 게 낫다. 한 바운디드 컨텍스트 안에서 CQRS로 방식을 섞는 것이 9장의 "여러 방식 혼용"과 만난다 → [바운디드 컨텍스트와 통합](ddd-bounded-context-and-integration.md). ([#9](../sources/ddd-bounded-context.md), [#11](../sources/ddd-cqrs.md))

## 장단점
- **장점**: 명령 모델이 도메인에 집중(조회 성능 코드가 안 섞임), 조회 성능을 자유롭게 높임.
- **단점**: 코드가 늘고, 기술·저장소·메시징이 더 필요할 수 있음.
- 단순 도메인/적은 트래픽엔 유지 비용만 커진다. 트래픽 높은데 단일 모델 고집하면 유지보수 비용이 커진다 — **장단점 비교로 도입 결정**. ([#11](../sources/ddd-cqrs.md))

## 관련 토픽
- [애그리거트 설계](ddd-aggregate-design.md)
- [응용 서비스 계층](ddd-application-service-layer.md)
- [도메인 이벤트](ddd-domain-events.md)
- [바운디드 컨텍스트와 통합](ddd-bounded-context-and-integration.md)
