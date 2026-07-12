# Second Brain — LLM Wiki

개인 학습 기록을 **LLM Wiki 패턴**([Andrej Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f))으로 관리하는 저장소.
읽은 자료를 매번 다시 찾는 대신, LLM이 원본을 정리해 **누적되는 지식층**으로 유지한다.

## 구조

```text
.
├── CLAUDE.md          # 스키마: LLM이 따르는 관례·워크플로 (핵심 설정)
├── index.md           # 콘텐츠 카탈로그 (전체 페이지 목록 + 한 줄 요약)
├── log.md             # 활동 로그 (ingest/lint 기록, append-only)
├── raw/               # 원본 보존 — 내가 소유한 자료만 전체 복사
└── wiki/
    ├── sources/       # 원문 1개 = 정리 페이지 1개
    ├── topics/        # 소스 여러 개를 묶은 해석 (학습의 핵심)
    └── entities/      # 인물·도구·개념 고정 페이지
```

## 사용법

- **새 자료 넣기**: LLM에게 "이 소스 ingest해줘"라고 요청 → `CLAUDE.md`의 Ingest 워크플로대로 처리.
- **찾기**: `index.md`에서 카탈로그 탐색, `wiki/topics/`에서 종합된 해석 확인.
- **점검**: 주기적으로 "lint 돌려줘" → 끊긴 근거·낡은 정리·중복 topic 정리.

## 규칙 요약

- 원문(`raw/`)과 해석(`wiki/`)을 분리한다.
- source page는 `Summary / Key Claims / Caveats / Related Topics` 4섹션. 불확실한 내용은 Caveats에 ⚠️.
- **저작권**: 외부 저작물은 `raw/`에 복사하지 않고 링크+발췌만. 소유 자료만 전체 복사.

자세한 규약은 [`CLAUDE.md`](./CLAUDE.md) 참고.
