# PicoAgents를 위한 메모리 도구(Memory Tool)

## 개요

`MemoryTool`은 Anthropic의 메모리 도구(memory tool)와 유사하게 대화 전반에 걸쳐 지속되는 파일 기반 메모리 저장소(storage)를 제공한다. 에이전트(agent)는 정보 파일을 생성, 읽기, 편집, 정리함으로써 자신의 메모리(memory)를 능동적으로 관리할 수 있다.

## 주요 기능

- **대화 간 학습(cross-conversation learning)**: 메모리는 에이전트 세션(session) 간에 지속된다
- **에이전트 제어(agent-controlled)**: 에이전트가 도구 호출(tool call)을 통해 메모리를 능동적으로 관리한다
- **파일 기반 작업(file-based operations)**: 메모리 파일 생성, 보기, 편집, 삭제, 이름 바꾸기
- **정리된 저장 구조**: 정보를 분류하기 위한 디렉터리(directory) 구조
- **보안(security)**: 경로 탐색(path traversal) 보호로 무단 접근 차단
- **스트리밍(streaming) 지원**: PicoAgents의 스트리밍 아키텍처(architecture)와 호환

## 설치

메모리 도구는 PicoAgents에 포함되어 있다. 추가 의존성(dependency)이 필요 없다.

```python
from picoagents.tools import MemoryTool
```

## 빠른 시작

### 기본 사용법

```python
from picoagents import Agent
from picoagents.llm import AzureOpenAIChatCompletionClient
from picoagents.tools import MemoryTool

# Create memory tool
memory = MemoryTool(base_path="./agent_memory")

# Create agent with memory
agent = Agent(
    name="assistant",
    instructions="""You are a helpful assistant.

IMPORTANT: ALWAYS check your memory directory first:
  Use memory(command="view", path="/memories")

Store important patterns and insights for future reference.""",
    model_client=AzureOpenAIChatCompletionClient(model="gpt-4.1-mini"),
    tools=[memory]
)

# Agent can now use memory across conversations
response = await agent.run("Help me debug this code")
```

### 대화 간 학습

```python
# Session 1: Agent learns pattern
response1 = await agent.run("""
Review this code with a race condition:
```python
self.results = []
for future in as_completed(futures):
    self.results.append(future.result())  # RACE!
```
""")

# Session 2: New conversation, agent applies learned pattern
agent.context = AgentContext()  # Reset context
response2 = await agent.run("""
Review this async code:
```python
self.responses = []
async for item in items:
    self.responses.append(item)  # Similar pattern?
```
""")
# Agent will check memory first and apply stored knowledge
```

## 작업(Operation)

### 1. 디렉터리 또는 파일 보기

디렉터리 내용이나 파일 내용을 선택적 줄 범위와 함께 표시한다.

```python
# View directory
memory(command="view", path="/memories")
# Output: Directory: /memories
#         - notes.md
#         - patterns/

# View file
memory(command="view", path="/memories/notes.md")
# Output:    1: # Meeting Notes
#            2: - Discussed timeline
#            3: - Next steps

# View specific lines
memory(command="view", path="/memories/notes.md", view_range=[2, 3])
# Output:    2: - Discussed timeline
#            3: - Next steps
```

### 2. 파일 생성

파일을 생성하거나 덮어쓴다.

```python
memory(
    command="create",
    path="/memories/patterns/singleton.md",
    file_text="# Singleton Pattern\n\nEnsure only one instance..."
)
```

### 3. 파일 편집 (str_replace)

파일의 텍스트를 치환한다.

```python
memory(
    command="str_replace",
    path="/memories/notes.md",
    old_str="- Discussed timeline",
    new_str="- Discussed project timeline and deadlines"
)
```

### 4. 텍스트 삽입

특정 줄 번호에 텍스트를 삽입한다.

```python
memory(
    command="insert",
    path="/memories/notes.md",
    insert_line=2,
    insert_text="- Team: Alice, Bob, Carol\n"
)
```

### 5. 파일 또는 디렉터리 삭제

파일이나 빈 디렉터리를 제거한다.

```python
# Delete file
memory(command="delete", path="/memories/temp.md")

# Delete empty directory
memory(command="delete", path="/memories/old_patterns")
```

### 6. 이름 바꾸기 또는 이동

파일과 디렉터리의 이름을 바꾸거나 이동한다.

```python
memory(
    command="rename",
    old_path="/memories/draft.md",
    new_path="/memories/final.md"
)

# Move to subdirectory
memory(
    command="rename",
    old_path="/memories/note.md",
    new_path="/memories/archive/note.md"
)
```

## 에이전트 지침

최상의 결과를 위해 에이전트의 시스템 프롬프트(system prompt)에 메모리 지침을 포함하십시오:

