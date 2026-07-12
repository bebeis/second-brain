---
title: 애그리거트 루트 (Aggregate Root)
type: entity
updated: 2026-07-10
status: draft
---

# 애그리거트 루트 (Aggregate Root)

애그리거트를 대표하는 엔티티. 애그리거트에 속한 객체를 관리하고, 애그리거트가 제공할 기능을 구현하며, **구성 객체 전체의 일관성**을 책임진다. 주문 애그리거트의 루트는 `Order`.

## 핵심 역할
- **일관성 보장**: 상태를 바꾸는 도메인 기능을 루트가 제공하고, 그 안에서 규칙을 먼저 검사한다. `Order.changeShippingInfo()`는 변경 가능 여부를 확인한 뒤 바꾼다.
- **내부 직접 변경 차단**: `order.getShippingInfo().setAddress(...)`처럼 루트를 우회하면 규칙이 적용되지 않는다. 내부 객체를 노출하더라도 변경 기능은 노출하지 않는다.

## 이를 지키는 두 습관
1. 단순 필드 변경용 public set을 만들지 않는다 → 의미 있는 메서드(`cancel()`, `changePassword()`).
2. 밸류를 불변으로 만든다 → [밸류 객체](value-object.md).

## 참조 단위
- 리포지터리는 루트 기준으로만 만든다 → [리포지터리](repository-pattern.md).
- 다른 애그리거트는 루트 식별자(ID)로 참조한다.
- 팩토리: 생성 가능 여부를 자기 상태로 판단하면 루트에 팩토리 메서드를 둔다(`Store.createProduct()`).

## 등장
[DDD Start #2](../sources/ddd-architecture-overview.md), [#3](../sources/ddd-aggregate.md), [#4](../sources/ddd-repository-and-model-mapping.md), [#8](../sources/ddd-aggregate-transaction-and-locking.md) · 토픽 [애그리거트 설계](../topics/ddd-aggregate-design.md)
