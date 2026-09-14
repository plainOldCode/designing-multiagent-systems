> **번역 안내**: 이 브랜치(ko)는 비공식 한국어 번역 브랜치입니다.
> 원문과 불일치하는 경우 원문(main)이 우선하며, 이 프로젝트의 Apache-2.0 라이선스가 그대로 적용됩니다.

# Designing Multi-Agent Systems

[Designing Multi-Agent Systems: Principles, Patterns, and Implementation for AI Agents](https://buy.multiagentbook.com/?utm_source=github&utm_medium=readme)의 공식 코드 저장소입니다. 저자는 [Victor Dibia](https://victordibia.com)입니다.

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/victordibia/designing-multiagent-systems?quickstart=1)

[![Designing Multi-Agent Systems](./docs/images/bookcover.png)](https://buy.multiagentbook.com/?utm_source=github&utm_medium=readme)

완전하고 검증된 구현체를 통해 제1원리(first principles)로부터 효과적인 멀티 에이전트 시스템(multi-agent system)을 구축하는 방법을 배웁니다. 이 저장소에는 멀티 에이전트 시스템이 어떻게 작동하는지를 가르치는 단일 목적을 위해 처음부터 완전히 만든 만능급 멀티 에이전트 프레임워크(framework) **PicoAgents**가 포함되어 있습니다. 에이전트(agent) 추론 루프부터 오케스트레이션(orchestration) 패턴까지 모든 구성 요소가 명료성과 투명성을 갖추어 구현되어 있습니다.

[전자책 구매](https://buy.multiagentbook.com/?utm_source=github&utm_medium=readme) | [Amazon 페이퍼백](https://www.amazon.com/dp/B0G2BCQQJY) | [Amazon 하드커버](https://www.amazon.com/dp/B0G2F6T2BZ)

---

## 왜 이 책과 코드 저장소인가?

AI 에이전트 분야가 빠르게 진화하면서, 효과적인 멀티 에이전트 시스템을 만들기 위한 명확한 패턴들이 분명히 드러나고 있습니다. 이 책은 이러한 패턴을 식별하고 효과적으로 적용하기 위한 실질적인 지침을 제공하는 데 초점을 맞춥니다.

**이 방식이 특별한 이유:**

- **기초 우선**: 처음부터 직접 만들어 모든 구성 요소와 설계 결정을 이해합니다
- **완전한 구현**: 모든 이론적 개념을 실제로 동작하고 검증된 코드로 뒷받침합니다
- **프레임워크 독립적**: 특정 프레임워크를 초월하는 핵심 패턴 (단일 프레임워크 중심의 책에서 흔한 종속(lock-in)이나 낡은 API 문제를 피합니다)
- **운영 고려사항**: 실제 경험에서 나온 평가(evaluation), 최적화, 배포 지침

## 무엇을 배우고 무엇을 구축하는가

이 책은 이론에서 운영까지 이르는 4개 부분(부)으로 구성되어 있습니다:

### 1부: 멀티 에이전트 시스템의 기초

|  장  | 제목 | 코드 | 학습 성과 |
| -------- | -------------------------------------------- | --------------------------------------------------------------------------------- | -------------------------------------------------------- |
| **1장** | 멀티 에이전트 시스템 이해 | 시인/비평가 예시, [`yc_analysis/`](examples/workflows/yc_analysis/) 참고 | 멀티 에이전트 시스템이 언제 필요한지 이해 |
| **2장** | 멀티 에이전트 패턴 | - | 조정 전략 숙달(워크플로(workflow) vs 자율) |
| **3장** | 멀티 에이전트 시스템을 위한 UX 설계 원칙 | - | 직관적인 에이전트 인터페이스 구축 원칙 |

### 2부: 멀티 에이전트 시스템을 처음부터 구축하기

| 장 | 제목 | 코드 | 학습 성과 |
| -------- | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| **4장** | 첫 번째 에이전트 만들기 | [`agents/_agent.py`](picoagents/src/picoagents/agents/_agent.py), [`basic-agent.py`](examples/agents/basic-agent.py), [`memory.py`](examples/agents/memory.py), [`middleware.py`](examples/agents/middleware.py), [`structured-output.py`](examples/agents/structured-output.py), [`agent_as_tool.py`](examples/agents/agent_as_tool.py), [`otel/`](examples/otel/), [`memory/`](examples/memory/), [`tools/approval_example.py`](examples/tools/approval_example.py) <br> [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/victordibia/designing-multiagent-systems/blob/main/examples/notebooks/01_basic_agent.ipynb) | 도구, 메모리(memory), 스트리밍(streaming), 미들웨어(middleware), 관측성(observability), 사람 개입(human-in-the-loop)을 갖춘 에이전트 구축 |
| **5장** | 컴퓨터 사용 에이전트 | [`agents/_computer_use/`](picoagents/src/picoagents/agents/_computer_use/), [`computer_use.py`](examples/agents/computer_use.py) | 멀티모달 추론을 갖춘 브라우저 자동화 에이전트 구축 |
| **6장** | 멀티 에이전트 워크플로 구축 | [`workflow/`](picoagents/src/picoagents/workflow/), [`workflows/`](examples/workflows/) | 스트리밍 관측성을 갖춘 타입 안전한 워크플로 구축 |
| **7장** | 자율 멀티 에이전트 오케스트레이션 | [`orchestration/`](picoagents/src/picoagents/orchestration/), [`round-robin.py`](examples/orchestration/round-robin.py), [`ai-driven.py`](examples/orchestration/ai-driven.py), [`plan-based.py`](examples/orchestration/plan-based.py) | GroupChat, LLM 주도, 계획 기반 오케스트레이션 구현 (Magentic One 패턴) |
| **8장** | 현대적 에이전트 UX 애플리케이션 구축 | [`app/`](examples/app/) (최소 구성 FastAPI+SSE 예시), [`webui/`](picoagents/src/picoagents/webui/) (운영용 React UI) | 웹 UI, 자동 발견, 실시간 스트리밍을 갖춘 대화형 에이전트 애플리케이션 구축 |
| **9장** | 멀티 에이전트 프레임워크 | [`frameworks/`](examples/frameworks/) (Microsoft Agent Framework, Google ADK, LangGraph 비교) | 멀티 에이전트 프레임워크를 평가하고 올바르게 선택 |

### 3부: 멀티 에이전트 시스템 평가 및 최적화

| 장 | 제목 | 코드 | 학습 성과 |
| --------- | ------------------------------ | ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------- |
| **10장** | 멀티 에이전트 시스템 평가 | [`eval/`](picoagents/src/picoagents/eval/), [`agent-evaluation.py`](examples/evaluation/agent-evaluation.py) | LLM-as-judge와 지표를 갖춘 평가 프레임워크 구축 |

### 4부: 실세계 응용

| 장 | 제목 | 코드 | 학습 성과 |
| --------- | ----------------------------------------- | ------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| **14장** | 비정형 데이터로 비즈니스 질문에 답하기 | [`yc_analysis/`](examples/workflows/yc_analysis/) | 운영 사례 연구: 비용 최적화와 체크포인트(checkpoint)로 5,000개 이상의 기업 분석 |
| **17장** | 소프트웨어 엔지니어링 에이전트 | [`swe_agent/`](examples/agents/swe_agent/) | 코딩 도구와 워크스페이스 관리를 갖춘 완전한 소프트웨어 엔지니어링 에이전트 구축 |

## 시작하기

### 옵션 1: 대화형 노트북

위의 장 표에서 Colab 배지를 클릭하면 브라우저에서 예시를 실행할 수 있습니다. 설치가 필요 없습니다.

### 옵션 2: GitHub Codespaces

<a href="https://codespaces.new/victordibia/designing-multiagent-systems?quickstart=1" target="_blank"><img src="https://github.com/codespaces/badge.svg" alt="Open in GitHub Codespaces"></a>

브라우저에서 미리 설정된 개발 환경을 제공합니다. 열리면 다음을 진행합니다:

1. API 키 추가: `export OPENAI_API_KEY='your-key'`
2. 예시 실행: `python examples/agents/basic-agent.py`
3. 웹 UI 실행: `picoagents ui`

무료 등급: 월 60시간

### 옵션 3: 로컬 설치

```bash
# Clone the repository
git clone https://github.com/victordibia/designing-multiagent-systems.git
cd designing-multiagent-systems

# Navigate to the PicoAgents framework directory
cd picoagents

# Create virtual environment (recommended)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Basic installation
pip install -e .

# Or install with optional features
pip install -e ".[web]"           # Web UI and API server
pip install -e ".[mcp]"           # MCP client and playground (mcp>=2.0.0)
pip install -e ".[persist]"       # Run/eval persistence behind the History page
pip install -e ".[computer-use]"  # Browser automation
pip install -e ".[examples]"      # Run example scripts
pip install -e ".[all]"           # Most extras (not persist, otel, dev, frameworks)

# Set up your API key
export OPENAI_API_KEY="your-api-key-here"
```

### 빠른 시작: 첫 번째 에이전트

이 책에서는 멀티 에이전트 시스템 구축의 기초를 다루며, 아래에 보이는 `Agents` 추상화를 단계적으로 쌓아 올립니다:

```python
from picoagents import Agent, OpenAIChatCompletionClient

def get_weather(location: str) -> str:
    """Get current weather for a given location."""
    return f"The weather in {location} is sunny, 75°F"

# Create an agent
agent = Agent(
    name="assistant",
    instructions="You are helpful. Use tools when appropriate.",
    model_client=OpenAIChatCompletionClient(model="gpt-4.1-mini"),
    tools=[get_weather]
)

# Use the agent
response = await agent.run("What's the weather in Paris?")
print(response.messages[-1].content)
```

**더 단순한 시작점을 원하나요?** [`code_along/`](code_along/) 디렉터리에서는 네 단계를 거쳐 영점에서 최소한의 에이전트를 만듭니다: [핵심 에이전트 루프](code_along/ch04_v1_agent.py) → [도구 호출](code_along/ch04_v2_tools.py) → [메모리](code_along/ch04_v3_memory.py) → [스트리밍](code_along/ch04_v4_streaming.py). PicoAgents는 같은 아이디어를 확장한 운영 준비 버전입니다.

### 모델 클라이언트 설정

PicoAgents는 통합 인터페이스로 여러 LLM 제공자를 지원합니다. 각 제공자의 설정은 최소한입니다 — API 자격 증명만 준비하고 클라이언트 클래스를 바꾸면 됩니다. 임의의 제공자를 위한 커스텀 모델 클라이언트 구축은 4장에서 다룹니다.

| 제공자 | 클라이언트 클래스 | 설정 | 예시 | 소스 |
| ----------------- | ---------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | -------------------------------------------------------------------- |
| **OpenAI** | [`OpenAIChatCompletionClient`](picoagents/src/picoagents/llm/_openai.py) | 1. [platform.openai.com](https://platform.openai.com)에서 API 키 발급<br>2. `export OPENAI_API_KEY='sk-...'` | [`basic-agent.py`](examples/agents/basic-agent.py) | [`_openai.py`](picoagents/src/picoagents/llm/_openai.py) |
| **Azure OpenAI** | [`AzureOpenAIChatCompletionClient`](picoagents/src/picoagents/llm/_azure_openai.py) | 1. [Azure Portal](https://portal.azure.com)에 모델 배포<br>2. 엔드포인트, 키, 배포 이름 설정 | [`swe_agent/agent.py`](examples/agents/swe_agent/agent.py) 참고 | [`_azure_openai.py`](picoagents/src/picoagents/llm/_azure_openai.py) |
| **Anthropic** | [`AnthropicChatCompletionClient`](picoagents/src/picoagents/llm/_anthropic.py) | 1. [console.anthropic.com](https://console.anthropic.com)에서 API 키 발급<br>2. `export ANTHROPIC_API_KEY='sk-...'` | [`agent_anthropic.py`](examples/agents/agent_anthropic.py) | [`_anthropic.py`](picoagents/src/picoagents/llm/_anthropic.py) |
| **GitHub Models** | [`OpenAIChatCompletionClient`](picoagents/src/picoagents/llm/_openai.py)<br>+ `base_url` | 1. [github.com/settings/tokens](https://github.com/settings/tokens)에서 토큰 발급<br>2. `export GITHUB_TOKEN='ghp_...'`<br>3. `base_url="https://models.github.ai/inference"` 설정 | [`agent_githubmodels.py`](examples/agents/agent_githubmodels.py) | [`_openai.py`](picoagents/src/picoagents/llm/_openai.py) 사용 |
| **로컬/커스텀** | [`OpenAIChatCompletionClient`](picoagents/src/picoagents/llm/_openai.py)<br>+ `base_url` | 임의의 OpenAI 호환 엔드포인트 지정<br>(Ollama, LM Studio, vLLM 등) | `base_url="http://localhost:8000"` 사용 | [`_openai.py`](picoagents/src/picoagents/llm/_openai.py) 사용 |

**간단한 예시:**

```python
# OpenAI (default)
from picoagents import OpenAIChatCompletionClient
client = OpenAIChatCompletionClient(model="gpt-4.1-mini")

# Anthropic
from picoagents import AnthropicChatCompletionClient
client = AnthropicChatCompletionClient(model="claude-3-5-sonnet-20241022")

# GitHub Models (free tier)
client = OpenAIChatCompletionClient(
    model="openai/gpt-4.1-mini",
    api_key=os.getenv("GITHUB_TOKEN"),
    base_url="https://models.github.ai/inference"
)

# Local LLM (e.g., Ollama)
client = OpenAIChatCompletionClient(
    model="llama3.2",
    base_url="http://localhost:11434/v1"
)
```

### 웹 UI 실행

![PicoAgents Web UI](./docs/images/picoagents_screenshot.png)

```bash
# Auto-discover agents, orchestrators, and workflows in current directory
picoagents ui

# Or specify a directory
picoagents ui --dir ./examples
```

웹 UI는 코드베이스에서 에이전트, 오케스트레이터, 워크플로를 찾아 이를 실행할 장소를 제공합니다: 스트리밍 채팅, 실시간 디버그 레일, 기록된 실행 이력까지 지원합니다.

여기에는 MCP 서버에 연결하고, 도구를 호출하고, 원시 JSON-RPC 트래픽을 확인할 수 있는 **MCP Playground**와, 데이터셋, 대상, 배치 실행을 위한 **평가 대시보드**도 포함됩니다. 패키지에는 도구, 호출 중 입력, 알림, 대화형 UI, OAuth 보호 접근을 다루는 5개의 데모 MCP 서버가 기본으로 제공됩니다.

### 예시 실행

예시는 접근하기 쉽도록 루트 레벨에 있습니다:

```bash
# Basic agent with tools (Chapter 4)
python examples/agents/basic-agent.py

# Browser automation agent (Chapter 5)
python examples/agents/computer_use.py

# Autonomous orchestration (Chapter 7)
python examples/orchestration/round-robin.py
python examples/orchestration/ai-driven.py

# Production workflow (Chapter 14)
python examples/workflows/yc_analysis/workflow.py
```

## PicoAgents 프레임워크

이 저장소는 두 가지 주요 구성으로 나뉩니다:

### 1. 프레임워크 소스 ([`picoagents/`](picoagents/))

처음부터 만든 완전한 멀티 에이전트 프레임워크:

```
picoagents/
├── src/picoagents/
│   ├── agents/            # Core Agent implementation (Ch 4)
│   │   ├── _agent.py      # Complete agent with streaming, tools, memory
│   │   └── _computer_use/ # Browser automation agents (Ch 5)
│   ├── workflow/          # Type-safe workflow engine (Ch 5)
│   │   ├── core/          # DAG-based execution with streaming
│   │   └── steps/         # Reusable workflow steps
│   ├── orchestration/     # Autonomous coordination (Ch 7)
│   │   ├── _round_robin.py  # Sequential turn-taking
│   │   ├── _ai.py           # LLM-driven speaker selection
│   │   └── _plan.py         # Plan-based orchestration (Magentic One)
│   ├── tools/             # 15+ built-in tools (core, research, coding)
│   ├── eval/              # Evaluation framework (Ch 10)
│   │   ├── judges/        # LLM-as-judge, reference-based
│   │   └── _runner.py     # Test execution and metrics
│   ├── webui/             # Web UI, MCP playground (Ch 8, Ch 12)
│   ├── llm/               # OpenAI and Azure clients
│   ├── memory/            # Memory implementations
│   ├── termination/       # 9 termination conditions
│   └── middleware/        # Extensible middleware system
└── tests/                 # Comprehensive test suite

### 2. Examples ([`examples/`](examples/))
50+ runnable examples organized by chapter:

examples/
├── agents/            # Ch 4-5: Basic agents, tools, computer use
├── memory/            # Ch 4: Long-term memory & RAG patterns
├── mcp/               # Ch 4: Model Context Protocol agents
├── tools/             # Ch 4: Tool creation, approval loops & patterns
├── workflows/         # Ch 6: Sequential, parallel, production workflows
├── orchestration/     # Ch 7: Round-robin, AI-driven, plan-based
├── app/               # Ch 8: Modern Agent UX (FastAPI + SSE)
├── webui/             # Ch 8: Web UI integration examples
├── frameworks/        # Ch 9: Comparisons (LangGraph, AutoGen, etc.)
├── evaluation/        # Ch 10: Agent evaluation patterns
├── notebooks/         # Interactive Jupyter notebooks
├── otel/              # Production: OpenTelemetry & Observability
└── contextengineering/# Production: Context management strategies
```

## 주요 기능

**운영 준비 패턴**

실제 사례 연구로 보여줍니다 ([YC Analysis 워크플로](examples/workflows/yc_analysis/) 참고):

- 비용 최적화: 2단계 필터링으로 LLM 비용 90% 절감
- 타입 안전성: Pydantic 검증을 갖춘 구조화된 출력
- 신뢰성: 체크포인트와 재개 가능한 워크플로
- 고급 추론: 문제 해결을 개선하는 Think 도구 (성능 54% 향상)

**컴퓨터 사용 에이전트**

- Playwright 기반 브라우저 자동화
- 비전 모델을 활용한 멀티모달 추론
- 내장 도구: navigate, click, type, scroll, extract content

**웹 UI 및 CLI**

- 에이전트, 오케스트레이터, 워크플로 자동 발견
- Server-Sent Events 기반 실시간 스트리밍
- 대화 이력을 갖춘 세션 관리
- 실행: `picoagents ui`

**평가 프레임워크**

- LLM-as-judge 평가 패턴
- 기준 기반 검증(정확 일치, 퍼지, 포함)
- 여러 심사를 조합한 합산 점수
- 종합적인 지표 수집

## 프레임워크 비교

PicoAgents의 패턴은 실제 운영 프레임워크에도 잘 옮겨집니다. 이를 보여주기 위해 이 저장소에는 주요 프레임워크별 동등 구현이 포함되어 있습니다:

| 프레임워크 | 예시 | 설명 |
| ----------------------------------------------------------------- | -------------------------------- | --------------------------------------- |
| [Microsoft Agent Framework](examples/frameworks/agent-framework/) | 에이전트, 워크플로, 오케스트레이션 | Microsoft의 에이전트 프레임워크 |
| [Google ADK](examples/frameworks/google-adk/) | 에이전트, 워크플로, 오케스트레이션 | Google의 Agent Development Kit |
| [LangGraph](examples/frameworks/langgraph/) | 에이전트, 워크플로, 오케스트레이션 | LangChain의 그래프 기반 에이전트 프레임워크 |

이 비교는 PicoAgents를 쓰든 LangGraph나 다른 프레임워크를 쓰든 — 도구 호출 에이전트, 순차 워크플로, 라운드 로빈 오케스트레이션 같은 — 핵심 패턴은 동일하다는 점을 보여줍니다. 패턴을 한 번 배우면 어디서나 적용할 수 있습니다.

## 책 구매

**"Designing Multi-Agent Systems: Principles, Patterns, and Implementation for AI Agents"**

이 저장소는 책의 모든 개념을 구현합니다. 책은 효과적인 멀티 에이전트 시스템 구축에 필요한 이론, 설계 트레이드오프, 운영 고려사항을 제공합니다.

- [전자책 구매](https://buy.multiagentbook.com/?utm_source=github&utm_medium=readme)
- [Amazon 페이퍼백](https://www.amazon.com/dp/B0G2BCQQJY)
- [Amazon 하드커버](https://www.amazon.com/dp/B0G2F6T2BZ)

## 질문 및 피드백

책이나 코드에 대한 질문이나 피드백이 있으시면 [이슈를 열어](https://github.com/victordibia/designing-multiagent-systems/issues) 주십시오.

## 인용

```bibtex
@book{dibia2025multiagent,
  title={Designing Multi-Agent Systems: Principles, Patterns, and Implementation for AI Agents},
  author={Dibia, Victor},
  year={2025},
  github={https://github.com/victordibia/designing-multiagent-systems}
}
```
