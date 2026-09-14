# PicoAgents 예시

Victor Dibia의 저서 [Designing Multi-Agent Systems](https://buy.multiagentbook.com/?utm_source=github&utm_medium=readme-examples)를 위한 실행 가능한 예시들입니다. 각 예시는 그 뒤에 있는 이론과 설계 트레이드오프를 설명하는 책의 장에 대응합니다.

## 빠른 시작

```bash
cd picoagents
pip install -e ".[all]"
cp .env.example .env  # Add your OPENAI_API_KEY
cd ..
python examples/agents/basic-agent.py
```

## 장별 예시

| 장 | 디렉터리 | 배우게 되는 것 |
|---------|-----------|-------------------|
| 4장: 첫 번째 에이전트(agent) 만들기 | [`agents/`](agents/) | 도구 사용, 메모리(memory), 구조화된 출력, 미들웨어(middleware) |
| 5장: 워크플로(workflow) | [`workflows/`](workflows/) | 순차, 조건부, 병렬 실행 패턴 |
| 6장: 오케스트레이션(orchestration) | [`orchestration/`](orchestration/) | 라운드 로빈, AI 주도, 계획 기반 멀티 에이전트 조정 |
| 10장: 평가(evaluation) | [`evaluation/`](evaluation/) | LLM-as-judge, 기준 기반 평가, 지표 |
| 11장: 최적화 | [`optimization/`](optimization/) | 평가 피드백으로부터 지시문, 스킬, 도구 최적화 |

## 모든 예시

### agents/
4장의 핵심 에이전트 패턴들.

| 파일 | 설명 |
|------|-------------|
| `basic-agent.py` | 날씨·계산기 도구를 갖춘 단순 에이전트 |
| `memory.py` | 대화 메모리를 갖춘 에이전트 |
| `structured-output.py` | Pydantic 모델을 반환하는 에이전트 |
| `middleware.py` | 요청/응답 미들웨어 파이프라인 |
| `computer_use.py` | 브라우저 자동화 에이전트 |
| `agent_as_tool.py` | 한 에이전트를 다른 에이전트의 도구로 사용 |
| `serialization.py` | 에이전트 상태 저장 및 불러오기 |

### workflows/
6장의 명시적 제어 패턴들.

| 파일 | 설명 |
|------|-------------|
| `sequential.py` | 타입이 지정된 입출력으로 단계 연결 |
| `conditional.py` | 조건부 분기와 팬인 병합 |
| `general.py` | 플루언트 API를 갖춘 확장 순차 파이프라인 |
| `checkpoint_example.py` | 워크플로 상태 저장 및 재개 |
| `data_visualization/` | 완전한 데이터 분석 워크플로 |
| `yc_analysis/` | Y Combinator 스타트업 분석 워크플로 |

### orchestration/
7장의 자율 조정.

| 파일 | 설명 |
|------|-------------|
| `round-robin.py` | 에이전트들이 순서대로 번갈아 발언 |
| `ai-driven.py` | LLM이 다음 화자를 선택 |
| `ai-driven-research.py` | AI가 조정하는 리서치 팀 |
| `plan-based.py` | 오케스트레이터가 계획을 세우고 따름 |

### evaluation/
10장의 테스트와 지표.

| 파일 | 설명 |
|------|-------------|
| `agent-evaluation.py` | LLM-as-judge 평가 |
| `reference-based-evaluation.py` | 기대 출력과 비교 |
| `comprehensive-evaluation.py` | 전체 평가 스위트 |

### optimization/
11장의, 평가 피드백으로부터 에이전트를 자동 개선합니다.

| 파일 | 설명 |
|------|-------------|
| `optimize-agent.py` | 핵심 최적화 루프: 실패를 성찰하고 지시문 재작성 |
| `generalization.py` | 학습 데이터에서 최적화, 보존된 테스트에서 채점: 규칙을 배운 것인가, 평가 문제를 외운 것인가? |
| `compare-optimizers.py` | 동일한 예산에서 Reflective, Pareto, MIPRO, 실제 GEPA 비교 |

### 기타 예시

| 디렉터리 | 설명 |
|-----------|-------------|
| `tools/` | 도구 정의와 승인 패턴 |
| `memory/` | 메모리 구현 (list, tool-based) |
| `mcp/` | Model Context Protocol 통합 |
| `otel/` | OpenTelemetry 관측성(observability) |
| `webui/` | 웹 UI 예시 |
| `app/` | 풀스택 애플리케이션 예시 |
| `contextengineering/` | 컨텍스트 윈도우 관리 전략 |
| `frameworks/` | 다른 프레임워크에서의 동일 패턴 (LangGraph, Google ADK 등) |

## 프레임워크 비교

[`frameworks/`](frameworks/) 디렉터리는 다음 전반의 동등 구현을 보여줍니다:

- **agent-framework/** - Microsoft Agent Framework
- **langgraph/** - LangChain LangGraph
- **google-adk/** - Google Agent Development Kit
- **claude-agent-sdk/** - Anthropic Claude Agent SDK

각 디렉터리는 비교를 위해 동일한 에이전트/워크플로/오케스트레이션 패턴을 구현합니다.
