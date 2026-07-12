---
title: 밸류 객체 (Value Object)
type: entity
updated: 2026-07-10
status: draft
---

# 밸류 객체 (Value Object)

고유 식별자 없이 **개념적으로 완전한 하나의 값**을 표현하는 타입. 주소(Address), 받는 사람(Receiver), 금액(Money) 등. 엔티티나 다른 밸류의 속성으로 쓰인다.

## 성질
- **불변(immutable)**: 값을 바꿀 땐 새 객체로 교체한다. 공유로 인한 의도치 않은 변경을 막고 방어적 복사를 줄인다.
- **동등성은 모든 속성 값으로 판단**한다(엔티티는 식별자로).
- **여러 필드일 필요 없음**: 값 하나를 감싸 의미를 드러내도 된다(int → `Money`). 관련 기능(`Money.add()`)을 가질 수 있다.

## 활용
- **식별자를 밸류로**: `String` 대신 `OrderNo`·`MemberId`로 의미를 명확히. JPA에선 `@EmbeddedId`(+`Serializable`, equals/hashCode).
- **JPA 매핑**: 한 테이블 내 밸류는 `@Embeddable`/`@Embedded`, 한 칼럼 저장은 `AttributeConverter`, 별도 테이블 컬렉션은 `@ElementCollection`.
- 개념상 밸류지만 상속이 필요해 `@Entity`로 매핑해야 할 때도 상태 변경 메서드는 제공하지 않는다.

## 등장
[DDD Start #1](../sources/ddd-domain-model-basics.md), [#2](../sources/ddd-architecture-overview.md), [#4](../sources/ddd-repository-and-model-mapping.md) · 토픽 [도메인 모델 구성요소](../topics/ddd-domain-model-building-blocks.md), [애그리거트 설계](../topics/ddd-aggregate-design.md) · 관련 [애그리거트 루트](aggregate-root.md)
