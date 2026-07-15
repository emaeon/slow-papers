# TH-RAG Reading Notes

> Paper: _TH-RAG: Topic-Based Hierarchical Knowledge Graphs for Robust Multi-hop Reasoning in Graph-based RAG Systems_
>
> 목표: 논문의 핵심 내용을 이해하면서 영어 논문 독해 속도와 독립적인 해석 능력을 기른다.

## 오늘 다시 볼 곳

- [ ] 새로 발견한 동사를 뜻이 아니라 **프레임 전체**로 기록하기
- [ ] 긴 문장에서 가장 바깥쪽 주어·동사부터 찾기
- [ ] `which`, `where`, `-ing`의 의미상 주체 확인하기
- [ ] 영어 어순을 그대로 옮기지 않고 10초 의미 스케치 만들기
- [ ] 오늘 읽은 범위와 핵심 주장 1~3줄 남기기

---

## 1. Paper Map

### 논문이 해결하려는 문제

LLM이 문서에서 추출한 트리플 기반 지식 그래프는 흔히 파편화되어 있다. 엔터티 사이의 핵심 관계 경로가 누락되거나 끊어지면, 모델은 여러 곳에 흩어진 정보를 통합하지 못하고 정확하면서 문맥에 근거한 답변을 생성하기 어려워진다.

기존 연구는 클러스터링 기반 요약, 커뮤니티 탐지, HNSW 같은 그래프 보강 기법으로 연결성을 복구하려 했다. 그러나 이러한 방법은 상당한 추가 계산 부담을 발생시키거나 세부적인 의미 관계를 왜곡할 수 있으며, 검색된 정보의 정확성과 신뢰성을 저해할 위험이 있다.

### 핵심 아이디어 한 줄

TH-RAG는 `Topic → Subtopic → Entity/Triplet`의 3계층 지식 그래프를 구축하고, 질문과 관련된 토픽부터 하향식으로 탐색해 파편화된 정보에서도 근거를 안정적으로 회수한다.

### 전체 파이프라인

1. **Hierarchical KG Construction**
   - LLM이 각 문서 청크에서 Triplet, Subtopic, Topic을 동시에 추출한다.
   - 이들을 연결해 의미적으로 구조화된 3계층 지식 그래프를 만든다.
2. **Topic-based Graph Traversal**
   - 질문과 가장 관련성 높은 Topic 노드에서 시작한다.
   - 관련 Subtopic과 Entity를 재귀적으로 탐색해 후보 Triplet을 수집한다.
3. **Query-based Retrieval & Filtering**
   - 질문과 후보 트리플 엣지 사이의 cosine similarity를 계산한다.
   - 관련성 높은 정보를 답변 생성용 최종 context로 선택한다.

### Introduction 논리

1. 파인튜닝으로 LLM에 새 지식을 추가하면 계산 비용, 시간 소모, 파국적 망각 문제가 발생한다.
2. RAG가 대안이지만 일반 검색은 흩어진 정보에 대한 멀티홉 추론에 약하다.
3. GraphRAG는 지식 그래프를 사용하지만, LLM이 만든 트리플 그래프는 파편화되기 쉽다.
4. 기존 그래프 보강 방법은 계산 비용과 의미 왜곡 문제가 있다.
5. TH-RAG는 Topic–Subtopic–Triplet 계층으로 연결성을 보강한다.
6. 관련 Topic부터 내려가며 후보 근거를 찾고, 질문과의 유사도로 최종 문맥을 선택한다.
7. 저자들은 이것이 정확성, 안정성, 효율성, 확장성을 함께 개선한다고 주장한다.

### 현재까지 확인한 실험 주장

- 개방형 생성 QA인 UltraDomain과 구체적인 멀티홉 QA인 MultiHop-RAG 및 HotpotQA에서 평가했다.
- 저자들은 TH-RAG가 기존 graph-based RAG 방법보다 우수하다고 주장한다.
- Ablation 결과는 계층적 그래프 설계와 검색 전략이 성능 향상에 기여한다고 보고한다.
- 결과표와 평가 설계의 타당성은 이후 직접 검토한다.

---

## 2. Verb Frames

동사는 단독 뜻이 아니라 **슬롯을 포함한 설계도**로 익힌다.

