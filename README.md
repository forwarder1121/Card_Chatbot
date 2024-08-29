# [카드 정보 챗봇 프로젝트](https://cardchatbot-gq6qfp8xt3dzd6tntthh4u.streamlit.app)

이 프로젝트는 사용자가 챗봇 인터페이스를 통해 카드 혜택 정보를 자세히 제공받을 수 있도록 설계되었습니다.     
프로젝트는 웹에서 수집한 카드 데이터를 벡터 데이터베이스(Pinecone)에 저장하고, 이를 활용하여 사용자 질문에 답변합니다.  

## 프로젝트 구조

```plaintext
MINI_PROJECT/
├── CardInfo/                   # 개별 카드 정보 파일을 저장하는 디렉터리
├── .env                        # 환경 변수 파일
├── .gitignore                  # Git 무시 파일
├── card_crawler.py             # 웹사이트에서 카드 정보를 크롤링하는 스크립트
├── chatbot_logic.py            # 챗봇 질의응답 로직을 처리하는 스크립트
├── combined_card_info.json     # 모든 카드 정보를 포함한 통합 JSON 파일
├── Dockerfile                  # Docker 환경을 위한 설정 파일
├── pinecone_store.py           # 카드 데이터를 Pinecone 벡터 데이터베이스에 저장하는 스크립트
├── README.md                   # 프로젝트 설명 파일
├── requirements.txt            # 프로젝트에 필요한 Python 패키지 목록
└── streamlit_app.py            # Streamlit 인터페이스를 관리하는 스크립트
```

## 요구 사항

스크립트를 실행하기 전에, 다음 명령어로 필요한 Python 패키지를 설치하세요:

```bash
pip install -r requirements.txt
```

## 설정 방법

### 1. 환경 변수 설정

프로젝트의 루트 디렉터리에 `.env` 파일을 생성하고 다음 내용을 추가하세요:

```plaintext
OPENAI_API_KEY=your_openai_api_key
PINECONE_API_KEY=your_pinecone_api_key
HUGGINGFACEHUB_API_TOKEN=your_huggingfacehub_api_token
```

`your_openai_api_key`, `your_pinecone_api_key`, `your_huggingfacehub_api_token`은 실제 API 키로 교체해야 합니다.

### 2. 카드 데이터 크롤링

`card_crawler.py` 스크립트를 사용하여 대상 웹사이트에서 카드 정보를 크롤링합니다.  
이 스크립트는 데이터를 추출하여 각 카드사마다 별도의 JSON 파일로 저장합니다.

```bash
python card_crawler.py
```

스크립트를 실행하면 `CardInfo/` 디렉터리에 JSON 파일이 생성됩니다.

### 3. JSON 파일 통합

`card_crawler.py` 스크립트는 데이터를 크롤링한 후 개별 JSON 파일을 하나의 `combined_card_info.json` 파일로 자동 통합합니다.

### 4. Pinecone에 데이터 저장

`pinecone_store.py` 스크립트를 사용하여 통합된 JSON 데이터를 Pinecone 벡터 데이터베이스에 업로드합니다.  
각 카드의 혜택이 Hugging Face 임베딩으로 벡터화되어 Pinecone에 저장됩니다.

```bash
python pinecone_store.py
```

이 스크립트는 데이터가 Pinecone에 한 번만 업로드되도록 보장합니다.

### 5. Streamlit 챗봇 앱 실행

마지막으로, `streamlit_app.py`를 사용하여 챗봇 인터페이스를 실행합니다.  
이 앱을 통해 사용자는 카드 혜택에 대해 질문할 수 있으며, Pinecone 데이터베이스에서 관련 정보를 실시간으로 검색하여 답변을 제공합니다.

```bash
streamlit run streamlit_app.py
```

Streamlit 앱은 브라우저에서 `http://localhost:8501`에서 확인할 수 있습니다.

## 스크립트 개요

### `card_crawler.py`

이 스크립트는 Selenium을 사용하여 특정 웹사이트에서 카드 혜택 데이터를 크롤링합니다.  
데이터는 JSON 형식으로 저장되며, 카드사마다 별도의 파일에 저장됩니다.  
크롤링 후, 개별 JSON 파일을 하나의 `combined_card_info.json` 파일로 통합합니다.

### `pinecone_store.py`

이 스크립트는 `combined_card_info.json`에서 카드 데이터를 로드한 후 Pinecone에 업로드합니다.  
데이터는 Hugging Face 임베딩을 사용해 벡터화된 후 Pinecone에 저장됩니다.  
이렇게 저장된 데이터는 챗봇과의 상호작용 시 효율적으로 검색됩니다.

### `chatbot_logic.py`

이 스크립트는 챗봇의 질의응답 로직을 처리합니다.  
사용자가 입력한 질문을 처리하여 Pinecone에서 관련 정보를 검색하고, GPT 모델을 통해 자연어 응답을 생성합니다.

### `streamlit_app.py`

이 Streamlit 앱은 사용자 인터페이스를 제공합니다.  
사용자와 챗봇 간의 대화를 관리하고, `chatbot_logic.py`의 로직을 활용하여 실시간으로 질문에 답변합니다.

## 사용 예시

1. **데이터 크롤링 및 준비:**
   `card_crawler.py`를 실행하여 데이터를 크롤링하고 `combined_card_info.json`을 생성합니다.

2. **Pinecone에 데이터 저장:**
   `pinecone_store.py`를 실행하여 크롤링된 데이터를 Pinecone에 업로드합니다.

3. **챗봇 실행:**
   `streamlit_app.py`를 실행하여 챗봇 서버를 시작합니다.

## 기여 방법

이 프로젝트에 기여하고 싶다면, 리포지토리를 포크하고 풀 리퀘스트를 생성하세요.  
버그 또는 기능 요청에 대한 이슈를 열어도 좋습니다.

## 라이선스

이 프로젝트는 MIT 라이선스에 따라 라이선스가 부여됩니다. 자세한 내용은 `LICENSE` 파일을 참조하세요.
