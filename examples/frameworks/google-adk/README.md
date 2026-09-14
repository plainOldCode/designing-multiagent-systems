# Google ADK (Agent Development Kit) 예시

이 예시들은 PicoAgents의 동일 패턴을 Google의 Agent Development Kit(ADK)로 구현한 것입니다. ADK는 Gemini 모델에 최적화된 Google의 AI 에이전트(agent) 구축 프레임워크입니다.

## 설정

```bash
# Install Google ADK
pip install google-adk

# Set your Google API key
export GOOGLE_API_KEY="your-api-key"

# Or use Application Default Credentials
gcloud auth application-default login
```

## 예시

### 에이전트(agent)

| 예시                        | PicoAgents 대응              | 설명                                     |
| ----------------------------- | ----------------------------- | --------------------------------------- |
| `agents/basic_agent.py`       | `agents/basic-agent.py`       | 날씨·계산기 도구를 갖춘 에이전트 |
| `agents/memory.py`            | `agents/memory.py`            | 메모리(memory) 관리를 위한 세션(session) 상태(state)     |
| `agents/structured_output.py` | `agents/structured-output.py` | Pydantic 모델 응답                |

### 워크플로(workflow)

| 예시                      | PicoAgents 대응              | 설명                       |
| ------------------------- | ------------------------- | ------------------------- |
| `workflows/sequential.py` | `workflows/sequential.py` | 순차 에이전트 파이프라인 |

### 오케스트레이션(orchestration)

| 예시                          | PicoAgents 대응                   | 설명                          |
| ----------------------------- | ------------------------------ | ----------------------------- |
| `orchestration/loop_agent.py` | `orchestration/round-robin.py` | 시인(poet)과 비평가(critic)의 협업 |
| `orchestration/parallel.py`   | (신규 패턴)                  | 병렬 에이전트 실행      |

## PicoAgents와의 주요 차이점

1. **Google 최적화**: 기본 모델은 Gemini이며 Google Cloud와 긴밀히 통합
2. **ToolContext**: 도구는 `state`, `user_id`, `session_id`를 담은 `ToolContext`를 수신
3. **출력 키(output key) 패턴**: 에이전트는 `output_key`로 세션 상태(state)에 출력을 저장
4. **상태(state) 보간(interpolation)**: 지시문(instruction)이 상태 변수를 참조 가능: `{generated_code}`
5. **내장 워크플로(workflow) 에이전트**: `SequentialAgent`, `ParallelAgent`, `LoopAgent`가 1급 구성 요소(first-class)

## 예시 실행

```bash
# From the examples/frameworks/google-adk directory
python agents/basic_agent.py
python workflows/sequential.py
python orchestration/loop_agent.py
```

## 모델 구성

모든 예시는 기본 모델로 `gemini-flash-latest`를 사용합니다. 다음도 사용할 수 있습니다:

- `gemini-flash-latest` - 빠르고 효율적 (기본값)
- `gemini-1.5-pro` - 복잡한 작업에 더 유능
- `OpenAiLlm` 또는 `AnthropicLlm`을 통한 OpenAI/Anthropic 모델