| Frame                           | 의미                              | 역할/주의점                            | 논문 예시 또는 의미 스케치                     |
| ------------------------------- | --------------------------------- | -------------------------------------- | ---------------------------------------------- |
| `enable A to do B`              | A가 B할 수 있게 하다              | 실제로 B를 하는 주체는 A               | RAG → LLM → 외부 지식 통합                     |
| `integrate A into B`            | A를 B에 통합하다                  | `into B`는 통합되는 대상               | new knowledge → LLMs                           |
| `organize A into B`             | A를 B의 형태로 조직하다           | `into B`는 결과 구조                   | triplets → topics/subtopics                    |
| `structure A as B`              | A를 B 형태로 구조화하다           | `as B`는 결과 형태                     | retrieval DB → KG                              |
| `extend A by doing B`           | B함으로써 A를 확장하다            | `by -ing`는 방법                       | KG로 DB를 구조화하여 RAG 확장                  |
| `improve A by doing B`          | B함으로써 A를 개선하다            | `by -ing`는 방법                       | 정보를 의미 수준에서 구조화 → 추론 정확성 향상 |
| `construct A from B by doing C` | C하여 B로부터 A를 구축하다        | A=결과, B=재료, C=방법                 | 말뭉치에서 트리플을 추출해 KG 구축             |
| `leverage A to do B`            | B하기 위해 A를 활용하다           | `to do B`는 목적                       | retriever 활용 → 관련 정보 검색                |
| `fetch A from B`                | B에서 A를 가져오다                | A=검색 정보, B=출처                    | external corpora에서 relevant information 검색 |
| `generate A based on B`         | B를 바탕으로 A를 생성하다         | `based on B`는 근거                    | retrieved content → response                   |
| `outperform A in B`             | B 측면에서 A를 능가하다           | A=비교 대상, B=평가 기준               | baselines를 accuracy에서 능가                  |
| `provide A for B`               | B를 위한 A를 제공하다             | A=제공물, B=용도/수혜자                | GraphRAG systems를 위한 foundation             |
| `provide A with B`              | A에게 B를 제공하다                | 위 패턴과 어순이 다름                  | model에게 evidence 제공                        |
| `facilitate A by doing B`       | B함으로써 A를 용이하게 하다       | 단순한 향상보다 ‘쉽게 함’              | 구조와 관계 포착 → 문서 이해 촉진              |
| `prevent A from doing B`        | A가 B하지 못하게 막다             | 실제 B의 주체는 A                      | fragmentation이 model의 reasoning을 방해       |
| `prevent A`                     | A를 막거나 방해하다               | 목적어 하나가 바로 옴                  | prevent multi-hop reasoning                    |
| `struggle to do A`              | A하는 데 어려움을 겪다            | 목적보다 어려운 행동                   | integrate dispersed information에 어려움       |
| `hinder one's ability to do A`  | ~가 A하는 능력을 저해하다         | `one's`가 능력의 소유자                | model의 정확한 답변 생성 능력 저해             |
| `focus on doing A`              | A하는 데 초점을 맞추다            | `on` 뒤는 명사/동명사                  | KG를 직접 구축하는 데 집중                     |
| `lead to A`                     | A로 이어지다                      | 앞 내용이 원인, A가 결과               | fragmentation → isolated subgraphs             |
| `suffer from A`                 | A라는 문제를 겪다                 | A에는 주로 부정적 내용                 | suffer from drawbacks                          |
| `incur A`                       | 비용·손실 A를 초래하거나 부담하다 | 목적어가 주로 부정적                   | incur computational cost                       |
| `introduce the risk of A`       | A의 위험을 발생시키다             | `introduce`는 여기서 ‘소개하다’가 아님 | catastrophic forgetting의 위험 발생            |
| `restore A`                     | A를 복구하다                      | `store`와 분리해 생각하지 않기         | restore connectivity                           |
| `be composed of A`              | A로 구성되다                      | 수동형 고정 결합                       | KG composed of Triplets, Subtopics, Topics     |
| `compute A between B and C`     | B와 C 사이의 A를 계산하다         | A=측정값                               | query와 edge 사이 cosine similarity 계산       |
| `select A as B`                 | A를 B로 선택하다                  | B=선택된 역할                          | information을 final context로 선택             |
| `show that S + V`               | S가 V한다는 것을 보여주다         | `that` 뒤는 완전한 주장                | results show that TH-RAG outperforms baselines |
| `assume that S + V`             | S가 V한다고 가정하다              | `that` 뒤는 완전한 가정                | connectivity exists라고 가정                   |

