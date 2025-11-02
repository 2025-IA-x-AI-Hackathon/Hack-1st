# F&B Startup Navigator - Backend

AI 멀티 에이전트 시스템을 활용한 외식업 창업 컨설팅 플랫폼

## 개요

F&B Startup Navigator는 예비 요식업 창업자의 개인 데이터와 실제 시장 데이터를 종합 분석하여, 성공 가능성이 높은 맞춤형 창업 아이템과 실행 로드맵을 제안하는 지능형 의사결정 지원 시스템입니다.

## 주요 특징

- **6개의 전문 AI 에이전트**: 각기 다른 전문성을 가진 에이전트들이 협업
- **Google Gemini 2.0 기반**: 최신 AI 모델 활용
- **데이터 기반 분석**: 객관적인 페르소나 분석 및 시장 분석
- **실행 가능한 로드맵**: 구체적이고 단계별 창업 가이드 제공
- **확장 가능한 아키텍처**: 외부 API 및 도구 통합 가능

## 시스템 구성

### 에이전트

1. **Profiler Agent**: 창업자 페르소나 분석 (MBTI, 경력, 리스크 수용도)
2. **Market Analyst Agent**: 시장 및 상권 분석 (인구통계, 경쟁 강도, 트렌드)
3. **Idea Validator Agent**: 창업 아이템 검증 및 TOP 3 선정
4. **Roadmap Architect Agent**: 상세 실행 로드맵 생성
5. **Report Synthesizer Agent**: 최종 보고서 작성
6. **Master Orchestrator**: 전체 워크플로우 관리

### 기술 스택

- **AI Framework**: Google Generative AI (Gemini)
- **Language**: Python 3.13+
- **Data Validation**: Pydantic
- **Error Handling**: Tenacity (Retry logic)

## 빠른 시작

### 1. 설치

```bash
cd backend
pip install -e .
```

### 2. 환경 설정

```bash
cp .env.example .env
# .env 파일에 GOOGLE_API_KEY 입력
```

Google AI API 키는 [Google AI Studio](https://makersuite.google.com/app/apikey)에서 발급받을 수 있습니다.

### 3. 실행

```bash
python -m backend
```

또는:

```bash
backend
```

### 4. Python 코드에서 사용

```python
from backend import run_navigator

personal_info = {
    "gender": "여성",
    "age": 32,
    "mbti": "ENFJ",
    "previous_company": "카카오",
    "previous_position": "마케팅",
    "startup_experience": True
}

project_info = {
    "food_sector": ["베이커리", "카페", "양식"],
    "region": "마포구",
    "capital": 50000000
}

result = run_navigator(personal_info, project_info)
```

## 프로젝트 구조

```
backend/
├── src/backend/
│   ├── agents/           # AI 에이전트 구현
│   │   ├── base.py       # 기본 에이전트 클래스
│   │   ├── profiler.py   # 창업자 프로파일링
│   │   ├── market_analyst.py  # 시장 분석
│   │   ├── idea_validator.py  # 아이템 검증
│   │   ├── roadmap_architect.py  # 로드맵 설계
│   │   ├── report_synthesizer.py  # 보고서 생성
│   │   └── orchestrator.py  # 워크플로우 관리
│   ├── models/           # 데이터 모델
│   │   ├── schemas.py    # Pydantic 스키마
│   │   └── prompts.py    # 에이전트 프롬프트
│   ├── tools/            # 외부 도구 (확장 가능)
│   ├── config.py         # 설정 관리
│   └── main.py           # 메인 진입점
├── data/                 # 데이터 파일
├── pyproject.toml        # 프로젝트 설정
├── .env.example          # 환경 변수 예시
└── README.md             # 이 파일

```

## 상세 사용법

자세한 사용법은 [USAGE.md](./USAGE.md)를 참조하세요.

## API 문서

### run_navigator()

```python
def run_navigator(
    personal_info: dict,
    project_info: dict,
    output_path: Path | None = None
) -> dict:
    """
    F&B Startup Navigator를 실행합니다.
    
    Args:
        personal_info: 사용자 개인 정보
        project_info: 프로젝트 정보
        output_path: 결과 저장 경로 (선택)
    
    Returns:
        최종 보고서 딕셔너리
    """
```

### MasterOrchestrator

```python
orchestrator = MasterOrchestrator()
report = orchestrator.run(personal_info, project_info)
# FinalReport 객체 반환
```

## 개발

### 의존성 추가

```bash
# pyproject.toml 수정 후
pip install -e .
```

### 로깅

로그 레벨 조정:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

### 테스트

```bash
# TODO: 테스트 추가 예정
pytest tests/
```

## 확장

### 외부 API 통합

`backend/tools/` 디렉토리에 새로운 도구를 추가할 수 있습니다:

```python
# backend/tools/market_api.py
class MarketDataAPI:
    def get_market_data(self, region: str):
        # 상권 정보 API 호출
        pass
```

에이전트에서 사용:

```python
# agents/market_analyst.py
from ..tools.market_api import MarketDataAPI

class MarketAnalystAgent(BaseAgent):
    def __init__(self):
        super().__init__(...)
        self.market_api = MarketDataAPI()
    
    def run(self, input_data):
        # API 데이터 활용
        market_data = self.market_api.get_market_data(region)
```

## 기여

프로젝트 기여를 환영합니다! 이슈나 PR을 자유롭게 생성해주세요.

## 라이센스

팀 프로젝트 - Team 1st

## 문의

이슈를 생성하거나 프로젝트 관리자에게 문의하세요.

