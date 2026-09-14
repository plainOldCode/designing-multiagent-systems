# 프레임워크 비교 예시

이 디렉터리는 서로 다른 프레임워크 전반에 걸친 멀티 에이전트 패턴의 동등 구현을 담고 있습니다. 목표는 이 책에서 가르치는 핵심 패턴이 보편적이며 어떤 프레임워크에서든 구현할 수 있음을 보여주는 것입니다.

## 구조

각 프레임워크 하위 디렉터리는 메인 `examples/` 구조를 그대로 따릅니다:

```
frameworks/
├── agent-framework/     # Microsoft Agent Framework
│   ├── agents/          # Basic agents, memory, structured output
│   ├── workflows/       # Sequential workflows
│   └── orchestration/   # Round-robin, handoff patterns
├── google-adk/          # Google Agent Development Kit
│   ├── agents/          # Basic agents, memory, structured output
│   ├── workflows/       # Sequential workflows
│   └── orchestration/   # Loop agent, parallel patterns
└── langgraph/           # LangGraph (LangChain's graph-based agents)
    ├── agents/          # Basic agents, memory, structured output
    ├── workflows/       # Sequential workflows
    └── orchestration/   # Round-robin, supervisor patterns
```

## 패턴 대응표

| 패턴 | PicoAgents | Agent Framework | Google ADK | LangGraph |
|---------|------------|-----------------|------------|-----------|
| 도구를 갖춘 기본 에이전트(agent) | `Agent` + 함수 | `ChatAgent` + `@ai_function` | `Agent` + 함수 | `create_react_agent` + `@tool` |
| 메모리(memory)/컨텍스트(context) | `ListMemory` | `ContextProvider` | `ToolContext.state` | `MemorySaver` 체커포인터(checkpointer) |
| 순차 워크플로(workflow) | `Workflow.chain()` | `SequentialBuilder` | `SequentialAgent` | `StateGraph` + 엣지(edge) |
| 라운드 로빈 오케스트레이션(orchestration) | `RoundRobinOrchestrator` | `WorkflowBuilder` (순환) | `LoopAgent` | `StateGraph` + 조건부 엣지 |
| 병렬 오케스트레이션 | (수동 asyncio) | `ConcurrentBuilder` | `ParallelAgent` | (수동 asyncio) |
| 슈퍼바이저 오케스트레이션 | `SupervisorOrchestrator` | (수동) | (sub_agents) | `StateGraph` + 라우팅(routing) |
| 핸드오프(handoff) 오케스트레이션 | (수동) | `HandoffBuilder` | (sub_agents 라우팅) | `Command`/`Send` |
| 구조화된 출력(structured output) | `output_format=Model` | `response_format=Model` | `response_schema=Model` | `with_structured_output` |

## 예시 실행

### Microsoft Agent Framework

```bash
# Install agent-framework
pip install agent-framework[azure]

# Set environment variables
export AZURE_OPENAI_ENDPOINT="https://your-endpoint.openai.azure.com/"
export AZURE_OPENAI_API_KEY="your-key"  # Or use Azure CLI auth

# Run examples
python examples/frameworks/agent-framework/agents/basic_agent.py
python examples/frameworks/agent-framework/orchestration/round_robin.py
```

### Google ADK

```bash
# Install Google ADK
pip install google-adk

# Set environment variable
export GOOGLE_API_KEY="your-api-key"

# Run examples
python examples/frameworks/google-adk/agents/basic_agent.py
python examples/frameworks/google-adk/orchestration/loop_agent.py
```

### LangGraph

```bash
# Install LangGraph and LangChain OpenAI
pip install langgraph langchain-openai python-dotenv

# Option 1: Azure OpenAI (used in examples)
export AZURE_OPENAI_ENDPOINT="https://your-endpoint.openai.azure.com/"
export AZURE_OPENAI_API_KEY="your-key"

# Option 2: OpenAI directly (see README for code changes)
export OPENAI_API_KEY="your-api-key"

# Run examples
python examples/frameworks/langgraph/agents/basic_agent.py
python examples/frameworks/langgraph/workflows/sequential.py
python examples/frameworks/langgraph/orchestration/supervisor.py
```

## 비교 철학

이 예시들의 목표는 다음과 같습니다:

1. **동등성 보여주기**: 같은 작업, 같은 동작, 다른 구문(syntax)
2. **패턴 강조**: 핵심 패턴은 프레임워크에 독립적
3. **실용적일 것**: 실행 가능한 예시, 유사 코드(pseudo-code) 아님
4. **집중 유지**: 프레임워크 간에 깔끔하게 대응되는 것만 재현

명확한 대응이 없는 프레임워크 고유 기능은 의도적으로 건너뜁니다.