```python
agent = Agent(
    instructions="""You are an expert code reviewer.

MEMORY PROTOCOL:
1. ALWAYS check your memory directory FIRST using:
   memory(command="view", path="/memories")

2. Review relevant files before starting work

3. Store important patterns and insights:
   memory(command="create", path="/memories/patterns/bug_type.md", file_text="...")

4. Update memory as you learn:
   memory(command="str_replace", path="...", old_str="...", new_str="...")

5. Organize memory into directories:
   - /memories/patterns/ - Code patterns and best practices
   - /memories/bugs/ - Known bugs and fixes
   - /memories/projects/ - Project-specific notes

ASSUME INTERRUPTION: Your conversation may be reset at any time.
Only information in memory will persist.""",
    tools=[memory]
)
```

## 메모리 구성 모범 사례

### 디렉터리 구조

메모리를 논리적 카테고리로 정리하십시오:

```
/memories/
  ├── patterns/          # Code patterns and best practices
  │   ├── singleton.md
  │   └── race_conditions.md
  ├── bugs/             # Known bugs and fixes
  │   ├── thread_safety.md
  │   └── async_issues.md
  ├── projects/         # Project-specific context
  │   ├── project_a.md
  │   └── project_b.md
  └── users/            # User preferences
      └── preferences.md
```

### 파일 명명

- 서술적 이름 사용: `fix.md` 대신 `race_condition_fix.md`
- 언더스코어(underscore) 또는 하이픈(hyphen) 사용: `user_preferences.md`
- 버전 관리되는 정보에 날짜 포함: `meeting_2025_01_15.md`

### 내용 형식

파싱(parsing)이 쉬운 구조화된 형식을 사용하십시오:

```markdown
# Bug Pattern: Race Condition

## Symptom
Inconsistent results in concurrent operations

## Cause
Multiple threads/coroutines modifying shared state without synchronization

## Solution
1. Use thread-safe data structures (Queue, Lock)
2. Avoid shared mutable state
3. Use futures to collect results

## Examples
- Web scraper with self.results.append()
- API client with self.error_count += 1
```

## 보안 고려사항

### 경로 탐색 보호

메모리 백엔드(backend)는 디렉터리 탐색(directory traversal) 공격을 방지하기 위해 모든 경로를 검증(validation)한다:

```python
# These are blocked:
memory(command="view", path="../../etc/passwd")  # Error!
memory(command="view", path="/memories/../../../secret")  # Error!

# These work:
memory(command="view", path="/memories/notes.md")  # ✓
memory(command="view", path="notes.md")  # ✓
```

### 승인 모드(approval mode)

민감한 애플리케이션(application)의 경우 모든 메모리 작업에 승인을 요구하십시오:

```python
memory = MemoryTool(
    base_path="./memory",
    approval_mode=ApprovalMode.ALWAYS  # Require user approval
)
```

### 민감한 정보

에이전트는 민감한 데이터(data)를 저장하지 말아야 한다. 지침을 추가하십시오:

```python
instructions="""
DO NOT store in memory:
- Passwords or API keys
- Personal identifiable information (PII)
- Credit card numbers
- Confidential business data

Store only:
- Patterns and insights
- Non-sensitive user preferences
- Generic knowledge and best practices
"""
```

## 고급 사용법

### 사용자 정의 메모리 백엔드

사용자 정의 저장소(storage)를 위해 `MemoryBackend`를 확장(extension)하십시오:

```python
from picoagents.tools import MemoryBackend

class DatabaseMemoryBackend(MemoryBackend):
    """Store memory in a database instead of files."""

    def __init__(self, db_connection):
        super().__init__(base_path="./memories")
        self.db = db_connection

    def view(self, path, view_range=None):
        # Query database instead of file
        return self.db.query(path)

    def create(self, path, file_text):
        # Store in database
        self.db.insert(path, file_text)
        return f"Stored in database: {path}"
```

### 오케스트레이션(orchestration)과의 통합

메모리 도구는 모든 오케스트레이션 패턴과 동작한다:

```python
from picoagents.orchestration import RoundRobinOrchestrator

# Create agents with shared memory backend
shared_memory = MemoryBackend(base_path="./team_memory")

researcher = Agent(
    name="researcher",
    tools=[MemoryTool(base_path=shared_memory.base_path)]
)

writer = Agent(
    name="writer",
    tools=[MemoryTool(base_path=shared_memory.base_path)]
)

# Orchestrator coordinates agents with shared memory
orchestrator = RoundRobinOrchestrator(
    agents=[researcher, writer],
    termination=MaxMessageTermination(10)
)
```

## Anthropic 메모리 도구와의 비교

