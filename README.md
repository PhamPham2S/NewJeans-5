# 🛡️ Aegis-Bio Sentinels: The Guardians of Factual Wellness

> "Unyielding Truth, Unwavering Health." (흔들리지 않는 진실, 변함없는 건강)
> 
> 

**의료 자문봇 (Medical Advisory Bot)** 프로젝트 저장소입니다. 본 프로젝트는 **'뉴진스는5명'** 팀에 의해 개발되었으며, 의료 분야의 특수성을 고려하여 할루시네이션을 최소화하고 정확한 근거를 제시하기 위해 **RAG (Retrieval-Augmented Generation)** 기술을 도입했습니다. 

## 📌 Project Overview

기존 Fine-tuning 방식은 의료 데이터의 긴 문맥과 분포 편차를 학습하는 데 비효율적이며, 새로운 지식 반영이 어렵다는 한계가 있습니다. 이에 우리는 **Hybrid Search (Semantic + Keyword)**와 **Reranker**를 결합한 고도화된 RAG 파이프라인을 구축하여, **근거 기반(Evidence-based)**의 신뢰할 수 있는 답변을 제공합니다.

## 👥 Team: NewJeans-5 (뉴진스는5명)

* **Members**: 조준호, 성명준, 이상준, 이용직, 이채연 



## 🚀 Key Features & Pipeline

본 프로젝트의 핵심 검색 및 생성 파이프라인은 다음과 같습니다.

```mermaid
graph LR
    A[Query] --> B[Naive Retrieval<br/>(k=20)]
    A --> C[BM25 Retrieval<br/>(k=20)]
    B & C --> D[Ensemble & Reranking<br/>(Top-5)]
    D --> E[LLM Generation<br/>(EXAONE-3.5-7.8B)]
    E --> F[Answer]
```

1. 
**Hybrid Retrieval**: 의미 기반의 **Naive Search (k=20)**와 키워드 기반의 **BM25 (k=20)**를 결합하여 Recall(재현율)을 극대화했습니다.


2. 
**Reranking**: **`BAAI/bge-reranker-v2-m3`** 모델을 사용하여 추출된 40개의 문서 중 문맥적 유사도가 가장 높은 상위 5개를 정밀하게 선별합니다.


3. 
**LLM**: 한국어 성능이 뛰어난 **`LGAI-EXAONE/EXAONE-3.5-7.8B-Instruct`** 모델을 사용하여 최종 답변을 생성합니다.


4. 
**Prompt Engineering**: 서술형 답변 시 핵심 정보만 간결하게 요약하도록 프롬프트를 최적화했습니다.



## 📊 Performance

다양한 검색 전략 실험 결과, 최종 파이프라인은 객관식, 단답형, 서술형 모든 지표에서 가장 우수한 성능을 보였습니다.

| 파이프라인 구성 | 객관식 (Acc) | 단답형 (BERTScore) | 서술형 (BERTScore) |
| --- | --- | --- | --- |
| Only RAG (Baseline) | 0.646 | 0.723 | 0.731 |
| RAG + BM25 | 0.643 | 0.729 | 0.728 |
| **RAG + BM25 + Reranker (Final)** | **0.683** | **0.881** | **0.886** |

* 
**Result**: Reranker 도입 후 객관식 정확도는 약 **4%**, 서술형 점수는 약 **16%** 향상되었습니다.



## 📂 Repository Structure

```bash
NewJeans-5/
├── rag+bm25+rerank(bgem3).ipynb    # 메인 RAG 파이프라인 실행 코드 (Hybrid Search + Reranking)
├── eval_items.jsonl                # 성능 평가를 위한 Test Dataset (Question-Answer Pairs)
├── eval_gold_pred_with_bertscore.csv # 모델 예측 결과 및 BERTScore 평가 결과 파일
└── README.md                       # 프로젝트 설명서

```

## 💾 Dataset & Vector DB

본 프로젝트에서 사용된 Vector DB는 용량 문제로 인해 **Hugging Face Datasets**에 별도로 호스팅되어 있습니다. 코드를 실행하기 위해서는 아래 링크에서 DB를 다운로드해야 합니다.

* **Hugging Face Repository**: [NewJeans-5/NewJeans-5](https://huggingface.co/datasets/NewJeans-5/NewJeans-5/tree/main)
* **Data Scale**:
* 원천 데이터: 총 55,918개 JSON 파일 (약 2.2억 토큰) 


* 평가 데이터: 약 3.5만 개 의학 Q&A 쌍 





### How to Load Vector DB

이 코드를 실행하기 위해 Hugging Face에서 `chroma.sqlite3`를 다운로드하여 로컬 경로에 위치시켜야 합니다.

```python
# Hugging Face에서 Vector DB 다운로드 예시
from huggingface_hub import snapshot_download

snapshot_download(repo_id="NewJeans-5/NewJeans-5", repo_type="dataset", local_dir="./chroma_db")

```

## 🛠️ Usage

1. 이 저장소를 Clone 합니다.
2. Hugging Face에서 Vector DB를 다운로드합니다.
3. `rag+bm25+rerank(bgem3).ipynb` 노트북 파일을 열어 경로를 설정한 후 실행합니다.

---

**Developers**: NewJeans-5 Team @ 2026
