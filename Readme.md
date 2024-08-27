# Dacon

## Competition

<details>
<summary>재정정보 AI 검색 알고리즘 경진대회</summary>

- 일시 : 2024.07.29 ~ 2024.08.23 09:59
- 순위(전체 360팀, 참가자 수 1,001명)
  - Public : 21 등, score : 0.71238
  - Private : 24 등, score : 0.68749
- <https://dacon.io/competitions/official/236295/overview/description>
- 주최 : 한국재정정보원, 기획재정부
- 규칙
  - 외부 데이터 사용 금지
  - 학습 데이터 증강 가능 : 제공된 훈련 데이터를 증강할 수 있지만, ChatGPT, Claude 등과 같은 모델의 **코드와 가중치 파일이 공개되지 않은 LLM(또는 사전학습 모델)**은 사용 불가. (데이터 전처리에도 동일한 규칙 적용)
  - 공식 공개 사전 학습 모델 사용 가능 : 가중치 파일이 공식적으로 공개되고 사용에 법적 제약이 없는 사전 학습 모델은 사용 가능
  - 유료 LLM 모델 API 사용 금지
  - 허용 기법 : 순수 프롬프팅, RAG, 파인튜닝

### 정리

- 열린재정의 중앙정부 재정 정보 검색 및 제공 편의성과 활용도를 높이는 Retrieval-Augmented Generation(RAG) 기반 Chatbot 개발
- '재정 정보 질의 응답 데이터셋'과 재정 보고서, 예산 설명자료, 기획재정부 보도자료 등 다양한 재정 관련 텍스트 데이터를 활용해 주어진 질문에 대해 정확도가 높은 응답을 제시하는 자연어처리 알고리즘 개발

### Our Project

#### Data Load

- PDF 데이터를 불러오기 위해 pdfplumber 라이브러리 활용
- 일부 PDF 문서가 가로용지 양식으로 2페이지가 한 개의 페이지로 묶여있어서, pdfplumber 사용시 가로로 텍스트 데이터 추출 과정에서 데이터의 손상이 발생이되어, 이를 가로 용지의 부분을 반으로 나누어 한 개의 페이지로 PDF 파일 수정
- fitz 사용시 최대 score : 0.62(public)
- pdfplumber 사용시 최대 score : 0.69(public)
- pdf 분할 + pdfplumber 사용시 최대 score : 0.71
- 아쉬운 점
  - pdf 에서 마크다운 형식으로 수작업으로 변환하여 그림 및 표의 양식을 자연어 텍스트 양식으로 바꾸었지만, 제대로 활용할 시간이 부족했다.

#### Embedding

- 사용 모델 : intfloat/multilingual-e5-large
- 처음엔, 임베딩 모델을 KoBERT, RoBERTa 등 다양한 방법을 사용하였으나 multilingual-e5-base 보다 성능이 개선되지 않았다.
- GPU를 사용하여 임베딩을 수행하다 보니, 임베딩 모델의 크기에 따라 GPU 메모리가 많이 할당이 되어 일반적인 Local 환경에서 구동의 어려움이 있었다.
  - 이를, 임베딩 모델을 **CPU**로 수행하도록 변경하니 multilingual-e5-large 도 충분히 사용가능하였다.(단, Local의 RAM 크기가 32GB 이상. 임베딩시 약 25GB 사용 - 임베딩 데이터가 많아질 수록 메모리 사용량 높아진다)
- 아쉬운 점
  - CPU로 변경하여 사용하면 리소스 문제를 어느정도 해결할 수 있다는 것을 뒤늦게 알게되어 chunk 조절 등 다양한 실험을 수행할 시간 확보를 충분히 하지 못했다.
  - "bespin-global/klue-sroberta-base-continue-learning-by-mnr", "Alibaba-NLP/gte-multilingual-base", "BAAI/bge-multilingual-gemma2" 등 더 다양한 모델을 사용하여 성능을 비교하지 못했다.

#### VectorDB

- FAISS 사용
- 아쉬운점
  - FAISS가 현재 데이콘 기간에서 빠르게 결과를 도출하는데 있어서 편리한 점이 많아서 다른 VectorDB를 적용은 따로 수행하지 않았다.

#### LLM

- 사용 모델 : meta-llama/Meta-Llama-3.1-8B-Instruct
- Why : 성능이 준수하고 그나마 가벼운 모델이기에 적용
- 적용해 본 모델 : gemma-2-8b, gemma-2-21b(두 모델 모두 llama-3.1-8b보다 성능이 개선되지 않았다.)
- 아쉬운점
  - "meta-llama/Meta-Llama-3.1-70B-Instruct"도 적용해 보고 싶었지만, 해당 모델과 더 큰 모델의 경우 고사양의 GPU 리소스를 요구하여 Tesla-V100으로도 정상적으로 수행에 어려움이 존재하였다.
  - RAG 기법이 아닌, Fine-tuning을 제대로 시도해보지 못 했던 점이 아직 공부해야 될 점인 것 같다.(결과가 어떻든 성능을 한번이라도 내본 경험을 했어야 했다. RAG에서 성능을 급격히 향상시키다 보니 이 부분을 소홀했던 것 같다.)

- 대회 정보 : <https://dacon.io/competitions/official/236295/overview/description>
- Leaderboard
  - private : 24/359 (6.67%)
  - public : 21/359 (5.84%)

</details>
