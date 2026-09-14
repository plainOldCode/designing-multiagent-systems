# PicoAgents

![PicoAgents Web UI](https://raw.githubusercontent.com/victordibia/designing-multiagent-systems/main/docs/images/picoagents_screenshot.png)

**멀티 에이전트 시스템(multi-agent system)이 제1원리(first principles)에서 어떻게 작동하는지 가르치기 위해 만든 교육용 멀티 에이전트 프레임워크(framework)입니다.**

[Victor Dibia](https://victordibia.com)의 저서 [**"Designing Multi-Agent Systems"**](https://buy.multiagentbook.com/?utm_source=github&utm_medium=readme-picoagents)를 위한 동반 코드입니다. 책의 모든 개념이 명료성과 모범 사례(best practices)를 갖추어 이 코드에 구현되어 있습니다—코드를 읽고 정확히 어떻게 작동하는지 이해하며 배울 수 있습니다.

> **학습용으로 제작**: 이 프레임워크는 성능 최적화보다 코드 명료성과 교육적 가치를 우선합니다.

## 설치

```bash
pip install picoagents
```

**요구 사항:**

- Python 3.10+
- OpenAI API 키(`OPENAI_API_KEY` 환경 변수 설정)

**선택적 추가 기능:**

```bash
pip install "picoagents[web]"      # Web UI, MCP playground, evaluation dashboard
pip install "picoagents[mcp]"      # MCP client (requires mcp>=2.0.0, protocol 2026-07-28)
pip install "picoagents[persist]"  # Run and eval persistence behind the History page
```

## 빠른 시작

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

## 포함된 내용

PicoAgents는 다음 항목들의 완전히 동작하는 예시를 구현합니다:

- **에이전트(Agents)** - 추론 루프(reasoning loop), 도구 호출, 메모리(memory), 미들웨어(middleware), 스트리밍(streaming)
- **워크플로(Workflows)** - 병렬 및 조건부 패턴을 갖춘 타입 안전한(type-safe) DAG 기반 실행
- **오케스트레이션(Orchestration)** - 라운드 로빈(round-robin), AI 주도, 계획 기반의 멀티 에이전트 조정
- **도구(Tools)** - 15개 이상의 내장 도구(파일 작업, 코드 실행, 검색, 할 일(todo), 스킬(skill))
- **MCP** - 2026-07-28 사양(spec)용 클라이언트, 그리고 통신 내용(wire)을 완전히 볼 수 있는 상태로 서버를 테스트하는 놀이터(playground)
- **평가(Evaluation)** - LLM-as-judge 패턴, 참조 기반 검증, 데이터셋과 배치(batch) 실행
- **Web UI** - 자동 발견, 스트리밍 채팅, 실행 이력, MCP 놀이터

## 프로젝트 구조

```
picoagents/
├── src/picoagents/
│   ├── agents/            # Agent implementations (Ch 4-5)
│   ├── workflow/          # Workflow orchestration (Ch 5)
│   ├── orchestration/     # Autonomous coordination (Ch 6)
│   ├── tools/             # Tool system and built-in tools
│   ├── eval/              # Evaluation framework (Ch 8)
│   ├── webui/             # Web interface with auto-discovery
│   ├── llm/               # LLM clients (OpenAI, Azure)
│   ├── memory/            # Memory implementations
│   └── termination/       # Termination conditions
└── tests/                 # Comprehensive test suite
```

## Web UI

에이전트와 워크플로를 자동 발견하는 웹 인터페이스를 실행합니다:

```bash
picoagents ui
```

스트리밍 응답, 실시간 디버그(debug) 이벤트, 세션 관리 기능을 제공합니다.

## 예시

책의 장별로 정리된 50개 이상의 실행 가능한 예시는 [메인 저장소](https://github.com/victordibia/designing-multiagent-systems)를 참조하십시오:

- [`examples/agents/`](https://github.com/victordibia/designing-multiagent-systems/tree/main/examples/agents) - 기본 에이전트, 도구, 메모리, 컴퓨터 사용 (4-5장)
- [`examples/workflows/`](https://github.com/victordibia/designing-multiagent-systems/tree/main/examples/workflows) - 워크플로 패턴과 사례 연구 (5장)
- [`examples/orchestration/`](https://github.com/victordibia/designing-multiagent-systems/tree/main/examples/orchestration) - 멀티 에이전트 조정 (6장)
- [`examples/evaluation/`](https://github.com/victordibia/designing-multiagent-systems/tree/main/examples/evaluation) - 평가 패턴 (8장)

## 책 구하기

<p align="center">
  <a href="https://buy.multiagentbook.com/?utm_source=github&utm_medium=readme-picoagents">
    <img src="https://raw.githubusercontent.com/victordibia/designing-multiagent-systems/main/docs/images/bookcover.png" alt="Designing Multi-Agent Systems Book Cover" width="100%">
  </a>
</p>

**[Designing Multi-Agent Systems: Principles, Patterns, and Implementation for AI Agents](https://buy.multiagentbook.com/?utm_source=github&utm_medium=readme-picoagents)**

이 프레임워크는 책의 모든 개념을 구현합니다. 책은 다음을 제공합니다:

- 각 패턴을 **왜, 언제** 사용하는가
- 설계 결정에 대한 **트레이드오프(trade-off) 분석**
- 완전한 구현을 갖춘 **실세계 사례 연구**
- 시스템 성능 측정을 위한 **평가 전략**

**[→ 전자책 구매](https://buy.multiagentbook.com/?utm_source=github&utm_medium=readme-picoagents)** | **[→ GitHub 저장소](https://github.com/victordibia/designing-multiagent-systems)**

## 개발

```bash
# Clone repository
git clone https://github.com/victordibia/designing-multiagent-systems.git
cd designing-multiagent-systems/picoagents

# Install with dev dependencies
pip install -e ".[dev]"

# Run tests
python -m pytest tests/

# Type checking
python -m mypy src/
python -m pyright src/

# Code formatting
python -m black src/ tests/
python -m isort src/ tests/
```

## 저자

**Victor Dibia** - [웹사이트](https://victordibia.com) | [LinkedIn](https://www.linkedin.com/in/dibiavictor/) | [GitHub](https://github.com/victordibia)

## 인용

```bibtex
@book{dibia2025multiagent,
  title={Designing Multi-Agent Systems: Principles, Patterns, and Implementation for AI Agents},
  author={Dibia, Victor},
  year={2025},
  url={https://buy.multiagentbook.com}
}
```

## 라이선스

MIT 라이선스 - 세부 사항은 LICENSE 파일 참조.

---

**더 알아보기**: [책 웹사이트](https://buy.multiagentbook.com/?utm_source=github&utm_medium=readme-picoagents) | [GitHub](https://github.com/victordibia/designing-multiagent-systems) | [문서](https://github.com/victordibia/designing-multiagent-systems#readme)
