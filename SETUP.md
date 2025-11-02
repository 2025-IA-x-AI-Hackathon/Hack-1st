# Setup Guide - F&B Startup Navigator

## 완료된 작업 요약

FastAPI 백엔드와 프론트엔드 연동이 완료되었습니다.

### 생성된 파일

#### Backend
1. **`backend/src/backend/main.py`** - FastAPI 메인 애플리케이션
   - `/api/submit` POST 엔드포인트 구현
   - CORS 설정 (프론트엔드 연동)
   - 요청/응답 데이터 변환
   - 에러 처리 및 로깅

2. **`backend/run_server.py`** - 개발 서버 실행 스크립트
   - 자동 리로드 설정
   - 포트 8080에서 실행

3. **`backend/test_api.py`** - API 테스트 스크립트
   - Health check 테스트
   - Submit 엔드포인트 테스트
   - 결과를 JSON 파일로 저장

4. **`backend/API_INTEGRATION.md`** - 상세 API 연동 가이드
   - 요청/응답 형식 설명
   - TypeScript 타입 정의
   - 프론트엔드 구현 예시
   - 에러 처리 가이드
   - 트러블슈팅

#### Frontend
1. **`frontend/src/api/startup.ts`** - API 클라이언트 유틸리티
   - TypeScript 타입 정의 (모든 스키마)
   - StartupAPIClient 클래스
   - transformFormDataToRequest 헬퍼 함수
   - 싱글톤 인스턴스 (startupAPI)

2. **`frontend/src/pages/SelectionSummary.tsx`** - 수정됨
   - 새로운 API 클라이언트 사용
   - 간소화된 백엔드 호출 로직

#### Documentation
1. **`README.md`** - 프로젝트 루트 README 업데이트
2. **`backend/README.md`** - 백엔드 README 업데이트
3. **`frontend/README.md`** - 프론트엔드 README 업데이트

#### Configuration
1. **`backend/pyproject.toml`** - 의존성 추가
   - `fastapi>=0.115.0`
   - `uvicorn[standard]>=0.32.0`
   - `requests>=2.32.0` (dev)

---

## 설치 및 실행

### 1단계: 백엔드 설정

```bash
cd backend

# 의존성 설치
pip install -e .
# 또는
uv sync

# 환경 변수 설정
echo "GOOGLE_API_KEY=your_api_key_here" > .env

# 서버 실행
python run_server.py
```

**확인:**
- 서버: http://localhost:8080
- API 문서: http://localhost:8080/docs
- Health: http://localhost:8080/health

### 2단계: 프론트엔드 설정

```bash
cd frontend

# 의존성 설치
npm install

# 환경 변수 설정
echo "VITE_API_URL=http://localhost:8080" > .env

# 개발 서버 실행
npm run dev
```

**확인:**
- 프론트엔드: http://localhost:5173

---

## API 테스트

### 자동 테스트 스크립트

```bash
cd backend
python test_api.py
```

### 수동 테스트 (cURL)

```bash
curl -X POST http://localhost:8080/api/submit \
  -H "Content-Type: application/json" \
  -d '{
    "personalInfo": {
      "name": "테스트",
      "gender": "m",
      "age": 34,
      "mbti": "ENTJ",
      "previous_job": "마케터",
      "self_employed_experience": true
    },
    "projectInfo": {
      "foodSector": "카페",
      "region": "강남구",
      "capital": 50000000
    }
  }'
```

### 브라우저 테스트

1. 프론트엔드에서 정보 입력
2. "최종 확인" 페이지까지 진행
3. "확정" 버튼 클릭
4. 개발자 도구 콘솔에서 요청/응답 확인

---

## 데이터 흐름

```
Frontend (React)
    │
    ├─ User fills form
    │
    ├─ transformFormDataToRequest()
    │   └─ Converts form data to API format
    │
    ├─ startupAPI.submitStartupPlan()
    │   └─ POST /api/submit
    │
    v
Backend (FastAPI)
    │
    ├─ Validate request (Pydantic)
    │
    ├─ Transform to workflow schemas
    │   ├─ PersonalInfo
    │   └─ ProjectInfo
    │
    ├─ run_workflow()
    │   ├─ Profiler Agent
    │   ├─ Market Analyst Agent
    │   ├─ Item Recommender Agent
    │   └─ Roadmap Architect Agent (x3)
    │
    ├─ Generate FinalReport
    │   ├─ executive_summary
    │   ├─ persona_profile
    │   ├─ market_analysis
    │   ├─ recommended_items (TOP 3)
    │   └─ roadmaps (3개)
    │
    └─ Return JSON response
```

---

## 주요 엔드포인트

### POST /api/submit

**Request:**
```typescript
{
  personalInfo: {
    name: string;
    gender: "m" | "f";
    age: number;
    mbti: string;
    previous_job: string;
    self_employed_experience: boolean;
  };
  projectInfo: {
    foodSector: string;
    region: string;
    capital: number;
  };
}
```

**Response:**
```typescript
{
  executive_summary: string;
  persona_profile: PersonaProfile;
  market_analysis: MarketAnalysis;
  recommended_items: RecommendedItem[];  // 3개
  roadmaps: Roadmap[];  // 3개
}
```

---

## 환경 변수

### Backend (.env)
```bash
GOOGLE_API_KEY=your_google_api_key
```

### Frontend (.env)
```bash
VITE_API_URL=http://localhost:8080
```

---

## 트러블슈팅

### 백엔드 서버가 시작되지 않음

```bash
# 의존성 재설치
cd backend
pip install -e . --force-reinstall

# 또는
uv sync --reinstall
```

### CORS 에러

`backend/src/backend/main.py`의 CORS 설정 확인:
```python
allow_origins=["http://localhost:5173", "http://localhost:3000"]
```

프론트엔드 포트가 다른 경우 추가.

### API 타임아웃

워크플로우 실행은 30초~2분 정도 소요됩니다.
- Google AI API 속도에 따라 다름
- 프론트엔드에 적절한 로딩 UI 필요

### 의존성 버전 충돌

```bash
# Backend
cd backend
uv sync --upgrade

# Frontend
cd frontend
npm update
```

---

## 다음 단계

1. **로딩 페이지 개선** (`RoadmapLoading.tsx`)
   - 실제 진행률 표시 (서버 사이드 이벤트?)
   - 각 에이전트 실행 상태 표시

2. **결과 페이지 구현** (`Roadmap.tsx`)
   - FinalReport 데이터 렌더링
   - 로드맵 시각화
   - PDF 다운로드 기능

3. **에러 처리 개선**
   - 재시도 로직
   - 더 상세한 에러 메시지
   - 오프라인 지원

4. **성능 최적화**
   - 응답 캐싱
   - 부분 결과 스트리밍
   - 에이전트 병렬 처리 확대

---

## 참고 문서

- [Backend README](./backend/README.md)
- [Frontend README](./frontend/README.md)
- [API Integration Guide](./backend/API_INTEGRATION.md)
- [Agents Documentation](./docs/agents.md)

---

## 지원

문제가 있거나 질문이 있으면 이슈를 생성하세요.

