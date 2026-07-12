---
title: 도메인 모델 구성요소 — 엔티티·밸류·도메인 서비스·유비쿼터스 언어
type: topic
sources: [ddd-domain-model-basics, ddd-architecture-overview, ddd-domain-service, ddd-bounded-context]
updated: 2026-07-10
status: draft
---

# 도메인 모델 구성요소

도메인 영역을 채우는 재료들과, 그것들에 이름을 붙이는 언어에 대한 정리. 1·2장이 엔티티/밸류를, 7장이 도메인 서비스를, 1·9장이 유비쿼터스 언어와 용어의 문맥 의존성을 다룬다.

## 엔티티 — 식별자로 구분
엔티티의 핵심은 **식별자**다. 배송지·상태가 바뀌어도 주문번호는 그대로이므로 **식별자가 같으면 같은 엔티티**다(보통 식별자 기준 equals/hashCode). 도메인 엔티티는 DB 엔티티와 달리 **데이터와 기능을 함께** 갖는다. 식별자 생성은 규칙/생성기(UUID)/사용자 입력/DB 시퀀스 등이 있다. ([#1](../sources/ddd-domain-model-basics.md))

## 밸류 — 개념적으로 완전한 하나
밸류 타입은 여러 필드를 개념 하나로 묶거나(Receiver, Address), 값 하나에 의미를 입힌다(Money). 원칙 — [밸류 객체](../entities/value-object.md) 참조:
- **불변으로 구현**: 값을 바꾸는 대신 새 객체로 교체. 방어적 복사가 줄고 공유로 인한 의도치 않은 변경을 막는다.
- **비교는 모든 속성 값으로** 판단(엔티티는 식별자로).
- **식별자도 밸류로**: `String` 대신 `OrderNo`, `MemberId`로 의미를 드러내고 식별자 자체에 도메인 기능을 담을 수 있다. ([#1](../sources/ddd-domain-model-basics.md), [#2](../sources/ddd-architecture-overview.md))

## set을 넣지 않기
습관적 public set은 도메인 의도를 흐리고 객체를 불완전 상태로 만든다. **생성 시점에 필수 값을 생성자로 모두 받고 제약을 검사**하며, 외부에서 상태를 바꾸는 건 `changeShippingInfo()`·`completePayment()`처럼 의미 있는 메서드로만 한다. 이 원칙은 [애그리거트 루트](../entities/aggregate-root.md)의 일관성 보장과 직결된다. ([#1](../sources/ddd-domain-model-basics.md))

## 도메인 서비스 — 한 애그리거트에 담기 애매한 로직
계산에 여러 애그리거트가 필요하거나(결제 금액 = 상품·쿠폰·회원등급) 외부 시스템 연동이 필요한 도메인 로직은 특정 애그리거트에 억지로 넣지 말고 [도메인 서비스](../entities/domain-service.md)로 분리해 명시화한다. 특징:
- **상태 없이 로직만**. 필요한 값은 파라미터로 받는다.
- 애그리거트에 **필드로 주입하지 않는다**(도메인 서비스는 애그리거트의 데이터가 아님). 메서드 파라미터로 전달하거나, 반대로 도메인 서비스에 애그리거트를 넘긴다(계좌 이체).
- 외부 연동은 "역할 관리 시스템과 연동"이 아니라 **"생성 권한을 확인한다"는 도메인 로직 관점**의 인터페이스로 표현하고, 구현은 인프라에 둔다.
- 응용 서비스와의 구분: 로직이 애그리거트 **상태를 변경/계산하는 도메인 로직**이면 도메인 서비스, 트랜잭션 등 **실행 흐름**이면 응용 서비스. ([#7](../sources/ddd-domain-service.md))

## 유비쿼터스 언어와 용어의 문맥 의존성
- **유비쿼터스 언어**: 전문가·관계자·개발자가 함께 쓰는 공통 언어를 대화·문서·모델·코드·테스트에 일관되게 반영한다 — [유비쿼터스 언어](../entities/ubiquitous-language.md). `STEP1/STEP2` 대신 `PAYMENT_WAITING/PREPARING`처럼 도메인 용어를 코드에 넣으면 번역이 줄고 버그가 준다. ([#1](../sources/ddd-domain-model-basics.md))
- **같은 용어, 다른 의미**: "상품"이 카탈로그에서는 판매 정보, 배송에서는 물리적 물건, 재고에서는 추적 대상이다. 그래서 **하위 도메인마다 별도 모델**을 만들어야 하고, 이 통찰이 곧 [바운디드 컨텍스트](ddd-bounded-context-and-integration.md)로 이어진다. ([#1](../sources/ddd-domain-model-basics.md), [#9](../sources/ddd-bounded-context.md))

## 관련 토픽
- [애그리거트 설계](ddd-aggregate-design.md)
- [계층형 아키텍처와 DIP](ddd-layered-architecture-and-dip.md)
- [바운디드 컨텍스트와 통합](ddd-bounded-context-and-integration.md)
