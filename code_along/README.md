# PicoAgents Code Along

picoagents 라이브러리의 최소·단계적 구현 — **동일한 API, 더 적은 코드 줄 수**.

이 파일들로 이해를 단계적으로 쌓아 올릴 수 있습니다. 각 버전은 독립적으로 완결되어 있으며 실행할 수 있습니다. 직접 작성한 코드는 변경 없이 완전한 picoagents 라이브러리에서도 그대로 동작합니다.

## 파일

| 파일 | 줄 수 | 추가되는 것 | 장 절 |
|------|-------|--------------|-----------------|
| `ch04_v1_agent.py` | ~100 | 핵심 에이전트(agent) 루프, `run()` | 4.1-4.3 |
| `ch04_v2_tools.py` | ~180 | 도구 호출(tool calling), 함수-스키마 변환 | 4.4 |
| `ch04_v3_memory.py` | ~230 | ListMemory, 대화 이력(메모리(memory)) | 4.5 |
| `ch04_v4_streaming.py` | ~260 | `run_stream()`, 이벤트 유형 | 4.1 |

## 빠른 시작

```bash
# Set your API keys
export AZURE_OPENAI_API_KEY="..."
export AZURE_OPENAI_ENDPOINT="..."

# Or for OpenAI (modify the client in the file)
export OPENAI_API_KEY="..."

# Run any version
python ch04_v1_agent.py
python ch04_v2_tools.py
python ch04_v3_memory.py
python ch04_v4_streaming.py
```

## 완전 라이브러리와 동일한 API

```python
# Code-along version
from ch04_v2_tools import Agent

# Full library - just change the import
from picoagents import Agent

# Same code works with both
agent = Agent(
    name="assistant",
    instructions="You are helpful.",
    tools=[get_weather]
)
response = await agent.run("What's the weather?")
print(response.final_content)
```

## 포함된 것과 생략한 것

| 기능 | Code Along | 전체 라이브러리 |
|---------|--------------|--------------|
| Agent 클래스 | ✓ | ✓ |
| `run()` 메서드 | ✓ | ✓ |
| `run_stream()` | ✓ (v4) | ✓ |
| 도구 호출 | ✓ (v2+) | ✓ |
| ListMemory | ✓ (v3+) | ✓ |
| 미들웨어 | - | ✓ |
| OpenTelemetry | - | ✓ |
| CancellationTokens | - | ✓ |
| 컴포넌트 직렬화 | - | ✓ |
| BaseTool 추상 클래스 | - | ✓ |
| BaseMemory 추상 클래스 | - | ✓ |
| ChromaDB/벡터 메모리 | - | ✓ |
| 에이전트를 도구로 사용 | - | ✓ |

## 모델 클라이언트

이 예시들은 Azure OpenAI를 사용합니다. 다른 제공자를 쓰려면 클라이언트 초기화를 수정하면 됩니다:

```python
# Azure OpenAI (default)
from openai import AsyncAzureOpenAI
client = AsyncAzureOpenAI(api_version="2024-12-01-preview")

# OpenAI
from openai import AsyncOpenAI
client = AsyncOpenAI()  # Uses OPENAI_API_KEY

# Any OpenAI-compatible endpoint
from openai import AsyncOpenAI
client = AsyncOpenAI(base_url="http://localhost:11434/v1", api_key="unused")
```

## 이 문서가 존재하는 이유

4장은 에이전트 개념을 단계적으로 가르치지만, 완전한 라이브러리는 운영 관련 코드가 1000줄이 넘습니다. 이 파일들이 그 간극을 메웁니다:

1. **각 단계에서 실행 가능** - 코드 조각이 아닌 완전한 프로그램
2. **동일한 API** - 코드가 전체 라이브러리로 그대로 이전됨
3. **최소 구성** - 개념 이해에 필요한 것만 포함
4. **단계적 확장** - 각 버전이 이전 버전 위에 쌓임
