---
title: 도메인 서비스 (Domain Service)
type: entity
updated: 2026-07-10
status: draft
---

# 도메인 서비스 (Domain Service)

한 애그리거트에 넣기 애매한 도메인 로직을 담는, 도메인 영역의 구성요소. **상태 없이 로직만** 구현하고 필요한 값은 파라미터로 받는다.

## 언제 쓰나
1. **계산 로직**: 여러 애그리거트가 필요하거나 한 애그리거트에 넣기 복잡한 계산(할인 금액 = 상품·쿠폰·회원등급).
2. **외부 시스템 연동 도메인 로직**: 다른 시스템을 써야 하는 도메인 로직(설문 생성 권한 확인).

## 규칙
- 애그리거트에 **필드로 주입하지 않는다**(도메인 서비스는 애그리거트의 데이터가 아님). 애그리거트 메서드에 파라미터로 전달하거나, 반대로 도메인 서비스에 애그리거트를 넘긴다(계좌 이체).
- 외부 연동은 "외부 시스템과 연동"이 아니라 **도메인 로직 관점의 인터페이스**로 표현하고(`SurveyPermissionChecker`), 구현은 인프라에 둔다 → [DIP](dip.md).
- **응용 서비스와 구분**: 애그리거트 상태를 변경/계산하는 도메인 로직이면 도메인 서비스, 트랜잭션 등 실행 흐름이면 [응용 서비스](../topics/ddd-application-service-layer.md).
- 위치는 도메인 영역(`domain.service`).

## 바운디드 컨텍스트 통합에서
컨텍스트 간 통합의 안티코럽션 계층도 도메인 서비스 인터페이스로 표현된다(`ProductRecommendationService`) → [바운디드 컨텍스트와 통합](../topics/ddd-bounded-context-and-integration.md).

## 등장
[DDD Start #7](../sources/ddd-domain-service.md), [#9](../sources/ddd-bounded-context.md) · 토픽 [도메인 모델 구성요소](../topics/ddd-domain-model-building-blocks.md), [응용 서비스 계층](../topics/ddd-application-service-layer.md)
