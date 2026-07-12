---
title: DDD Start #4 — 리포지터리와 모델 구현
type: source
source_url:
source_owner: me
raw_copy: raw/ddd-start/도메인 주도 개발 시작하기 #4 리포지터리와 모델 구현.md
ingested: 2026-07-10
updated: 2026-07-10
status: reviewed
topics: [ddd-aggregate-design, ddd-layered-architecture-and-dip, ddd-cqrs-and-read-model]
---

# DDD Start #4 — 리포지터리와 모델 구현

> 최범균 『도메인 주도 개발 시작하기』 4장 정리 노트 (본인 필기).

## Summary

리포지터리는 애그리거트를 저장소에 보관하고 다시 가져오는 역할을 하며, RDBMS 환경에서는 객체 모델과 관계형 모델을 매핑하기 좋은 ORM(자바 표준 JPA)을 기준으로 구현한다. 리포지터리 인터페이스는 애그리거트와 함께 도메인 영역에, 구현 클래스는 인프라스트럭처 영역에 두고, 인터페이스는 언제나 애그리거트 루트를 기준으로 작성한다. 기본 기능은 식별자 기반 조회와 저장 두 가지이며, JPA에서는 `EntityManager`의 `find()`·`persist()`로 구현하고 수정은 변경 감지에 맡긴다. 이 장은 애그리거트–테이블 매핑(밸류의 `@Embeddable`/`@Embedded`, `AttributeConverter`, 밸류 컬렉션, 밸류 타입 식별자), 애그리거트 로딩 전략(즉시/지연 로딩 선택), 영속성 전파(cascade·orphanRemoval), 식별자 생성 방식을 다룬다. 마지막으로 도메인 모델에 JPA 애너테이션을 붙이는 것이 DIP를 완벽히 지키지 못함을 인정하면서도, 구현 편의성과 복잡도를 고려한 **실용적 타협**을 선택한다.

## Key Claims

