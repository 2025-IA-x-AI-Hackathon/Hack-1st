# F&B Startup Navigator - 시스템 아키텍처

## 시스템 개요

```
┌─────────────────────────────────────────────────────────────────┐
│                    F&B Startup Navigator                         │
│                  AI Multi-Agent System                           │
└─────────────────────────────────────────────────────────────────┘
```

## 워크플로우

```
사용자 입력 (PersonalInfo + ProjectInfo)
    │
    ▼
┌─────────────────────────────────────┐
│   Master Orchestrator               │  ← 전체 워크플로우 관리
│   (orchestrator.py)                 │
└─────────────────────────────────────┘
    │
    ├──────────────────────────────────────────────────────────┐
    │                                                          │
    ▼                                                          │
┌─────────────────────────────────────┐                       │
│ Step 1: Profiler Agent              │  ← 창업자 페르소나 분석 │
│ (profiler.py)                       │                       │
│                                     │                       │
│ Input:  PersonalInfo                │                       │
│ Output: PersonaProfile              │                       │
└─────────────────────────────────────┘                       │
    │                                                          │
    ▼                                                          │
┌─────────────────────────────────────┐                       │
│ Step 2: Market Analyst Agent        │  ← 시장 & 상권 분석   │
│ (market_analyst.py)                 │                       │
│                                     │                       │
│ Input:  PersonaProfile + ProjectInfo│                       │
│ Output: MarketAnalysis              │                       │
└─────────────────────────────────────┘                       │
    │                                                          │
    ▼                                                          │
┌─────────────────────────────────────┐                       │
│ Step 3: Idea Validator Agent        │  ← 아이템 검증 & 선정 │
│ (idea_validator.py)                 │                       │
│                                     │                       │
│ Input:  PersonaProfile +            │                       │
│         MarketAnalysis +            │                       │
│         ProjectInfo                 │                       │
│ Output: Top 3 RecommendedItems      │                       │
└─────────────────────────────────────┘                       │
    │                                                          │
    ▼                                                          │
┌─────────────────────────────────────┐                       │
│ Step 4: Roadmap Architect Agent     │  ← 로드맵 생성        │
│ (roadmap_architect.py)              │                       │
│                                     │                       │
│ Input:  RecommendedItems +          │                       │
│         PersonalInfo +              │                       │
│         ProjectInfo                 │                       │
│ Output: 3 Detailed Roadmaps         │                       │
└─────────────────────────────────────┘                       │
    │                                                          │
    ▼                                                          │
┌─────────────────────────────────────┐                       │
│ Step 5: Report Synthesizer Agent    │  ← 최종 보고서 작성   │
│ (report_synthesizer.py)             │                       │
│                                     │                       │
│ Input:  All Previous Results        │                       │
│ Output: FinalReport                 │                       │
└─────────────────────────────────────┘                       │
    │                                                          │
    ▼                                                          │
최종 보고서 (FinalReport) ──────────────────────────────────────┘
```

## 컴포넌트 구조

### 1. 에이전트 (Agents)

```
agents/
├── base.py                 # BaseAgent 클래스
│   ├── _initialize_model() # Gemini 모델 초기화
│   ├── _call_model()       # API 호출 (retry 로직)
│   └── _extract_json()     # JSON 파싱
│
├── profiler.py            # 창업자 페르소나 분석
│   └── 분석: 성별, 나이, MBTI, 경력, 경험
│
├── market_analyst.py      # 시장 분석
│   └── 분석: 인구통계, 임대료, 경쟁강도, 트렌드
│
├── idea_validator.py      # 아이템 검증
│   └── 평가: 시장성(40%) + 적합성(30%) + 수익성(30%)
│
├── roadmap_architect.py   # 로드맵 설계
│   └── 생성: 공간/운영/자금/입지/행정/메뉴
│
├── report_synthesizer.py  # 보고서 작성
│   └── 통합: 요약 + 분석 + 추천 + 로드맵 + 결론
│
└── orchestrator.py        # 워크플로우 관리
    └── 조율: 순차 실행 및 데이터 전달
```

### 2. 데이터 모델 (Models)

```
models/
├── schemas.py             # Pydantic 스키마
│   ├── PersonalInfo       # 개인 정보
│   ├── ProjectInfo        # 프로젝트 정보
│   ├── PersonaProfile     # 페르소나 프로필
│   ├── MarketAnalysis     # 시장 분석 결과
│   ├── RecommendedItem    # 추천 아이템
│   ├── Roadmap            # 상세 로드맵
│   └── FinalReport        # 최종 보고서
│
└── prompts.py             # 에이전트별 프롬프트
    ├── PROFILER_AGENT_PROMPT
    ├── MARKET_ANALYST_PROMPT
    ├── IDEA_VALIDATOR_PROMPT
    ├── ROADMAP_ARCHITECT_PROMPT
    └── REPORT_SYNTHESIZER_PROMPT
```

### 3. 도구 (Tools)

```
tools/
└── __init__.py            # 외부 API 통합 (확장 가능)
    ├── [미래] market_data.py    # 상권 정보 API
    ├── [미래] map_api.py        # 지도 API
    ├── [미래] trend_analyzer.py # 트렌드 분석
    ├── [미래] policy_search.py  # 정책자금 검색
    └── [미래] image_gen.py      # 이미지 생성
```

