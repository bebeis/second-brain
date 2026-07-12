---
title: 리포지터리 (Repository)
type: entity
updated: 2026-07-10
status: draft
---

# 리포지터리 (Repository)

애그리거트 단위로 도메인 객체를 저장·조회하는 기능을 정의한다. **인터페이스는 도메인 영역, 구현 클래스는 인프라스트럭처 영역**에 둔다([DIP](dip.md)의 대표 적용 예).

## 기본 규칙
- **애그리거트 루트 기준**으로만 만든다. `Order`에 `OrderLine`이 포함돼도 `OrderRepository`만 두고 `OrderLineRepository`는 두지 않는다 → [애그리거트 루트](aggregate-root.md).
- 기본 기능은 **저장(save)**과 **식별자 조회(findById)**. 필요 시 조건 검색·삭제 추가.
- 저장·조회는 애그리거트 전체 대상. 조회된 애그리거트는 기능 실행에 필요한 구성요소를 모두 포함한 **완전한** 상태여야 한다.

## JPA 구현
- `EntityManager`의 `find()`/`persist()`. 수정은 트랜잭션 안 변경 감지로 자동 UPDATE(별도 메서드 불필요).
- 삭제는 `remove()`, 실무에선 삭제 플래그로 노출만 제어하기도 한다.
- 로딩 전략은 무조건 즉시/지연이 아니라 크기·빈도·조회 방식을 보고 고른다. 조회 화면은 [조회 전용 모델](../topics/ddd-cqrs-and-read-model.md)이 더 적합.
- 식별자 생성 기능을 `nextId()`로 리포지터리에 둘 수도 있다.

## 등장
[DDD Start #2](../sources/ddd-architecture-overview.md), [#4](../sources/ddd-repository-and-model-mapping.md) · 토픽 [애그리거트 설계](../topics/ddd-aggregate-design.md), [CQRS와 조회 모델](../topics/ddd-cqrs-and-read-model.md)
