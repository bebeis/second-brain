# 조회 기능과 조회 모델

이 장은 따로 깊게 정리하지 않는다. **실무에서 자주 쓰이는 핵심 패턴 위주라기보단**, DDD 자체와 직접 맞닿은 내용은 아니라고 본다. 상태 변경용 도메인 모델보다 **조회 모델**(주문 목록·상품 상세 같은 조회 기능)을 다루는데, 이런 조회는 JPA만 고집할 필요 없이 MyBatis·JdbcTemplate 같은 다른 기술로도 얼마든지 풀 수 있다. 그래서 여기서는 내 관점에서 "실무에서 얼마나 쓰는지"로만 갈라 메모해 둔다.

## 실무에서 자주 쓰는 편
- `Pageable`, `PageRequest`, `Sort`
- `Page<T>`와 `List<T>` 차이
- `count query`가 언제 나가는지
- 조회 전용 DTO 반환, JPQL `select new ...` 같은 DTO projection

특히 페이징·정렬·목록 조회는 거의 필수다. 이런 건 영한님 JPA 강의에서 잘 다뤄주신다 ㅎㅎ

## 알아두면 좋지만 요즘은 대체재를 많이 쓰는 것
- Spring Data JPA `Specification`, 스펙 조합, 스펙 빌더 클래스

"안 쓰는 기술"은 아니지만, 한국 Spring/JPA 실무에서 복잡한 동적 검색 조건은 **QueryDSL을 더 많이 쓰는 편**이다. `Specification`은 Criteria API 기반이라 코드가 장황하고 가독성이 떨어져서, 이미 QueryDSL을 쓰는 프로젝트면 잘 안 손이 간다. (사실 쌩 쿼리나 JPQL을 더 많이 쓰는 것 같다.)

## 상대적으로 덜 쓰는 것
- Hibernate `@Subselect`, `@Immutable`, `@Synchronize`, 조회 결과를 엔티티처럼 매핑하는 방식

DB View 비슷하게 읽기 전용 모델을 만들 때 쓸 수 있지만, 실무에선 보통 DTO projection·QueryDSL·native query·MyBatis·별도 read model로 해결하는 경우가 더 흔하다.
