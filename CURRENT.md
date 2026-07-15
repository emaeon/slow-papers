# Current Reading State

Last updated: 2026-07-15

## Paper

TH-RAG: Topic-Based Hierarchical Knowledge Graphs for Robust Multi-hop Reasoning in Graph-based RAG Systems

## Goal

논문의 핵심 내용을 이해하면서 영어 논문 독해 속도와 독립적인 해석 능력을 함께 기른다.

## Completed

- Abstract
- Introduction
- Method 3.1: Hierarchical Graph Construction
- Method 3.2: Topic-based Graph Traversal
- Method 3.3: Query-based Retrieval
- Method 3.4: Multi-hop Reasoning Robustness
- Experiments의 RQ1–RQ4

## Next

Section 4.1 Datasets부터 이어서 읽는다.

다음 문단에서는 아래 세 가지를 파악한다.

1. Abstractive QA가 요구하는 답변 유형
2. Specific QA가 요구하는 멀티홉 근거
3. 각 유형에 사용한 데이터셋

## Current passage

> In our experiments, we used two types of datasets. One is an abstractive QA dataset, such as UltraDomain, which requires answering open-ended questions based on broad knowledge. Following prior studies, we used three domain-specific datasets (Agriculture, CS, and Legal) and one mixed-domain corpus, from which we generated 125 questions. The other is a specific QA dataset, HotpotQA and MultiHop-RAG, which has concrete multi-hop evidence that must be retrieved to generate answers.

## Important Method Findings

- Indexing 시 청크마다 LLM 호출 한 번으로 Triplet, Subtopic, Topic을 동시에 추출한다.
- 실험 설정의 chunk size는 1,200 tokens, overlap은 100 tokens다.
- 각 Entity–Entity 엣지는 트리플이 추출된 원문 문장을 속성으로 보존한다.
- Query 시 LLM은 Topic과 Subtopic을 선택한다.
- Entity는 LLM이 별도로 선택하지 않고, 선택된 Subtopic 아래에서 모두 수집한다.
- 본문의 “Topic, Subtopic, Entity 후보를 LLM에 제공한다”는 표현은 Algorithm 2 및 부록의 실제 Subtopic prompt와 다소 일관되지 않는다.
- 각 Entity의 1-hop 엣지에서 원문 근거 문장을 수집한다.
- cosine similarity로 Top-`K1` 문장을 선택하고, Top-`K2` 문장이 포함된 원본 chunk를 추가한다.
- 기본 설정은 `K1=30`, `K2=5`다.
- TH-RAG는 관계 경로만으로 추론하기보다 계층 구조로 검색 공간을 좁히고 원문 근거를 필터링하는 hybrid retrieval 방식에 가깝다.

## Recurring Reading Difficulties

- 동사의 사전적 뜻은 알아도 목적어·전치사·to부정사가 포함된 전체 프레임의 인출이 느리다.
- 긴 문장에서 메인 절과 관계절의 주어를 혼동할 때가 있다.
- `which`가 바로 앞 명사를 받는지 앞 상황 전체를 받는지 판단하는 연습이 필요하다.
- 긴 명사구는 영어 어순대로 옮기지 말고 중심 명사부터 조립해야 한다.
- `yield`, `exhibit`, `locate`처럼 일상적 첫 번째 뜻과 학술 문맥의 뜻이 다른 동사가 자주 막힌다.

## Files

- `papers/th-rag/notes.md`: 논문 내용과 비판적 질문
- `papers/th-rag/reading-companion.md`: 동사 프레임, 어휘, 독해 패턴, 날짜별 기록
- `papers/th-rag/paper.pdf`: 로컬 PDF이며 Git에서 제외됨

## Resume Prompt

새 Codex 세션에서 다음과 같이 요청한다.

```text
AGENTS.md와 CURRENT.md를 먼저 읽어줘.
TH-RAG 논문을 영어 독해 훈련과 함께 읽고 있다.
CURRENT.md의 Next와 Current passage부터 기존 방식대로 이어가자.
```