| 기능 | Anthropic | PicoAgents MemoryTool |
|---------|-----------|----------------------|
| **저장소(Storage)** | 클라이언트 측(사용자 관리) | 파일 기반(로컬) |
| **작업(Operations)** | 6개 명령(command) | 6개 명령 (동일) |
| **영속성(Persistence)** | 세션 간(cross-session) | 세션 간(cross-session) |
| **경로 보안(Path Security)** | 필요(구현 부담) | 내장(built-in) |
| **에이전트 제어** | 완전 (파일 생성/편집) | 완전 (파일 생성/편집) |
| **구성(Organization)** | 디렉터리 기반 | 디렉터리 기반 |
| **자동 프롬프트(Auto-prompting)** | 내장 시스템 프롬프트(system prompt) | 수동 (지침에 추가) |
| **백엔드(Backend)** | 사용자 구현 | FileSystem (확장 가능) |

## 예시

완전한 예시는 [examples/tools/memory_tool_example.py](../examples/tools/memory_tool_example.py)를 참조하십시오:

- 대화 간 학습을 갖춘 코드 검토(code review)
- 모든 메모리 작업 시연
- 메모리 구성 패턴

## API 레퍼런스(Reference)

### MemoryTool

```python
class MemoryTool(BaseTool):
    def __init__(
        self,
        base_path: Union[str, Path] = "./memories",
        approval_mode: ApprovalMode = ApprovalMode.NEVER
    )
```

**매개변수(Parameters):**
- `base_path`: 메모리 저장소의 루트(root) 디렉터리 (기본값: "./memories")
- `approval_mode`: 작업에 승인(approval)을 요구할지 여부 (NEVER 또는 ALWAYS)

### MemoryBackend

```python
class MemoryBackend:
    def __init__(self, base_path: Union[str, Path] = "./memories")

    def view(self, path: str, view_range: Optional[List[int]] = None) -> str
    def create(self, path: str, file_text: str) -> str
    def str_replace(self, path: str, old_str: str, new_str: str) -> str
    def insert(self, path: str, insert_line: int, insert_text: str) -> str
    def delete(self, path: str) -> str
    def rename(self, old_path: str, new_path: str) -> str
```

**보안(Security):**
- 모든 경로는 탐색(traversal) 공격을 방지하기 위해 검증(validation)됨
- 경로는 `base_path`를 기준으로 해석(resolve)됨
- 상위(parent) 디렉터리 접근 시도를 하면 `ValueError`가 발생함

## 테스트(Test)

테스트 스위트(test suite)를 실행하십시오:

```bash
pytest tests/test_memory_tool.py -v
```

테스트 범위:
- 모든 메모리 작업
- 경로 보안(path security) 검증
- 오류 처리(error handling)
- 도구(tool) 통합(integration)

## 모범 사례

1. **항상 메모리를 먼저 확인**: 작업을 전에 `/memories`를 보도록 에이전트(agent)에게 지침
2. **카테고리별로 구성**: 서로 다른 종류의 정보를 위해 디렉터리(directory) 사용
3. **구조화된 형식**: 파싱(parsing)이 쉬운 markdown 또는 XML 사용
4. **정기적 정리**: 오래된 정보 삭제
5. **보안(security)**: 자격 증명(credential) 또는 PII는 절대 저장하지 않음
6. **오류 처리**: 에이전트는 누락된 파일을 부드럽게 처리해야 함
7. **버전 관리**: 파일명에 날짜 또는 버전 번호 포함

## 문제 해결(Troubleshooting)

### 메모리가 지속되지 않음

**문제**: 에이전트(agent)가 이전 대화를 기억하지 못함

**해결책**:
- 에이전트 지침에 메모리 확인이 포함되어 있는지 확인
- 세션 간에 `base_path`가 일정한지 검증
- 파일이 실제로 생성되는지 확인 (디렉터리(directory) 검사)

### 경로(path) 오류

**문제**: `ValueError: Access denied: path outside memory directory`

**해결책**: 상대 경로(relative path) 또는 `/memories`로 시작하는 경로를 사용하십시오:
```python
# Good
memory(command="view", path="/memories/notes.md")
memory(command="view", path="notes.md")

# Bad
memory(command="view", path="../../notes.md")
```

### 성능(Performance)

**문제**: 큰 메모리 디렉터리(directory)로 인해 보기(view) 작업이 느려짐

**해결책**:
- 하위 디렉터리(subdirectory)로 구성
- 인덱싱(indexing)을 갖춘 사용자 정의 백엔드(backend) 구현
- 오래된 정보를 주기적으로 아카이브(archive)

## 라이선스(License)

PicoAgents 프레임워크(framework)의 일부. 세부 사항은 저장소 LICENSE를 참조하십시오.
