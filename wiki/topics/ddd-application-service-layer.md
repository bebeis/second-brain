---
title: 응용 서비스 계층 — 흐름 제어·검증·권한·트랜잭션
type: topic
sources: [ddd-architecture-overview, ddd-application-and-presentation, ddd-domain-service]
updated: 2026-07-10
status: draft
---

# 응용 서비스 계층

응용 영역은 사용자 요청과 도메인을 잇는 창구다. 2장이 위치를, 6장이 책임과 구현 지침을, 7장이 도메인 서비스와의 경계를 정한다.

## 핵심 원칙: 흐름만 제어, 도메인 로직은 도메인에
응용 서비스는 **도메인 로직을 직접 구현하지 않고** 도메인 객체를 조합해 실행 흐름만 제어한다. 전형적 흐름:
- 기존 애그리거트 사용: 리포지터리 조회 → (없으면 예외) → 도메인 기능 실행 → 결과 반환
- 새 애그리거트 생성: 입력 검증 → 생성 → 저장 → 결과 반환

응용 서비스가 복잡하다면 도메인 로직이 새어 들어왔을 가능성이 높다. 암호 변경에서 "기존 암호 일치 확인"은 도메인 규칙이므로 애그리거트가 제공하고 응용 서비스는 호출만 한다. 로직을 도메인에 모으면 중복이 줄고 응집도가 오른다. ([#6](../sources/ddd-application-and-presentation.md))

## 표현 영역에 의존하지 않기
파라미터·리턴 타입에 `HttpServletRequest`·`HttpSession` 같은 표현 기술을 쓰지 않는다. 그러면 단독 테스트가 어렵고, 표현 구현 변경이 전파되며, 응용 서비스가 표현 역할까지 떠맡게 된다. 애그리거트 객체 자체를 리턴하는 것도 권장되지 않는다(표현 영역이 도메인 기능을 실행할 여지). **필요한 데이터만** 리턴한다. ([#6](../sources/ddd-application-and-presentation.md))

## 서비스 크기와 인터페이스
- 저자는 한 클래스에 도메인의 모든 기능을 몰아넣기보다 **기능별로 서비스를 나누는 방식을 선호**한다(필요한 의존만 갖고 품질 유지 쉬움). 중복 로직은 공통 헬퍼로.
- 응용 서비스 **인터페이스는 대개 불필요**(구현 교체가 드묾). 명확한 이유 없이 `...Service`/`...ServiceImpl`을 나누면 파일만 는다(YAGNI). ([#6](../sources/ddd-application-and-presentation.md))
- > 사용자 주석: 중복 로직은 Service 아래 Implement Layer(제민님 아키텍처)로 재사용성을 높일 수 있다 — 책 밖 해석.

## 트랜잭션은 응용 서비스의 책임
상태 변경은 트랜잭션으로 묶는다. 스프링 `@Transactional`은 `RuntimeException` 시 롤백·아니면 커밋. 도메인 서비스는 도메인 로직만 하고 **트랜잭션 같은 응용 로직은 응용 서비스가** 처리한다 → [도메인 서비스](../entities/domain-service.md). ([#6](../sources/ddd-application-and-presentation.md), [#7](../sources/ddd-domain-service.md))

## 값 검증
원칙적으로 응용 서비스에서 검증하되, 표현 영역은 오류를 모아 사용자에게 한 번에 보여줘야 한다. 오류마다 예외를 던지면 (1) 컨트롤러 catch가 번잡하고 (2) 첫 오류에서 멈춰 UX가 나쁘다. 해법: 오류를 **목록으로 모아 단일 예외**로 던져 `BindingResult`에 담거나, 표현 영역이 필수/형식/범위를, 응용 서비스가 존재/중복 같은 논리 검증을 나눠 맡는다. 저자는 근래 **응용 서비스에서 형식·논리 검증을 모두 처리하는 편**. ([#6](../sources/ddd-application-and-presentation.md))

## 권한 검사 — 세 지점
1. **표현 영역**: 주로 인증 여부. 서블릿 필터(스프링 시큐리티도 필터)에서 URL 접근 제어.
2. **응용 서비스**: URL만으로 부족하면 메서드 단위. 스프링 시큐리티 AOP `@PreAuthorize("hasRole('ADMIN')")`.
3. **도메인 객체 수준**: 작성자 본인 확인 등은 애그리거트를 조회한 뒤 권한 검사 서비스 호출.

프레임워크 이해가 부족하면 무리한 확장보다 도메인에 맞는 직접 구현이 유지보수에 유리할 수 있다. ([#6](../sources/ddd-application-and-presentation.md))

## 조회 전용 기능엔 응용 서비스가 없어도 된다
조회 전용 DAO를 호출만 하고 추가 로직·트랜잭션이 없다면, 응용 서비스를 만들지 말고 **표현 영역에서 DAO/리포지터리를 바로** 써도 된다 → [CQRS와 조회 모델](ddd-cqrs-and-read-model.md). ([#6](../sources/ddd-application-and-presentation.md))

## 관련 토픽
- [계층형 아키텍처와 DIP](ddd-layered-architecture-and-dip.md)
- [도메인 모델 구성요소](ddd-domain-model-building-blocks.md)
- [CQRS와 조회 모델](ddd-cqrs-and-read-model.md)