1. **리포지터리 인터페이스는 도메인, 구현은 인프라.** 구현 클래스를 `domain.impl`에 둘 수도 있으나, 인프라스트럭처에 두어 도메인의 인프라 의존을 낮추는 편이 낫다. 인터페이스는 애그리거트 루트(`Order`) 기준으로 작성한다.
2. **JPA 기본 구현.** `find()`로 식별자 조회, `persist()`로 저장한다. 수정용 별도 메서드는 대개 불필요한데, 트랜잭션 안에서 조회 후 상태를 바꾸면 커밋 시점 변경 감지로 `UPDATE`가 실행되기 때문이다. 조건 조회는 JPQL/Criteria, 삭제는 `EntityManager.remove()`로 하되 실무에서는 삭제 플래그로 노출 여부만 제어하기도 한다.
3. **밸류 매핑.** 한 테이블에 함께 저장되는 밸류 타입은 `@Embeddable`, 밸류 프로퍼티는 `@Embedded`. 칼럼 이름이 다르면 `@AttributeOverride(s)`. JPA가 요구하는 기본 생성자는 오용 방지를 위해 `protected`로 둔다. 공개 setter는 캡슐화를 약화하므로 도메인 모델은 필드 접근을 쓰는 편이 좋다.
4. **밸류를 한 칼럼에 저장**할 때는 `AttributeConverter`를 만든다. `@Converter(autoApply = true)`면 해당 타입에 자동 적용되고, 아니면 `@Convert`로 직접 지정한다.
5. **밸류 컬렉션.** 별도 테이블 저장은 `@ElementCollection`+`@CollectionTable`, `List` 순서 보존은 `@OrderColumn`. 한 칼럼에 저장할 때는 컬렉션을 별도 밸류 타입으로 감싸 `AttributeConverter`로 직렬화한다.
6. **밸류 타입 식별자.** `OrderNo`, `MemberId` 같은 타입은 `@Id` 대신 `@EmbeddedId`로 매핑한다. JPA 식별자 타입은 `Serializable`을 구현하고 `equals()`/`hashCode()`를 올바르게 구현해야 하며, 식별자 자체에 도메인 기능을 넣을 수 있다.
7. **별도 테이블 ≠ 엔티티.** 독립된 식별성이 없이 루트의 일부일 뿐이면 밸류다. `@SecondaryTable`+`@AttributeOverride`로 밸류를 별도 테이블에 매핑할 수 있으나, 조회 시 보조 테이블까지 조인하므로 목록 화면 등에는 조회 전용 모델이 낫다.
8. **JPA 제약으로 밸류를 `@Entity`로 매핑**해야 할 때가 있다(밸류 상속). `@Inheritance`·`@DiscriminatorColumn`·`@DiscriminatorValue`를 쓰되, 모델상 밸류라면 상태 변경 메서드는 제공하지 않는다. `@Entity` 컬렉션은 `@OneToMany`+`cascade`+`orphanRemoval`로 라이프사이클을 맞추지만, `clear()` 시 개별 조회·삭제로 성능 문제가 생길 수 있어 성능이 우선이면 `@Embeddable` 단일 타입에 타입 구분값을 쓰기도 한다.
9. **애그리거트 로딩 전략.** 루트를 조회하면 속한 객체가 필요한 상태여야 한다. 즉시 로딩은 편하지만 컬렉션이 여럿이면 카타시안 곱으로 행이 불어난다. 상태 변경은 트랜잭션 안 지연 로딩으로 충분한 경우가 많고 조회 화면은 조회 전용 모델이 적합하므로, 무조건 즉시/지연이 아니라 애그리거트 크기·실행 빈도·조회 방식·성능을 보고 선택한다.
10. **영속성 전파.** `@Embeddable` 밸류는 루트와 함께 저장·삭제돼 별도 설정이 불필요하다. `@Entity`로 매핑한 구성요소는 `@OneToOne`·`@OneToMany`에 `CascadeType.PERSIST`/`REMOVE`를 명시하고, 컬렉션에서 제거된 객체 삭제는 `orphanRemoval = true`.
11. **식별자 생성 3방식**: 사용자 직접 입력 / 도메인 규칙 / DB 일련번호. 생성 규칙이 도메인에 속하면 생성 기능도 도메인 영역에 두고(전담 도메인 서비스 또는 리포지터리 `nextId()`), 응용 서비스가 이를 얻어 엔티티를 만들어 저장한다. `@GeneratedValue`는 insert 이후에야 식별자를 알 수 있다.
12. **도메인 구현과 DIP는 실용적 타협.** 도메인 모델에 `@Entity`·`@Id` 등 JPA 애너테이션이 붙는 것은 엄밀히 DIP 위반이지만, 구현 기술은 자주 바뀌지 않고 애너테이션이 단위 테스트를 크게 방해하지 않으며 스프링 데이터 JPA 리포지터리도 인터페이스라 테스트 가능성을 유지한다. 따라서 편의성과 유연성의 균형을 택했다.

## Caveats

- ⚠️ raw 노트에는 매핑 코드·테이블 다이어그램 이미지가 다수 포함돼 있으나 이 정리에서는 생략했다. 실제 매핑 예시가 필요하면 원문 노트를 참조.
- ⚠️ 4.2절(스프링 데이터 JPA를 이용한 리포지터리 구현)은 원문 노트에서 "JPA 관련 내용이라 생략"으로 처리되어 있어 상세가 없다.
- ⚠️ "구현 기술은 실제로 자주 바뀌지 않는다"는 4.7절의 실용적 타협 논거는 책의 관점이며, 프로젝트 상황에 따라 판단이 달라질 수 있다.

## Related Topics

- [애그리거트 설계](../topics/ddd-aggregate-design.md) — 리포지터리는 애그리거트 단위로 만든다.
- [계층형 아키텍처와 DIP](../topics/ddd-layered-architecture-and-dip.md) — 리포지터리 인터페이스/구현 분리와 4.7절 DIP 타협.
- [CQRS와 조회 전용 모델](../topics/ddd-cqrs-and-read-model.md) — `@SecondaryTable`·즉시 로딩의 한계와 조회 전용 모델.
- [리포지터리 패턴](../entities/repository-pattern.md)
- [밸류 객체](../entities/value-object.md)
- [DIP](../entities/dip.md)