### 병렬 동사의 주어 찾기

> RAG **leverages** retrieval mechanisms to fetch information and **generates** responses.

- `leverages`와 `generates`의 주어: `RAG`
- `to fetch`의 의미상 주어도 `RAG`
- 같은 시제·형태의 동사를 먼저 짝지어 본다: `leverages ↔ generates`

---

## 3. Logical Connectors & Clause Patterns

| Pattern                      | 의미                  | 논리 방향/판별법                                                 |
| ---------------------------- | --------------------- | ---------------------------------------------------------------- |
| `owing to + 명사`            | ~ 때문에, ~ 덕분에    | 뒤가 원인, 앞이 결과. `because of`로 교체 가능                   |
| `because of / due to + 명사` | ~ 때문에              | 뒤가 원인                                                        |
| `by doing A`                 | A함으로써             | 방법                                                             |
| `to do A`                    | A하기 위해            | 보통 목적. 단, `struggle to do`처럼 동사 프레임의 일부일 수 있음 |
| `leading to A`               | 그 결과 A로 이어져    | 앞이 원인, A가 결과                                              |
| `As a result`                | 그 결과               | 앞 내용의 결과를 도입                                            |
| `since S + V`                | S가 V하기 때문에      | 문맥에 따라 시간도 가능하지만 이 논문에서는 이유                 |
| `not only A but also B`      | A뿐 아니라 B도        | A와 B가 같은 문법 형태로 병렬 연결                               |
| `compared to A`              | A와 비교하면          | 비교 기준을 표시                                                 |
| `Beyond A`                   | A 외에도, A를 넘어서  | 추가 효과를 도입                                                 |
| `while doing A`              | A하면서도             | 동시 행동 또는 대조                                              |
| `where S + V`                | 그 단계·환경·과정에서 | 장소가 아니어도 사용. `in which`로 교체 가능                     |
| `which S + V`                | 앞 명사/상황을 설명   | 무엇을 받는지 의미로 확인                                        |
| `be the case`                | 실제로 그러하다       | `rarely the case` = 실제로 그런 경우가 거의 없다                 |

### `which` 판별

명사를 받는 경우:

> Topic traversal, **which begins from Topic nodes**, ...

`which = Topic traversal`

앞 상황 전체를 받는 경우:

> Graphs are fragmented, **which hinders reasoning**.

`which = 그래프가 파편화된 상황`

### 쉼표 뒤 `-ing` 판별

쉼표 뒤 `-ing`가 항상 “그 결과”인 것은 아니다.

1. 고정 표현인가? `owing to` 등
2. `by -ing`인가? 대체로 방법
3. 앞 문장의 주체가 `-ing` 행동도 하는가? 동시 행동/부연 가능
4. 앞 사건 때문에 `-ing`가 발생하는가? 결과 가능
5. 바로 앞 명사가 아니라 앞 상황 전체가 의미상 주체일 수도 있음

예:

> The model struggles to integrate dispersed information, ultimately **hindering** its ability to generate accurate answers.

`hindering`의 원인은 `information`이 아니라 **모델이 정보를 통합하지 못하는 앞 상황 전체**다.

### 수동태 빠르게 읽기

행위자가 자명하고 처리 대상·절차가 중요한 경우 수동태를 쓴다.

> Cosine similarity **is computed** between the query and each edge.

머릿속에서 능동태로 복원:

> The system computes cosine similarity between the query and each edge.

---

## 4. Vocabulary That Blocked Comprehension

