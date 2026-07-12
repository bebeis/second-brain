# Index

이 위키의 전체 콘텐츠 카탈로그. 페이지가 늘어나면 여기에 링크 + 한 줄 요약을 추가한다.

## Sources

### 지식 관리
- [LLM Wiki 패턴 (Karpathy)](wiki/sources/llm-wiki-pattern.md) — RAG 대안으로서 영속적·복리적으로 축적되는 지식 관리 패턴.

### DDD — 최범균 『도메인 주도 개발 시작하기』 (본인 필기)
- [#1 도메인 모델 시작하기](wiki/sources/ddd-domain-model-basics.md) — 도메인/하위 도메인, 도메인 모델 패턴, 엔티티·밸류, 유비쿼터스 언어.
- [#2 아키텍처 개요](wiki/sources/ddd-architecture-overview.md) — 네 영역, 계층 구조, DIP, 도메인 구성요소.
- [#3 애그리거트](wiki/sources/ddd-aggregate.md) — 경계·루트·일관성, ID 참조, 트랜잭션 범위, 팩토리.
- [#4 리포지터리와 모델 구현](wiki/sources/ddd-repository-and-model-mapping.md) — JPA 매핑, 로딩·영속성 전파, 식별자 생성, 도메인과 DIP의 타협.
- [#5 스프링 데이터 JPA 조회 기능](wiki/sources/ddd-spring-data-jpa-query.md) — (메타 노트) 조회 모델과 실무 관점 판단.
- [#6 응용 서비스와 표현 영역](wiki/sources/ddd-application-and-presentation.md) — 응용/표현 책임, 값 검증, 권한 검사, 트랜잭션.
- [#7 도메인 서비스](wiki/sources/ddd-domain-service.md) — 여러 애그리거트가 필요한 도메인 로직의 분리.
- [#8 애그리거트 트랜잭션과 잠금](wiki/sources/ddd-aggregate-transaction-and-locking.md) — 선점/비선점/오프라인 선점 잠금.
- [#9 도메인 모델과 바운디드 컨텍스트](wiki/sources/ddd-bounded-context.md) — 경계, 컨텍스트 관계, 통합, 컨텍스트 맵.
- [#10 이벤트](wiki/sources/ddd-domain-event.md) — 강결합 제거, 비동기 처리, 이벤트 저장소.
- [#11 CQRS](wiki/sources/ddd-cqrs.md) — 명령/조회 모델 분리, 저장소 동기화.

## Topics

- [애그리거트 설계](wiki/topics/ddd-aggregate-design.md) — 경계·루트·ID 참조·트랜잭션·잠금·팩토리 (#2·3·4·8).
- [계층형 아키텍처와 DIP](wiki/topics/ddd-layered-architecture-and-dip.md) — 네 영역, DIP, 실용적 타협 (#1·2·4).
- [도메인 모델 구성요소](wiki/topics/ddd-domain-model-building-blocks.md) — 엔티티·밸류·도메인 서비스·유비쿼터스 언어 (#1·2·7·9).
- [응용 서비스 계층](wiki/topics/ddd-application-service-layer.md) — 흐름 제어·검증·권한·트랜잭션 (#2·6·7).
- [바운디드 컨텍스트와 통합](wiki/topics/ddd-bounded-context-and-integration.md) — 경계, 관계, 직접/간접 통합 (#9·10).
- [도메인 이벤트](wiki/topics/ddd-domain-events.md) — 강결합 제거, 비동기, 트랜잭션 연계 (#3·8·10·11).
- [CQRS와 조회 모델](wiki/topics/ddd-cqrs-and-read-model.md) — 명령/조회 분리, 조회 성능 (#4·5·9·11).

## Entities

- [애그리거트 루트](wiki/entities/aggregate-root.md) — 애그리거트 일관성을 책임지는 대표 엔티티.
- [밸류 객체](wiki/entities/value-object.md) — 식별자 없는 불변 값 타입.
- [DIP (의존 역전 원칙)](wiki/entities/dip.md) — 고수준의 의도를 인터페이스로 추상화.
- [리포지터리](wiki/entities/repository-pattern.md) — 애그리거트 단위 저장·조회.
- [도메인 서비스](wiki/entities/domain-service.md) — 한 애그리거트에 담기 애매한 도메인 로직.
- [바운디드 컨텍스트](wiki/entities/bounded-context.md) — 모델이 완전한 의미를 갖는 경계.
- [유비쿼터스 언어](wiki/entities/ubiquitous-language.md) — 전문가·개발자 공통 언어.
