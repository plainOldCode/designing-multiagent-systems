# 최소 에이전트(agent) 웹 애플리케이션

어떤 에이전트 웹 애플리케이션이든 갖춰야 할 세 가지 기본 계층을 보여주는, 완전하면서도 최소한의 예시입니다:

1. **에이전트 실행(Agent Execution)** - 에이전트 로직을 실행하는 PicoAgents
2. **통신 브리지(Communication Bridge)** - 실시간 스트리밍(streaming)을 위한 FastAPI + Server-Sent Events
3. **사용자 인터페이스(User Interface)** - 이벤트 스트림을 소비하는 바닐라 JavaScript

**전체 코드:** 약 200줄 (백엔드 100줄, 프론트엔드 100줄)

## 빠른 시작

### 사전 요구 사항

- Python 3.10+
- OpenAI API 키

### 1. 의존성 설치 및 API 키 설정

```bash
# Install dependencies
pip install picoagents fastapi "uvicorn[standard]"

# Set your API key
export OPENAI_API_KEY=your-key-here
```

### 2. 백엔드 시작

**옵션 A: 직접 실행 (가장 단순)**
```bash
cd examples/app/backend
python app.py
```

**옵션 B: uvicorn으로 실행 (더 많은 제어)**
```bash
cd examples/app/backend
uvicorn app:app --reload
```

서버가 `http://localhost:8000`에서 시작하며 프론트엔드를 자동으로 서빙합니다.

### 3. 브라우저 열기

`http://localhost:8000`으로 이동하기만 하면 됩니다 - 프론트엔드가 자동으로 서빙됩니다!

### 4. 사용해 보기

날씨 어시스턴트에게 여러 도시의 날씨를 물어보십시오:
- "파리 날씨가 어떤가요?"
- "도쿄 날씨는 어때요?"
- "뉴욕 날씨에 대해 알려줘"

에이전트가 실시간으로 응답을 스트리밍하는 과정을 지켜보십시오. 다음이 포함됩니다:
- 토큰 단위 텍스트 스트리밍
- 도구 실행 표시기
- 최종 서식화된 응답

## 아키텍처

### 계층 1: 에이전트 실행 (`backend/app.py` 18-35행)

```python
# Define a simple tool
def get_weather(location: str) -> str:
    return f"Weather in {location}: Sunny, 72°F"

# Create an agent
weather_agent = Agent(
    name="weather_assistant",
    model_client=OpenAIChatCompletionClient(model="gpt-4.1-mini"),
    instructions="Help users check weather using the get_weather tool",
    tools=[get_weather],
)
```

에이전트 실행 계층은 추론, 도구 호출, 응답 생성 등 모든 AI 로직을 처리합니다. 이는 UI와 완전히 분리되어 있습니다.

### 계층 2: 통신 브리지 (`backend/app.py` 55-91행)

```python
async def stream_agent_events(message: str):
    """Stream agent events as Server-Sent Events."""
    async for event in weather_agent.run_stream(message, stream_tokens=True):
        yield f"data: {event.model_dump_json()}\n\n"

@app.post("/chat/stream")
async def chat_stream(request: ChatRequest):
    return StreamingResponse(
        stream_agent_events(request.message),
        media_type="text/event-stream",
    )
```

통신 브리지는:
- REST 엔드포인트(endpoint)로 사용자 입력을 수신
- 에이전트 실행 이벤트를 실시간으로 스트리밍
- Server-Sent Events (SSE) 프로토콜 사용
- 에이전트 실행과 UI를 연결

**왜 SSE일까요?** 간단하고, 브라우저에 내장되어 있으며, 서버에서 클라이언트로의 스트리밍에 최적입니다.

### 계층 3: 사용자 인터페이스 (`frontend/index.html`)

```javascript
// Consume the SSE stream
const response = await fetch('http://localhost:8000/chat/stream', {
    method: 'POST',
    body: JSON.stringify({ message }),
});

const reader = response.body.getReader();
// Read and process events as they arrive...
```

UI가 하는 일:
- 사용자 메시지를 백엔드로 전송
- SSE 스트림 소비
- 이벤트가 도착하는 대로 실시간으로 인터페이스 갱신
- 프레임워크 불필요 - 순수 바닐라 JavaScript만 사용

## 시연하는 핵심 개념

### 1. **이벤트 주도 아키텍처(Event-Driven Architecture)**
에이전트가 이벤트(생각, 도구 호출, 응답)를 방출하면 백엔드가 이를 전달하고 UI가 이에 반응합니다.

### 2. **실시간 피드백을 위한 스트리밍**
사용자는 최종 응답을 기다리는 대신 에이전트가 작동하는 모습을 실시간으로 봅니다.

### 3. **관심사 분리(Separation of Concerns)**
- 에이전트는 HTTP나 브라우저를 알지 못합니다
- 백엔드는 DOM이나 UI를 알지 못합니다
- 프론트엔드는 에이전트 구현을 알지 못합니다
- 각 계층은 독립적으로 교체할 수 있습니다

### 4. **프로덕션 준비된 패턴**
동일한 아키텍처로 복잡한 애플리케이션까지 확장됩니다:
- 에이전트 추가 → 계층 1
- 인증, 캐싱 추가 → 계층 2
- 정교한 UI 구축 → 계층 3

## 이 예시 확장하기

**에이전트 추가:**
```python
math_agent = Agent(name="math", tools=[calculator])
research_agent = Agent(name="researcher", tools=[web_search])
```

**오케스트레이션(orchestration) 추가:**
```python
from picoagents.orchestration import RoundRobinOrchestrator

orchestrator = RoundRobinOrchestrator(
    agents=[weather_agent, math_agent],
    termination=MaxMessageTermination(max_messages=10)
)
```

**UI 개선:**
- React, Vue 또는 좋아하는 프레임워크 추가
- 메시지 기록 지속성 추가
- 에이전트 선택 UI 추가
- 문서 에이전트를 위한 파일 업로드 추가

**프로덕션 배포:**
- 인증 미들웨어(middleware) 추가
- 속도 제한(rate limiting) 추가
- 대화 기록용 데이터베이스 추가
- Docker/Kubernetes로 배포

## 왜 중요한가

이 최소 예시는 에이전트 UI 구축이 복잡하지 않다는 것을 보여줍니다 - 세 계층을 이해하고 올바르게 연결하는 문제입니다. 이 패턴을 이해하면 어떤 기술 스택으로든 어떤 에이전트 시스템의 UI든 만들 수 있습니다.

동일한 패턴은 다음을 사용해도 작동합니다:
- **다른 에이전트:** AutoGen, LangChain, 자체 구현
- **다른 백엔드:** Express.js, Flask, Django
- **다른 프론트엔드:** React, Vue, Streamlit, CLI

3계층 아키텍처는 보편적입니다.