| 표현                      | 이 논문에서의 의미                      | 헷갈렸던 의미/주의점                                 | 함께 기억할 표현                |
| ------------------------- | --------------------------------------- | ---------------------------------------------------- | ------------------------------- |
| `corpus / corpora`        | 말뭉치, 문서 집합                       | 용어 집합이 아님                                     | external corpora                |
| `fragmented`              | 파편화된                                | 물리적으로 깨진 것보다 그래프가 여러 부분으로 분리됨 | fragmented graph                |
| `coherence`               | 응집성, 일관성                          | —                                                    | semantic coherence              |
| `hinder`                  | 저해하다, 방해하다                      | —                                                    | hinder reasoning                |
| `restore`                 | 복구하다                                | 재저장하다가 아님                                    | restore connectivity            |
| `approximate`             | 근사적인                                | `abstract`(추상적인)와 구분                          | approximate graph algorithm     |
| `distortion`              | 왜곡                                    | —                                                    | semantic distortion             |
| `dispersed`               | 흩어진, 분산된                          | —                                                    | dispersed information           |
| `outperform`              | 능가하다                                | 단순히 좋은 능력을 보인다는 뜻보다 비교가 핵심       | outperform baselines            |
| `scalable`                | 데이터·시스템 규모가 커져도 적용 가능한 | 기능 추가 가능만을 뜻하지 않음                       | scalable approach               |
| `fine-grained`            | 세분화된, 세밀한 단위의                 | 잘 정제된과 구분                                     | fine-grained representation     |
| `critical`                | 핵심적인, 중요한                        | 이 문맥에서는 치명적인이 아님                        | critical relational paths       |
| `reliability`             | 신뢰성                                  | 의존 가능성과 구분                                   | accuracy and reliability        |
| `robust`                  | 견고한, 안정적인                        | 이 문맥에서는 안전한보다 적절                        | robust reasoning                |
| `novel`                   | 새로운, 새로운 방식의                   | 최신의는 아님                                        | novel framework                 |
| `facilitate`              | 촉진하다, 용이하게 하다                 | 단순히 향상하다보다 ‘쉽게 함’                        | facilitate integration          |
| `capture`                 | 포착하다, 구조에 담다                   | 물리적으로 붙잡다가 아님                             | capture semantic relationships  |
| `substantial`             | 상당한                                  | —                                                    | substantial overhead            |
| `overhead`                | 본래 작업 외에 추가로 드는 부담         | 일반 비용보다 ‘추가 부담’의 뉘앙스                   | computational overhead          |
| `catastrophic forgetting` | 파국적 망각                             | 새 학습 때문에 기존 지식을 심하게 잊는 현상          | risk of catastrophic forgetting |
| `promising alternative`   | 유망한 대안                             | —                                                    | offer a promising alternative   |
| `abstractive QA`          | 정보를 종합해 개방형 답변을 생성하는 QA | 단순히 ‘추상적인 QA’가 아님                          | UltraDomain                     |
| `specific QA`             | 구체적인 정답·근거가 있는 QA            | 이 논문에서는 멀티홉 QA 중심                         | MultiHop-RAG, HotpotQA          |
| `three-level`             | 세 개 층으로 된                         | 처리 순서인 `three-stage`와 구분                     | three-level hierarchical KG     |

---

## 5. Reading Method

### 10초 의미 스케치

완성된 한국어 번역을 만들기 전에 다음만 잡는다.

1. 누가?
2. 무엇을 하는가?
3. 대상은 무엇인가?
4. 방법·이유·결과는 무엇인가?

예:

```text
RAG → retrieval mechanism 활용 → 외부 정보 검색
RAG → retrieved content 기반 → 응답 생성
```

### 긴 문장 체크리스트

1. 가장 바깥 문장의 주어와 동사를 찾는다.
2. 동사를 단독 뜻이 아니라 프레임으로 본다.
3. 같은 형태의 병렬 동사와 공유 주어를 확인한다.
4. 관계절은 잠시 괄호로 빼고 메인 절부터 읽는다.
5. 전치사 뒤 명사가 어떤 관계인지 확인한다.
6. `which`, `where`, `-ing`의 의미상 주체를 확인한다.
7. 영어 어순을 버리고 짧은 한국어로 재진술한다.

### 문장 중요도

- **A — 정독:** 핵심 주장, 방법, 결과 해석, 한계
- **B — 속독:** 배경 설명, 실험 설정, 반복 설명
- **C — 필요할 때 확인:** 인용 나열, 참고문헌, 부차적인 구현 세부사항

중요도를 낮게 판단했다는 이유만으로 섹션 전체를 임의로 건너뛰지 않는다. 먼저 그 섹션이 논문 논리에 하는 역할을 확인한다.

