# 메모리(memory) 예시

이 디렉터리는 PicoAgents에서 에이전트(agent) 메모리의 두 가지 서로 다른 접근법을 시연합니다: **에이전트 관리 메모리**(에이전트가 자신의 지식 기반을 적극적으로 통제)와 **애플리케이션 관리 메모리**(개발자가 저장을 통제하고 프레임워크가 컨텍스트를 주입).

## 에이전트 관리 메모리 (MemoryTool)

**파일:** [`memory_tool_example.py`](memory_tool_example.py)

에이전트가 파일 조작을 통해 지속적 지식을 명시적으로 읽고, 쓰고, 정리합니다. 에이전트는 언제 메모리를 확인할지, 무엇을 저장할지, 정보를 어떻게 조직할지를 스스로 결정하며, 한 대화에서 발견한 패턴을 이후 세션에 적용할 수 있는 세션 간 학습을 가능하게 합니다.

메모리 도구는 [Anthropic의 컨텍스트 관리 연구](https://www.anthropic.com/news/context-management)의 아이디어, 특히 파일 기반 메모리 시스템의 아이디어를 활용합니다.

**책 참조:** 4장 4.10절 "Agent-Managed Memory"

## 애플리케이션 관리 메모리 (ListMemory)

**파일:** [`list_memory_example.py`](list_memory_example.py)

개발자가 `memory.add()`를 호출해 정보(사용자 선호, 사실, 대화 요약)를 저장하고, 프레임워크가 `memory.get_context()`를 통해 관련 컨텍스트를 자동으로 검색해 프롬프트에 주입합니다. 에이전트는 이 컨텍스트를 받지만 저장이나 검색을 통제하지는 않습니다.

**책 참조:** 4장 4.9절 "Adding Memory"

## 예시 실행

```bash
# Navigate to examples directory
cd /path/to/designing-multiagent-systems/examples/memory

# Run agent-managed memory example
python memory_tool_example.py

# Run application-managed memory example
python list_memory_example.py
```

두 예시 모두 `AZURE_OPENAI_API_KEY`와 `AZURE_OPENAI_ENDPOINT` 환경 변수가 필요합니다.

## 관련 문서

- [PicoAgents 메모리 문서](../../picoagents/docs/memory.md)
- [책 4장: 첫 번째 에이전트 만들기](../../../../chapters/ch04-building-first-agent.qmd)
