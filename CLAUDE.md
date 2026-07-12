# CLAUDE.md — Second Brain Wiki Schema

이 저장소는 **LLM Wiki 패턴**(Andrej Karpathy)을 개인 학습 기록에 적용한 것이다.
LLM(에이전트)은 이 파일의 규칙에 따라 소스를 ingest하고, 질문에 답하고, 위키를 유지한다.
아래는 범용 챗봇이 아니라 **규율 있는 위키 관리자**로 동작하기 위한 설정이다.

---

## 3계층 구조

| Layer | 경로 | 역할 |
|---|---|---|
| raw source | `raw/` | 원본 보존 (**내가 소유한 것만 전체 복사**) |
| maintained wiki | `wiki/sources/`, `wiki/topics/`, `wiki/entities/` | LLM이 만든 정리·해석층 |
| schema | `CLAUDE.md` (이 파일) | 관례와 워크플로 |

네비게이션: `index.md`(콘텐츠 카탈로그), `log.md`(append-only 활동 로그).

## 페이지 종류

- **source page** (`wiki/sources/`): 원문 **1개**를 읽고 정리. 파일 하나 = 소스 하나.
- **topic page** (`wiki/topics/`): 소스 **여러 개**를 묶어 해석. 학습의 핵심 자산.
- **entity page** (`wiki/entities/`): 반복 등장하는 인물·도구·개념의 고정 페이지.

## raw 소유권 정책 (중요)

- **소유 자료만 `raw/`에 전체 복사**한다: 내가 쓴 코드·노트·강의 내 필기, 재배포 허용된 공개 자료(라이선스 명시).
- **외부 저작물(기사·유료 자료·타인 gist 등)은 raw에 복사하지 않는다.** source page에 **URL 링크 + 핵심 발췌(짧게)** 만 남긴다.
- source page frontmatter의 `raw_copy`로 상태를 표기한다: 경로 or `none (external)`.

## 파일 명명 규칙

- 전부 **kebab-case**, 소문자, 영문·숫자·하이픈. 예: `llm-wiki-pattern.md`.
- source/topic/entity 파일명은 내용을 나타내는 slug. 날짜는 파일명에 넣지 않고 frontmatter에 둔다.

## Frontmatter 규약

**source page**
```yaml
---
title: <제목>
type: source
source_url: <원문 URL 또는 비움>
source_owner: me | external
raw_copy: raw/<파일> | none (external)
ingested: YYYY-MM-DD
updated: YYYY-MM-DD
status: draft | reviewed
topics: [<topic-slug>, ...]
---
```

**topic page**
```yaml
---
title: <제목>
type: topic
sources: [<source-slug>, ...]
updated: YYYY-MM-DD
status: draft | reviewed
---
```

## 본문 섹션 규약

모든 source page는 반드시 4개 섹션으로 나눈다:
`## Summary` · `## Key Claims` · `## Caveats` · `## Related Topics`.
**확실하지 않은 내용은 Caveats에 ⚠️로 표시**한다. (원문 근거 없는 추정과 사실을 섞지 않는다.)

## 워크플로

### Ingest (새 소스 넣기)
1. 소유 여부 판단 → 소유면 `raw/`에 원본 복사, 외부면 링크만.
2. 사용자와 핵심 요점을 짧게 논의(원하면).
3. `wiki/sources/<slug>.md` 작성 (Summary/Key Claims/Caveats/Related Topics).
4. 관련 `wiki/topics/`·`wiki/entities/` 페이지 갱신 또는 신규 생성. (소스 하나가 보통 여러 페이지에 영향)
5. `index.md`에 항목 추가.
6. `log.md`에 한 줄 추가: `## [YYYY-MM-DD] ingest | <제목>`.

### Query (질문에 답하기)
- 먼저 `wiki/` 정리층을 읽고 **인용과 함께** 답한다. 필요할 때만 `raw/` 원문으로 내려간다.
- 재사용 가치가 있는 답변(비교·분석·발견한 연결)은 새 topic page로 편입한다.

### Lint (주기적 점검)
- source는 있는데 source page가 없는가 / topic이 얇거나 중복인가 / index에 링크가 빠졌는가 /
  오래된 정리가 방치됐는가 / 페이지 간 모순 / 근거(source_url·raw_copy) 끊김.
- 발견 사항은 `log.md`에 `## [YYYY-MM-DD] lint | ...`로 기록.

## 원칙
- 저장량보다 **재사용 가능한 정리층**이 목적이다.
- 원문과 해석을 섞지 않는다. 근거 없는 단정 대신 Caveats.
- 이 스키마는 도메인에 맞게 사용자와 함께 계속 진화시킨다.
