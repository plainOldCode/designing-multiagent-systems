# Microsoft Agent Framework 예시

이 예시들은 PicoAgents의 동일 패턴을 Microsoft Agent Framework로 구현한 것입니다. Microsoft Agent Framework는 AutoGen과 Semantic Kernel을 개발한 것과 같은 팀이 만들었으며, 두 라이브러리의 후속 세대입니다.

## 설정

```bash
# Install agent-framework with Azure support
pip install agent-framework[azure]

# Authentication options:

# Option 1: API Key (set environment variables)
export AZURE_OPENAI_ENDPOINT="https://your-resource.openai.azure.com/"
export AZURE_OPENAI_CHAT_DEPLOYMENT_NAME="gpt-4.1-mini"
export AZURE_OPENAI_API_KEY="your-api-key"

# Option 2: Azure CLI (recommended for development)
az login
export AZURE_OPENAI_ENDPOINT="https://your-resource.openai.azure.com/"
export AZURE_OPENAI_CHAT_DEPLOYMENT_NAME="gpt-4.1-mini"
```

## 예시

### 에이전트(agent)

| 예시                        | PicoAgents 대응              | 설명                                     |
| ----------------------------- | ----------------------------- | --------------------------------------- |
| `agents/basic_agent.py`       | `agents/basic-agent.py`       | 날씨·계산기 도구를 갖춘 에이전트 |
| `agents/memory.py`            | `agents/memory.py`            | 메모리(memory) 주입용 컨텍스트(provider) 제공자   |
| `agents/structured_output.py` | `agents/structured-output.py` | Pydantic 모델 응답                |

### 워크플로(workflow)

| 예시                      | PicoAgents 대응              | 설명                       |
| ------------------------- | ------------------------- | ------------------------- |
| `workflows/sequential.py` | `workflows/sequential.py` | 순차 에이전트 파이프라인 |

### 오케스트레이션(orchestration)

| 예시                           | PicoAgents 대응                   | 설명                          |
| ------------------------------ | ------------------------------ | ------------------------------ |
| `orchestration/round_robin.py` | `orchestration/round-robin.py` | 시인(poet)과 비평가(critic)의 협업  |
| `orchestration/handoff.py`     | (신규 패턴)                  | 에이전트 간 핸드오프(handoff) 라우팅(routing) |

## PicoAgents와의 주요 차이점

1. **상태(state)리스 에이전트**: Agent Framework의 에이전트는 대화 기록을 내부에 저장하지 않습니다 - 상태(state)는 `AgentThread`를 사용
2. **컨텍스트 제공자(context provider)**: `invoking()`과 `invoked()` 훅(hook)을 활용한 더 구조화된 메모리(memory) 주입 방식
3. **워크플로(workflow) 빌더(builder)**: 플루언트(fluent) API 패턴 (`SequentialBuilder`, `HandoffBuilder` 등)
4. **이벤트 스트리밍(streaming)**: 모든 작업이 구조화된 `WorkflowEvent` 유형을 방출

## 예시 실행

```bash
# From the examples/frameworks/agent-framework directory
python agents/basic_agent.py
python workflows/sequential.py
python orchestration/round_robin.py
```