### 4. 설정 및 진입점

```
├── config.py              # 설정 관리
│   ├── GOOGLE_API_KEY
│   ├── MODEL_NAME
│   ├── TEMPERATURE
│   └── MAX_TOKENS
│
├── main.py                # 메인 진입점
│   ├── run_navigator()    # 주요 API
│   └── main()             # CLI 실행
│
└── __init__.py            # 패키지 초기화
    └── Exports: run_navigator, MasterOrchestrator
```

## 데이터 흐름

```
1. 입력 데이터
   ┌──────────────────┐     ┌──────────────────┐
   │ PersonalInfo     │     │ ProjectInfo      │
   ├──────────────────┤     ├──────────────────┤
   │ - gender         │     │ - food_sector[]  │
   │ - age            │     │ - region         │
   │ - mbti           │     │ - capital        │
   │ - prev_company   │     └──────────────────┘
   │ - prev_position  │
   │ - startup_exp    │
   └──────────────────┘

2. 중간 결과물
   ┌──────────────────┐     ┌──────────────────┐
   │ PersonaProfile   │     │ MarketAnalysis   │
   ├──────────────────┤     ├──────────────────┤
   │ - summary        │     │ - demographics   │
   │ - style[]        │     │ - avg_rent       │
   │ - risk_tolerance │     │ - competitors[]  │
   │ - strengths[]    │     │ - trends[]       │
   │ - weaknesses[]   │     │ - opportunities[]│
   └──────────────────┘     └──────────────────┘

3. 추천 결과
   ┌───────────────────────────────────────────┐
   │ RecommendedItem (x3)                      │
   ├───────────────────────────────────────────┤
   │ - rank (1-3)                              │
   │ - item (아이템명)                          │
   │ - concept (콘셉트)                         │
   │ - reason (선정 이유)                       │
   │ - scores (market/persona/profitability)   │
   └───────────────────────────────────────────┘

4. 실행 계획
   ┌───────────────────────────────────────────┐
   │ Roadmap (x3)                              │
   ├───────────────────────────────────────────┤
   │ - space_planning (공간 기획)              │
   │ - operation_prep (운영 준비)              │
   │ - financial_plan (자금 계획)              │
   │ - location_strategy (입지 전략)           │
   │ - administrative_tasks (행정/인허가)      │
   │ - menu_development (메뉴 개발)            │
   └───────────────────────────────────────────┘

5. 최종 출력
   ┌───────────────────────────────────────────┐
   │ FinalReport                               │
   ├───────────────────────────────────────────┤
   │ - executive_summary (요약)                │
   │ - persona_profile                         │
   │ - market_analysis                         │
   │ - recommended_items[3]                    │
   │ - roadmaps[3]                             │
   │ - conclusion (결론 및 조언)               │
   │ - generated_at                            │
   └───────────────────────────────────────────┘
```

## 기술 스택

### AI & LLM

- **Google Generative AI**: Gemini 2.0 Flash
  - 고성능 멀티모달 AI
  - 빠른 응답 속도
  - 긴 컨텍스트 윈도우

### Python 라이브러리

- **Pydantic**: 데이터 검증 및 스키마 정의
- **Tenacity**: API 호출 재시도 로직
- **python-dotenv**: 환경 변수 관리
- **httpx**: HTTP 클라이언트 (향후 외부 API 호출용)

### 아키텍처 패턴

- **Agent Pattern**: 각 에이전트는 독립적인 책임
- **Pipeline Pattern**: 순차적 데이터 변환
- **Strategy Pattern**: BaseAgent 추상화
- **Template Method**: 공통 로직 재사용

## 확장성

### 1. 새로운 에이전트 추가

```python
# agents/new_agent.py
from .base import BaseAgent

class NewAgent(BaseAgent):
    def __init__(self):
        super().__init__(
            name="New Agent",
            system_prompt="Your prompt here"
        )
    
    def run(self, input_data):
        # 구현
        pass
```

### 2. 외부 도구 통합

```python
# tools/market_api.py
class MarketDataTool:
    def fetch_demographics(self, region: str):
        # API 호출
        return data
```

### 3. 병렬 처리

현재는 순차 실행이지만, 독립적인 작업은 병렬화 가능:

```python
import asyncio

async def parallel_roadmaps(items):
    tasks = [create_roadmap(item) for item in items]
    return await asyncio.gather(*tasks)
```

## 성능 고려사항

- **API 호출 비용**: 각 에이전트는 1-3회 API 호출
- **응답 시간**: 전체 워크플로우 약 30-60초
- **에러 핸들링**: Tenacity로 자동 재시도
- **로깅**: 모든 단계 추적 가능

## 보안

- API 키는 환경 변수로 관리
- 입력 데이터는 Pydantic으로 검증
- 민감한 정보는 로그에 기록하지 않음

## 향후 개선 방향

1. **캐싱**: 동일 입력에 대한 결과 재사용
2. **스트리밍**: 실시간 진행 상황 표시
3. **A/B 테스트**: 다른 프롬프트/모델 비교
4. **벡터 DB**: RAG를 통한 시장 데이터 검색
5. **멀티모달**: 이미지 생성 및 분석 추가

