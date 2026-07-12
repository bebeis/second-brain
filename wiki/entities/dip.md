---
title: DIP (의존 역전 원칙)
type: entity
updated: 2026-07-10
status: draft
---

# DIP (Dependency Inversion Principle, 의존 역전 원칙)

고수준 모듈이 저수준 구현에 직접 의존하지 않게 만드는 원칙. 고수준 모듈(응용·도메인)의 **필요를 인터페이스로 추상화**하고, 저수준(인프라)이 그 인터페이스를 구현하게 해 **의존 방향을 역전**시킨다.

## 장점
1. 구현 기술을 바꿔도 고수준 모듈을 수정하지 않는다.
2. 실제 구현 없이 대역 객체로 고수준 모듈을 테스트할 수 있다.

## 올바른 적용 (핵심 주의)
- 인터페이스는 **저수준 기술 관점("룰 엔진 실행")이 아니라 고수준 모듈의 의도("할인 금액 계산")**에서 도출한다.
- 인터페이스 위치도 고수준 모듈 쪽에 둔다. 인프라에 인터페이스를 두고 도메인이 그것에 의존하면 여전히 잘못된 방향이다.
- 대표 예: [리포지터리](repository-pattern.md)(인터페이스=도메인, 구현=인프라), 알림, [도메인 서비스](domain-service.md)의 외부 연동 구현.

## 실용적 타협
항상 완벽히 지킬 필요는 없다. 도메인 모델의 `@Entity`·`@Transactional`처럼 자주 바뀌지 않는 기술 의존은 편의를 위해 허용할 수 있다. 의존 제거가 복잡도만 키우면 이점을 얻는 수준에서 범위를 조절한다.

## 등장
[DDD Start #2](../sources/ddd-architecture-overview.md), [#4](../sources/ddd-repository-and-model-mapping.md), [#7](../sources/ddd-domain-service.md) · 토픽 [계층형 아키텍처와 DIP](../topics/ddd-layered-architecture-and-dip.md)
