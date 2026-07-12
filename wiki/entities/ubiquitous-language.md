---
title: 유비쿼터스 언어 (Ubiquitous Language)
type: entity
updated: 2026-07-10
status: draft
---

# 유비쿼터스 언어 (Ubiquitous Language)

에릭 에반스가 강조한, **전문가·관계자·개발자가 함께 쓰는 공통 언어**. 대화·문서·도메인 모델·코드·테스트에 일관되게 반영되어야 소통의 모호함이 줄고, 개발자가 도메인과 코드를 불필요하게 번역하지 않는다.

## 왜 중요한가
- 주문 상태를 `STEP1/STEP2`로 표현하면 각 단계 의미를 따로 알아야 하고 규칙(`verifyStep1OrStep2`)이 드러나지 않는다.
- `PAYMENT_WAITING`, `PREPARING`, `SHIPPED`처럼 도메인 용어를 쓰면 **코드가 곧 도메인 의미를 표현**해 가독성이 오르고 버그가 준다.
- 도메인 이해가 깊어지면 더 적절한 용어를 찾게 되고, 새 용어는 코드·문서에도 반영해 산출물을 최신 모델로 유지한다.

## 문맥 의존성
같은 용어도 하위 도메인마다 의미가 다르다("상품" = 카탈로그의 정보 / 배송의 물리적 물건 / 재고의 추적 대상). 이 통찰이 [바운디드 컨텍스트](bounded-context.md)로 이어진다. 국내 개발자는 도메인 용어의 적절한 영어 표현을 찾는 노력을 아끼지 말아야 한다(`gubun` 같은 발음 표기 지양).

## 등장
[DDD Start #1](../sources/ddd-domain-model-basics.md), [#9](../sources/ddd-bounded-context.md) · 토픽 [도메인 모델 구성요소](../topics/ddd-domain-model-building-blocks.md)
