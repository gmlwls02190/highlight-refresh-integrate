<div align="center">

<img src="assets/logo.png" alt="HRI Insight Bridge 로고" width="120" />

# HRI Insight Bridge

**경제 보고서 AI 요약·질의응답 및 관리자 모니터링 서비스**

경제 분석 보고서(PDF)를 올리면 **근거 쪽수와 함께 요약·답변**하고,<br/>
여러 보고서의 경제 지표를 **한 표로 비교**해 주는 RAG 기반 웹 서비스입니다.

<br/>

![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain-1.4-1C3C3C?logo=langchain&logoColor=white)
![LangGraph](https://img.shields.io/badge/LangGraph-1.2-1C3C3C)
![Gradio](https://img.shields.io/badge/Gradio-6.30-F97316?logo=gradio&logoColor=white)
![ChromaDB](https://img.shields.io/badge/ChromaDB-1.5-FF6B6B)
![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?logo=mysql&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI-API-412991?logo=openai&logoColor=white)

현대 AI 서비스 개발자 과정 · 1차 프로젝트 **4조 Insight Bridge**<br/>
2026.10.21 ~ 2026.11.02

[시연](#시연) · [주요 기능](#주요-기능) · [아키텍처](#시스템-아키텍처) · [시작하기](#시작하기) · [팀 소개](#팀-소개)

</div>



---

# 김희진 · 작업 정리

HRI Insight Bridge 프로젝트에서 **김희진(GitHub `gmlwls02190`)** 이 커밋한 내용만 정리했습니다.
기간은 2026.10.26 ~ 2026.10.30이고, 병합 커밋을 뺀 커밋 40개를 기준으로 합니다.

## 한눈에 보기

| 영역 | 내용 |
|---|---|
| 문서 처리 | PDF 업로드, 섹션·청크 분할, 임베딩·유사도 계산, 키워드 추출 검색 |
| 저장소 | Chroma 연결·청크 저장·삭제, 보고서·섹션·청크 DAO, 보고서 조회 DAO |
| 요약 | 3줄 요약, 섹션별 요약, 보고서 요약 서비스, 요약 스키마 |
| 다중 보고서 | 다중 보고서 병합, 병합 화면 연결 |
| 검증 | 보고서 검증 보완(ECOS 통계 대조), 질의응답 탭의 수치 검증 버튼 |
| 화면 | 요약 화면, 업로드·다중 업로드 화면, 요약 방식 라디오 |
| 안정화 | `safe_call` 적용, 마크다운 취소선 처리, 중복 업로드 제한, 오류 정의 |

## 날짜별 작업

### 10/26 · 스키마와 업로드
- 요약 스키마 정의, 미기입 항목 수정, `lines` 최소·최대 길이 추가, 스키마 테스트
- PDF 업로드 기능과 테스트
- PDF 청크 분할

### 10/27 · 임베딩과 저장소
- 임베딩 및 유사도 계산 기능과 테스트
- Chroma 연결, 청크 저장·삭제와 테스트
- 키워드 추출 검색
- Chroma 서버 인증 로직 추가, 서버 업로드 테스트
- 보고서 DAO(보고서·섹션·청크)와 보고서 조회 DAO

### 10/28 · 요약과 화면
- 보고서 조회 DAO 문법 오류 수정
- 출력 오류 정의
- 섹션 요약, 3줄 요약과 테스트
- 섹션 청킹에서 빈 줄로 시작할 때 검증이 실패하던 문제 수정과 테스트
- 업로드 레포트 수정, 수정본으로 테스트
- 보고서 요약 기능과 테스트
- 요약 화면, 업로드 화면, 다중 업로드 화면
- 요청 실행 기능 추가

### 10/29 · 도구 호출과 병합·검증
- Tool Calling 도구 목록(`TOOLS`) 정의
- 다중 보고서 병합 및 보고서 검증 보완 기능
- 일부 함수에 `safe_call` 적용

### 10/30 · 연결과 보완
- 보고서 요약 입력을 텍스트 박스에서 라디오로 변경
- 퍼블리셔 기본값 삭제
- 다중 보고서 병합 기능을 화면에 연결
- 요청 실행 기능 추가
- 마크다운 취소선 문제 처리
- `save_log_node`에서 삭제됐던 `return` 되돌림
- 중복 업로드 제한 기능
- 요청 실행 기능을 제거하고 검증 보완 기능을 질의응답에 연결

## 담당 기능 상세

### 1. 문서 처리 · 청킹 · 임베딩
- **업로드와 분할**: PDF를 올리면 섹션과 청크로 나눕니다.
  (`hri/service/document_service.py`, `hri/splitter/text_splitter.py`, `hri/graph/ingest_graph.py`)
- **임베딩과 유사도**: 청크를 벡터로 바꾸고 유사도를 계산합니다. (`hri/vector_store/embedding.py`)
- **Chroma 저장소**: 연결, 청크 저장·삭제, 서버 인증을 처리합니다. (`hri/vector_store/chroma_store.py`)
- **키워드 검색**: 질문에서 키워드를 뽑아 검색합니다. (`hri/retriever/search.py`)
- **DAO**: 보고서·섹션·청크 저장과 보고서 조회를 담당합니다. (`hri/dao/report_dao.py`)

### 2. 요약
- 3줄 요약, 섹션별 요약, 보고서 요약 서비스를 구현했습니다.
  (`hri/prompt/summary_prompt.py`, `hri/service/summary_service.py`, `hri/domain/schemas/summary.py`)
- 요약 화면에서 보고서를 고르고 요약 방식을 라디오로 선택합니다. (`hri/view/summary_view.py`)

### 3. 다중 보고서 병합
- 여러 보고서를 하나로 병합해 공통점·차이점·종합 결론을 정리합니다.
- 병합 결과의 `~`가 마크다운 취소선으로 보이지 않게 처리했습니다.

### 4. 검증 보완
- 보고서 수치를 ECOS 통계와 대조해 일치·차이를 표시하고, 근거가 부족한 부분을 보완합니다.
- 질의응답 탭의 **수치 검증 (ECOS)** 버튼에서 실행하고, 결과는 대화창에 표시됩니다.
  (`hri/view/chat_view.py`)

### 5. 도구 호출
- `TOOLS` 정의와 도구 연결 코드를 작성했습니다. (`hri/router/tool_router.py`)
- 요청 실행 화면(`on_request`)은 마지막 커밋에서 제거했고, 검증 보완은 질의응답 탭으로 옮겼습니다.

### 6. 안정화
- `safe_call`을 적용해 오류를 사용자용 안내 문구로 바꿉니다. (`hri/exception/errors.py`)
- 같은 파일을 다시 올리지 못하게 중복 업로드를 제한합니다.

## 테스트

| 파일 | 대상 |
|---|---|
| `tests/test_summary_schemas.py` | 요약 스키마 |
| `tests/test_summary_prompt.py` | 요약 프롬프트 |
| `tests/test_summary_service.py` | 요약 서비스 |
| `tests/test_split_sections_empty_line.py` | 섹션 분할의 빈 줄 처리 |
| `tests/test_embedding.py` | 임베딩·유사도 |
| `tests/test_chroma_store.py` | Chroma 저장·삭제 |
| `tests/test_chroma_upload_server.py` | Chroma 서버 업로드 |
| `tests/test_upload_report.py` | 보고서 업로드 |
| `tests/demo_summary.py`, `tests/demo_summarize_report.py` | 요약 시연용 스크립트 |

## 주로 수정한 파일
`hri/view/summary_view.py`, `hri/dao/report_dao.py`, `hri/view/app.py`, `hri/service/document_service.py`, `hri/view/report_view.py`, `hri/router/tool_router.py`, `hri/prompt/summary_prompt.py`, `hri/domain/schemas/summary.py`

## 참고
- 위 내용은 커밋 제목과 수정 파일을 기준으로 정리했습니다. 병합 커밋(`Merge branch ...`)은 제외했습니다.
- 파일 경로의 기능 설명은 커밋 제목에서 추정한 것이라, 세부 동작은 해당 코드를 확인해야 합니다.
