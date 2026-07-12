---
title: 계층형 아키텍처와 DIP
type: topic
sources: [ddd-domain-model-basics, ddd-architecture-overview, ddd-repository-and-model-mapping]
updated: 2026-07-10
status: draft
---

# 계층형 아키텍처와 DIP

DDD 애플리케이션의 뼈대는 네 영역과 그 사이의 의존 방향이다. 1장이 도메인 모델 패턴을, 2장이 아키텍처와 DIP를, 4장이 그 이상과 현실의 타협을 다룬다.

## 네 개의 영역
| 영역 | 역할 |
|---|---|
| 표현(Presentation) | 요청 해석·응답 변환. HTTP 파라미터 ↔ 응용 서비스 객체 |
| 응용(Application) | 기능 실행 흐름 제어. 도메인 로직을 직접 구현하지 않고 도메인 객체를 조합 |
| 도메인(Domain) | 핵심 규칙·모델. `Order.cancel()` 같은 로직이 여기 |
| 인프라스트럭처(Infrastructure) | 구현 기술 연동 (RDBMS, 메시징, SMTP, REST) |

([#2](../sources/ddd-architecture-overview.md))

## 도메인 모델 패턴: 규칙을 도메인 객체 안에
계층 이전에 더 근본적인 원칙 — **업무 규칙은 도메인 모델 안에 있어야 한다**. "출고 전에는 배송지 변경 가능" 같은 규칙을 `Order`/`OrderState`에 두면 규칙이 바뀔 때 변경 범위가 줄고 도메인 지식이 코드에 반영된다. 개념 모델은 완벽할 수 없으므로 윤곽부터 만들고 점진적으로 발전시킨다. ([#1](../sources/ddd-domain-model-basics.md))

## 계층 구조의 문제와 DIP
순수 계층 구조에서는 도메인·응용이 인프라스트럭처의 **구체 기술에 종속**된다. 그러면 (1) 응용 서비스만 따로 테스트하기 어렵고 (2) 구현 기술을 바꾸기 어렵다.

**DIP(의존 역전 원칙)**가 이를 푼다 — [DIP](../entities/dip.md) 참조. 고수준 모듈(응용·도메인)의 **필요를 인터페이스로 추상화**하고, 저수준(인프라)이 그 인터페이스를 구현하게 한다. 그러면 의존 방향이 역전되어 인프라가 도메인/응용에 정의된 인터페이스에 의존한다.

핵심 주의: 인터페이스는 **저수준 기술("룰 엔진 실행")이 아니라 고수준 모듈의 의도("할인 금액 계산")에서 도출**하고, 위치도 고수준 쪽에 둔다. 대표 예가 리포지터리(인터페이스는 도메인, 구현은 인프라)와 알림. ([#2](../sources/ddd-architecture-overview.md))

## 이상과 현실: 실용적 타협
DIP를 항상 완벽히 지킬 필요는 없다. 2장·4장 모두 **구현 편의성도 가치**임을 강조한다:
- 응용 서비스에 `@Transactional`을 붙이는 것, 도메인 모델에 `@Entity`·`@Table`을 쓰는 것은 편리하고, 이 구현 기술은 **실제로 자주 바뀌지 않는다**(JPA→MyBatis, RDBMS→MongoDB 전환은 드묾).
- 애너테이션을 도메인에 써도 단위 테스트에 큰 지장이 없고, 스프링 데이터 JPA 리포지터리도 인터페이스라 테스트 가능성을 크게 해치지 않는다.
- 의존을 완전히 없애려다 설정·코드가 지나치게 복잡해지면 개발 비용만 커진다.

4장은 이를 명시적으로 **"실용적 타협"**이라 부른다. ([#4](../sources/ddd-repository-and-model-mapping.md))

## 모듈 구성
각 영역은 별도 패키지(`ui`, `application`, `domain`, `infrastructure`)에 두고, 도메인이 크면 하위 도메인별로 나눈다. 도메인 모듈은 애그리거트 기준으로 나눌 수 있고, 한 애그리거트의 모델과 리포지터리는 같은 패키지에 둔다. 한 패키지의 타입이 너무 많으면(대략 10~15개 기준) 분리를 고려. ([#2](../sources/ddd-architecture-overview.md))

## 관련 토픽
- [도메인 모델 구성요소](ddd-domain-model-building-blocks.md)
- [애그리거트 설계](ddd-aggregate-design.md)
- [응용 서비스 계층](ddd-application-service-layer.md)