### 어휘를 찾는 기준

- 모르면 핵심 주장을 이해할 수 없는 단어: 즉시 확인
- 문맥으로 방향을 추측할 수 있는 단어: 먼저 추측 후 확인
- 세부 수식이며 주장에 영향이 적은 단어: 표시만 하고 계속 읽기

---

## 6. Questions & Critiques

- Topic/Subtopic 라벨이 LLM 출력에 따라 불일치하면 그래프가 얼마나 팽창하는가?
- 성능 향상이 계층 그래프 자체에서 오는가, 원문 chunk 확장에서 오는가?
- `Topic → Subtopic` 탐색이 실제 관계 경로 추론인가, 관련 검색 공간을 좁히는 분류 과정인가?
- 평가에서 사용한 LLM-as-a-judge와 비교 조건은 공정한가?
- GraphRAG-G를 일부 retrieval 비교에서 제외한 판단은 타당한가?

---

## 7. Daily Log

### 2026-07-12

**읽은 범위**

- Abstract 전체
- Introduction 전체

**오늘 이해한 논문 내용**

- 기존 triplet 기반 GraphRAG의 핵심 문제는 그래프 파편화와 낮은 연결성이다.
- TH-RAG는 Topic, Subtopic, Triplet/Entity의 3계층 그래프를 사용한다.
- 관련 Topic에서 시작해 Subtopic과 Entity를 탐색한 뒤 후보 근거를 질문과의 유사도로 필터링한다.

**오늘 발견한 독해 문제**

- 동사의 뜻은 알아도 `동사 + 목적어 + 전치사/to부정사` 전체 프레임의 인출이 느리다.
- 긴 문장에서 메인 절과 관계절의 주어·동사를 혼동할 때가 있다.
- 영어 어순을 유지한 채 한국어를 조립하면 의미가 부자연스러워진다.
- `which`나 쉼표 뒤 `-ing`의 의미상 주체를 바로 앞 명사로 성급히 연결할 때가 있다.
- 핵심 학술 어휘 하나를 모르면 구조를 찾은 뒤에도 의미 조립이 멈춘다.

**오늘의 핵심 패턴**

- `enable A to do B`
- `outperform A in B`
- `owing to A`
- `leading to A`
- `where S + V`
- `hinder one's ability to do A`

**다음 읽기**

- Related Work 2.1: 초기 RAG와 graph-based RAG의 발전 흐름
- 논문 내용은 빠르게 파악하되 새 동사 프레임이 나타나면 이 문서에 추가한다.

---

## Daily Log Template

아래 블록을 복사해 날짜별로 위에 추가한다.

```md
### YYYY-MM-DD

**읽은 범위**

- **오늘 이해한 논문 내용**

- **새 동사 프레임**

- `frame`: 의미 / 예문

**새 연결 표현**

- **해석을 막은 어휘**

- `word`: 문맥상 의미 / 함께 쓰인 표현

**막힌 문장과 원인**

> 원문

- 구조:
- 막힌 이유:
- 의미 스케치:

**다음 읽기**

-
```

## 260714

논문 읽기용 Academic Vocabulary Cheat Sheet

표현 의미 논문에서의 뉘앙스 예시
adopt 채택하다 기존 방법을 가져와 사용 We adopt HNSW for retrieval.
employ 활용하다 방법론이나 기술을 적용 We employ graph traversal.
use 사용하다 가장 일반적인 표현 We use GPT-4.
utilize 활용하다 use보다 조금 더 형식적 We utilize external knowledge.
leverage 적극 활용하다 장점을 살려 활용 We leverage entity relations.
support 지원하다, 가능하게 하다 특정 기능을 수행 가능하게 함 Supports multi-hop reasoning.
enable 가능하게 하다 support보다 직접적 Enables efficient retrieval.
facilitate 촉진하다 더 쉽게 만들어줌 Facilitates reasoning.
allow 허용하다 수행 가능하게 함 Allows users to query graphs.

⸻

연구 문제 / 한계 설명

