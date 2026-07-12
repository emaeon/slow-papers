# slow-papers

천천히 읽더라도 연구의 논리와 영어 표현을 정확히 이해하기 위한 개인 논문 읽기 기록입니다.

각 논문은 다음 두 종류의 노트로 정리합니다.

- `notes.md`: 논문의 문제의식, 방법, 실험 결과, 비판적 질문
- `reading-companion.md`: 독해를 막은 어휘, 동사 프레임, 문장 구조, 일일 학습 기록

## Papers

| Paper | Venue | Status | Notes |
|---|---|---|---|
| TH-RAG: Topic-Based Hierarchical Knowledge Graphs for Robust Multi-hop Reasoning in Graph-based RAG Systems | ACL 2026 | Reading | [Paper notes](papers/th-rag/notes.md) · [Reading companion](papers/th-rag/reading-companion.md) |

## Reading workflow

1. Abstract와 Introduction에서 문제, 기존 한계, 제안 방법을 찾습니다.
2. 핵심 방법과 결과는 정독하고, 반복되는 배경과 인용은 빠르게 읽습니다.
3. 문장을 완전히 번역하기 전에 `주체 → 행동 → 대상 → 방법/이유/결과`로 의미를 스케치합니다.
4. 새 어휘는 단어 하나가 아니라 `동사 + 목적어 + 전치사` 프레임으로 기록합니다.
5. 결과표, ablation, limitations를 확인하며 저자의 주장을 비판적으로 검토합니다.

## Repository structure

```text
slow-papers/
├── README.md
├── papers/
│   └── th-rag/
│       ├── notes.md
│       └── reading-companion.md
└── templates/
    ├── paper-notes.md
    └── reading-companion.md
```

논문 PDF는 로컬에서만 관리하며 저장소에는 커밋하지 않습니다.

