# LangGraph 예시

이 예시들은 PicoAgents의 동일 패턴을 LangGraph로 구현한 것입니다. LangGraph는 그래프(graph) 기반 접근법을 사용해 상태(state)가 있는 다중 행위자 애플리케이션을 LLM으로 구축하는 LangChain의 프레임워크입니다.

## 설정

```bash
# Install LangGraph
pip install langgraph langchain-openai python-dotenv

# Option 1: Azure OpenAI (used in examples)
export AZURE_OPENAI_ENDPOINT="https://your-endpoint.openai.azure.com/"
export AZURE_OPENAI_API_KEY="your-key"

# Option 2: OpenAI directly
export OPENAI_API_KEY="your-api-key"
```

## 예시

### 에이전트(agent)

| 예시                        | PicoAgents 대응              | 설명                                     |
| ----------------------------- | ----------------------------- | --------------------------------------- |
| `agents/basic_agent.py`       | `agents/basic-agent.py`       | 날씨·계산기 도구를 갖춘 ReAct 에이전트 |
| `agents/memory.py`            | `agents/memory.py`            | 체커포인터(checkpointer) 기반 대화 메모리(memory)  |
| `agents/structured_output.py` | `agents/structured-output.py` | Pydantic 모델 응답                |

### 워크플로(workflow)

| 예시                      | PicoAgents 대응              | 설명                       |
| ------------------------- | ------------------------- | ------------------------- |
| `workflows/sequential.py` | `workflows/sequential.py` | 순차 노드(node) 파이프라인  |

### 오케스트레이션(orchestration)

| 예시                           | PicoAgents 대응                   | 설명                              |
| ------------------------------ | ------------------------------ | -------------------------------- |
| `orchestration/round_robin.py` | `orchestration/round-robin.py` | 에이전트 순번용 순환(cyclic) 그래프     |
| `orchestration/supervisor.py`  | `orchestration/supervisor.py`  | 슈퍼바이저(supervisor) 제어 위임(delegation) |

## PicoAgents와의 주요 차이점

1. **그래프(graph) 중심**: 모든 것이 StateGraph의 노드(node)와 엣지(edge)
2. **채널(channel)과 리듀서(reducer)**: 상태(state)는 타입이 지정된 채널과 리듀서로 관리
3. **체크포인팅(checkpointing)**: 메모리(memory)는 체커포인터(checkpointer) (MemorySaver, SQLite 등)로 처리
4. **조건부 라우팅(routing)**: 엣지(edge)는 상태(state)에 따라 조건적으로 설정 가능
5. **내장 ReAct**: `create_react_agent`가 도구 호출(tool-calling) 에이전트를 제공

## 예시 실행

```bash
# From the examples/frameworks/langgraph directory
python agents/basic_agent.py
python workflows/sequential.py
python orchestration/round_robin.py
```

## 모델 구성

예시는 `AzureChatOpenAI`로 `gpt-4.1-mini`와 함께 Azure OpenAI를 사용합니다. 대신 OpenAI를 직접 사용하려면 임포트와 클라이언트를 변경하십시오:

```python
# Azure OpenAI (current)
from langchain_openai import AzureChatOpenAI
llm = AzureChatOpenAI(azure_deployment="gpt-4.1-mini", ...)

# OpenAI directly
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o-mini", ...)
```

호환 모델:

- `gpt-4o-mini` / `gpt-4.1-mini` - 빠르고 비용 효율적
- `gpt-4o` - 복잡한 작업에 더 유능
- 모든 LangChain 호환 모델 (Anthropic, Google 등)