표현 의미 예시
challenge 도전 과제 A major challenge is scalability.
issue 문제 Existing methods suffer from this issue.
limitation 한계 One limitation is graph sparsity.
bottleneck 병목 Retrieval becomes a bottleneck.
drawback 단점 This approach has several drawbacks.
weakness 약점 Existing systems exhibit weaknesses.
deficiency 결함, 부족함 Prior work suffers from deficiencies.

⸻

문제 해결

표현 의미 예시
address 해결하다 We address graph sparsity.
tackle 다루다, 해결하다 Tackles multi-hop reasoning.
overcome 극복하다 Overcomes retrieval limitations.
mitigate 완화하다 Mitigates hallucinations.
resolve 해결하다 Resolves ambiguity.

⸻

성능 향상

표현 의미 예시
improve 개선하다 Improves retrieval quality.
enhance 향상시키다 Enhances reasoning ability.
boost 크게 향상시키다 Boosts performance.
increase 증가시키다 Increases accuracy.
reduce 감소시키다 Reduces latency.
decrease 감소시키다 Decreases memory usage.

⸻

연구 기여

표현 의미 예시
propose 제안하다 We propose a new framework.
introduce 소개하다 We introduce GraphRAG++.
present 제시하다 We present our approach.
contribute 기여하다 Our work contributes three aspects.
demonstrate 입증하다 Experiments demonstrate effectiveness.
show 보여주다 Results show significant gains.

⸻

비교 / 평가

표현 의미 예시
outperform 능가하다 Outperforms existing methods.
surpass 뛰어넘다 Surpasses previous SOTA.
achieve 달성하다 Achieves high accuracy.
compare 비교하다 Compared with baselines.
evaluate 평가하다 We evaluate on HotpotQA.
validate 검증하다 Experiments validate our hypothesis.

⸻

GraphRAG에서 자주 등장

표현 의미
explicit relation 명시적 관계
implicit relation 암묵적 관계
graph traversal 그래프 탐색
entity linkage 엔티티 연결
entity relationship 엔티티 관계
knowledge graph 지식 그래프
graph structure 그래프 구조
graph sparsity 그래프 희소성
multi-hop reasoning 멀티홉 추론
path finding 경로 탐색
evidence aggregation 증거 집계
contextual information 문맥 정보
semantic similarity 의미적 유사도
hierarchical structure 계층 구조
global information 전역 정보
local information 지역 정보

⸻

논문에서 진짜 자주 보이는 형용사

표현 의미
explicit 명시적인
implicit 암묵적인
efficient 효율적인
effective 효과적인
scalable 확장 가능한
robust 강건한
comprehensive 포괄적인
accurate 정확한
reliable 신뢰할 수 있는
interpretable 해석 가능한
lightweight 경량의
hierarchical 계층적인

⸻

ARR / ACL 논문에서 자주 보이는 문장 패턴

1. Existing methods suffer from several limitations.
   → 기존 방법들은 여러 한계를 가진다.
2. To address this issue, we propose …
   → 이 문제를 해결하기 위해 우리는 …를 제안한다.
3. Experimental results demonstrate that …
   → 실험 결과는 …를 보여준다.
4. Our method outperforms strong baselines.
   → 제안 방법은 강력한 비교군보다 우수하다.
5. These findings suggest that …
   → 이러한 결과는 …를 시사한다.
6. We further investigate …
   → 추가적으로 …를 분석한다.
7. We observe that …
   → 우리는 …를 관찰하였다.
8. This indicates that …
   → 이는 …를 의미한다.

## 2026-07-15 Method Reading Update

### 새 동사 프레임

| Frame | 문맥상 의미 | 예시 |
|---|---|---|
| `augment A with B` | A에 B를 추가해 보강하다 | augment each triplet with topic annotations |
| `instruct A to do B` | A에게 B하도록 지시하다 | instruct the LLM to extract corpus-relevant topics |
| `define A as B` | A를 B로 정의하다 | define the graph as `G=(V,E)` |
| `consist of A` | A로 구성되다 | `V` consists of three disjoint node sets |
| `store A as B` | A를 B로 저장하다 | store the source sentence as an attribute |
| `interpret A as B` | A를 B로 해석하다 | interpret topic selection as scope definition |
| `bound A to B` | A를 B로 제한하다 | bound `N_T` to a small value |
| `exhibit A` | A라는 특성·현상을 보이다 | exhibit imperfect consistency |
| `mitigate A by doing B` | B함으로써 A를 완화하다 | mitigate inconsistency by selecting multiple candidates |
| `yield A` | A를 산출하다 | yield candidate evidence sentences |
| `retain A as B` | A를 B로 보존하다 | retain the source sentence as an attribute |
| `fall back to A` | 필요할 때 A를 안전장치로 사용하다 | fall back to sentence-level evidence |
| `rely solely on A` | 오직 A에만 의존하다 | rely solely on abstracted triplets |
| `locate A` | A의 위치를 찾아내다 | locate the answer in a large corpus |

