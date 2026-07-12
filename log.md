# Log

위키 활동의 시간순 append-only 기록. 각 항목은 `## [YYYY-MM-DD] <op> | <제목>` 형식.

## [2026-07-12] edit | raw/ddd-start 정리글화 + 이미지 감축
- raw 노트 11개를 책 구성(절 번호·`#N` 접두사·태그)에서 벗어나 주제별 정리글로 재구성. 내용·용어·본인 주석은 보존.
- 책 그림/코드 스크린샷 PNG 절반 삭제(154→71, 12M→5.3M). 남은 이미지 참조는 전부 유효, 파일명 유지로 wiki `raw_copy` 링크 보존.
- git 히스토리를 단일 커밋으로 재정리(기존 커밋 로그 제거).

## [2026-07-10] ingest | DDD Start — 최범균 『도메인 주도 개발 시작하기』 1~11장
- raw `raw/ddd-start/` (본인 필기, 소유 자료) → source page 11개 생성 (`wiki/sources/ddd-*.md`, `source_owner: me`).
  - #1 도메인 모델 · #2 아키텍처 · #3 애그리거트 · #4 리포지터리/매핑 · #5 조회(메타 노트) · #6 응용/표현 · #7 도메인 서비스 · #8 트랜잭션/잠금 · #9 바운디드 컨텍스트 · #10 이벤트 · #11 CQRS.
- topic page 7개 신규: 애그리거트 설계 / 계층 아키텍처·DIP / 도메인 모델 구성요소 / 응용 서비스 계층 / 바운디드 컨텍스트·통합 / 도메인 이벤트 / CQRS·조회 모델.
- entity page 7개 신규: 애그리거트 루트 · 밸류 객체 · DIP · 리포지터리 · 도메인 서비스 · 바운디드 컨텍스트 · 유비쿼터스 언어.
- `index.md`에 DDD 섹션(sources·topics·entities) 추가. 링크 무결성 검사 통과(내부 .md 링크 전부 resolve).
- 작성 방식: 소스 페이지 11개는 fork 3개로 병렬 작성, 토픽·엔티티·네비게이션은 메인에서 종합.

## [2026-07-09] ingest | LLM Wiki 패턴 (Karpathy)
- `wiki/sources/llm-wiki-pattern.md` 생성 (Summary/Key Claims/Caveats/Related Topics).
- 외부 소유물이라 raw 전체 복사 없음 — source_url 링크로 유지.

## [2026-07-09] setup | 위키 구조 초기화
- CLAUDE.md(schema), README.md, index.md, log.md 생성.
- `raw/`, `wiki/{sources,topics,entities}/` 구조 구축. raw 정책: 소유 자료만 전체 복사.