### 새 명사 덩어리와 어휘

| 표현 | 자연스러운 의미 | 주의점 |
|---|---|---|
| `supporting sentence` | 답을 뒷받침하는 근거 문장 | `supporting`을 ‘지원 중인’으로 직역하지 않기 |
| `targeted context expansion` | 선별적 컨텍스트 확장 | 모든 chunk가 아니라 관련 문장 주변만 확장 |
| `semantic containment hierarchy` | 의미적 포함 계층 | 단순 연결이 아니라 Topic이 Subtopic을 포괄하는 관계 |
| `typed directed edge` | 타입이 지정된 방향성 엣지 | `typical direct edge`가 아님 |
| `source sentence` | 트리플이 추출된 원문 문장 | ‘원천 문장’보다 자연스러움 |
| `anchor for retrieval` | 검색을 시작하는 기준점 | 선택된 Entity의 역할 |
| `associated edges` | 해당 노드와 연결된 엣지 | `associated` = 관련된, 연결된 |
| `high-confidence node/sentence` | 관련성이 높다고 판단된 노드/문장 | 절대적으로 참이라는 뜻은 아님 |
| `contextual cues` | 주변 문맥 단서 | 선택 문장이 포함된 chunk에서 확보 |
| `near-zero cost` | 거의 없거나 무시할 정도로 적은 비용 | 수학적인 ‘0으로 수렴’과 구분 |

### 명사 덩어리 읽기

무조건 문장 맨 뒤에서 번역하지 않고, 중심 명사를 먼저 찾은 뒤 가까운 수식어부터 붙인다.

```text
key supporting sentences
→ sentences
→ supporting sentences
→ key supporting sentences
→ 핵심 근거 문장

targeted context expansion around relevant sentences
→ expansion
→ context expansion
→ targeted context expansion
→ 관련 문장 주변을 대상으로 한 선별적 컨텍스트 확장
```

### `, which`를 자연스럽게 읽기

먼저 문장을 끊고 대명사를 복원한다.

```text
A, which B
→ A다. 이 A는 B한다.
```

예:

> We collect entity nodes, which act as anchors for retrieval.

> 엔터티 노드를 수집한다. 이 노드들은 검색의 기준점 역할을 한다.

앞 명사가 동사의 주체로 자연스럽지 않으면 앞 상황 전체를 받을 수 있다.

```text
The graph is fragmented, which hinders reasoning.
→ 그래프가 파편화되어 있다. 이로 인해 추론이 방해된다.
```

### 오늘 발견한 반복 문제

- `K1`, `N_T`를 ‘K1번째’, ‘N번째’로 읽지 않고 선택할 **개수**로 읽기
- `while`, `around`, `as`를 영어 어순대로 붙이지 말고 논리 관계를 먼저 판단하기
- `yield`, `exhibit`, `locate`처럼 일상적 첫 번째 뜻과 논문 문맥의 뜻이 다른 동사 확인하기
- 긴 명사구는 중심 명사를 먼저 찾되, 뒤의 전치사구는 별도 설명으로 연결하기
- 원문을 한 문장짜리 한국어로 보존하려 하지 말고 두 문장으로 끊어 재진술하기

### 오늘 읽은 범위

- Method 3.1 Hierarchical Graph Construction
- Method 3.2 Topic-based Graph Traversal
- Method 3.3 Query-based Retrieval
- Method 3.4 Multi-hop Reasoning Robustness

### 다음 읽기

- Experiments의 연구 질문, 데이터셋, baseline, metric을 빠르게 파악한다.
- Results에서는 Table 2, 3, 6, 7을 중심으로 저자의 주장과 실제 수치를 비교한다.
