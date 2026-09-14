# MCP 통합(integration) 예시

Model Context Protocol(MCP) 서버(server)를 PicoAgents와 함께 사용하는 방법을 시연하는 예시입니다.

## MCP란?

[Model Context Protocol (MCP)](https://modelcontextprotocol.io)는 애플리케이션이 LLM에게 컨텍스트(context)를 제공하는 방식을 표준화하는 오픈 프로토콜(protocol)입니다. MCP 서버(server)는 다음을 노출합니다:

- **도구(Tools)**: LLM이 작업을 수행하기 위해 호출할 수 있는 함수
- **리소스(Resources)**: 컨텍스트 제공용 데이터 소스(source)
- **프롬프트(Prompts)**: 재사용 가능한 프롬프트 템플릿(template)

PicoAgents는 MCP와 매끄럽게 통합되어, MCP를 준수하는 어떤 서버(server)의 도구가든 PicoAgents의 네이티브 도구처럼 사용할 수 있습니다.

## 설치

```bash
# Install PicoAgents with MCP support
pip install "picoagents[mcp]"   # requires mcp>=2.0.0 (protocol 2026-07-28)

# Or install dependencies separately
pip install picoagents mcp
```

## 예시

### 도구 승인을 포함한 MCP (`basic_mcp_agent.py`)

PicoAgents의 승인(approval) 시스템과 MCP 파일시스템 통합을 함께 보여주는 실용적 예시입니다.

예시는 두 가지 작업을 보여줍니다:
1. **작업 1** (읽기 전용): 디렉터리 내용을 분석하고 정리 권고를 제공 - 승인 없이 자동 실행
2. **작업 2** (쓰기 작업): 샘플 파일 생성 - 실행 전 사용자 승인(approval) 필요

이로써 승인 모드(approval mode)가 민감한 작업에 안전장치(safety layer)를 제공하는 방식을 보여줍니다.

```python
from picoagents.tools import ApprovalMode, StdioServerConfig, create_mcp_tools

# Configure MCP server for a directory
config = StdioServerConfig(
    server_id="filesystem",
    command="npx",
    args=["-y", "@modelcontextprotocol/server-filesystem", "/path/to/dir"]
)

# Create tools
manager, tools = await create_mcp_tools([config])

# Enable approval for write/delete operations
for tool in tools:
    if "write" in tool.name or "delete" in tool.name:
        tool.approval_mode = ApprovalMode.ALWAYS

# Use with agent
agent = Agent(name="agent", tools=tools, ...)

# To use multiple servers, just pass a list:
# configs = [
#     StdioServerConfig(server_id="filesystem", ...),
#     HTTPServerConfig(server_id="weather", url="...", ...),
# ]
# manager, tools = await create_mcp_tools(configs)
```

**실행:**
```bash
# Analyze Desktop (default)
python examples/mcp/basic_mcp_agent.py

# Or specify a custom directory
python examples/mcp/basic_mcp_agent.py ~/Documents
```

**예상 동작:**
- 작업 1은 즉시 실행됩니다 (읽기 전용 작업)
- 작업 2는 잠시 멈추고 사용자에게 승인을 요청합니다:
  ```
  ⚠️  APPROVAL REQUIRED
  ==================================================

  [1] Tool: mcp_filesystem_write_file
      Parameters: {'path': '/Users/you/Desktop/sample.txt', 'content': 'Hello from MCP with approval!'}
      Approve? (y/n):
  ```
- 승인 후 실행이 계속되고 파일이 생성됩니다

## 지원 트랜스포트(transport)

PicoAgents는 모든 MCP 트랜스포트(transport)를 지원합니다:

### Stdio (로컬 서버(server)용)
```python
StdioServerConfig(
    server_id="filesystem",
    command="npx",
    args=["-y", "@modelcontextprotocol/server-filesystem", "/tmp"]
)
```

### Streamable HTTP (프로덕션 권장)
```python
HTTPServerConfig(
    server_id="weather",
    url="http://api.example.com/mcp",
    transport="streamable-http",
    headers={"Authorization": "Bearer token"}
)
```

### SSE (Server-Sent Events)
```python
HTTPServerConfig(
    server_id="github",
    url="http://localhost:3000/sse",
    transport="sse"
)
```

## 사용 가능한 MCP 서버

MCP 커뮤니티는 바로 쓸 수 있는 많은 서버(server)를 제공합니다:

- **Filesystem**: 파일 조작 ([npm](https://www.npmjs.com/package/@modelcontextprotocol/server-filesystem))
- **GitHub**: 저장소(repository) 조작 ([npm](https://www.npmjs.com/package/@modelcontextprotocol/server-github))
- **Google Drive**: Drive 접근 ([npm](https://www.npmjs.com/package/@modelcontextprotocol/server-gdrive))
- **Slack**: Slack 워크스페이스(workspace) 접근 ([npm](https://www.npmjs.com/package/@modelcontextprotocol/server-slack))
- **PostgreSQL**: 데이터베이스(database) 질의(query) ([npm](https://www.npmjs.com/package/@modelcontextprotocol/server-postgres))

더 많은 서버는 [공식 MCP 서버 저장소](https://github.com/modelcontextprotocol/servers)를 참고하십시오.

## 주요 기능

1. **투명한 통합**: MCP 도구는 PicoAgents 네이티브 도구와 완전히 동일하게 동작
2. **다중 서버(server)**: 여러 MCP 서버에 동시에 접속
3. **도구 이름공간(namespacing)**: 충돌 방지를 위해 도구가 서버 ID로 자동 이름공간화
4. **수명주기(lifecycle) 관리**: 정리(cleanup)를 갖춘 간단한 접속/해제
5. **모든 트랜스포트(transport)**: Stdio, SSE, HTTP 트랜스포트 기본 지원

## 아키텍처(architecture)

```
┌─────────────────────────────────────┐
│         PicoAgents Agent            │
└────────────┬────────────────────────┘
             │ uses tools
             ▼
┌─────────────────────────────────────┐
│       MCPTool (Bridge)              │
│  - Wraps MCP tools                  │
│  - Converts formats                 │
└────────────┬────────────────────────┘
             │
             ▼
┌─────────────────────────────────────┐
│      MCPClientManager               │
│  - Manages connections              │
│  - Handles discovery                │
└────────────┬────────────────────────┘
             │
             ▼
┌─────────────────────────────────────┐
│      External MCP Servers           │
│  (filesystem, github, etc.)         │
└─────────────────────────────────────┘
```

## 일반적인 패턴

### 패턴 1: 특정 도메인(domain)용 단일 서버(server)
```python
# Connect to filesystem server
config = StdioServerConfig(
    server_id="fs",
    command="npx",
    args=["-y", "@modelcontextprotocol/server-filesystem", "/workspace"]
)
manager, tools = await create_mcp_tools([config])

# Create domain-specific agent
agent = Agent(
    name="file_agent",
    instructions="You help users manage files in /workspace",
    model_client=model_client,
    tools=tools,
)
```

### 패턴 2: 풍부한 기능 위한 다중 서버(server)
```python
# Connect to multiple specialized servers
configs = [
    StdioServerConfig(server_id="fs", command="npx", args=[...]),
    StdioServerConfig(server_id="github", command="npx", args=[...]),
    HTTPServerConfig(server_id="weather", url="http://...", ...)
]
manager, tools = await create_mcp_tools(configs)

# Create versatile agent
agent = Agent(name="assistant", tools=tools, ...)
```

### 패턴 3: 지연(lazy) 접속
```python
# Register servers without connecting
manager, _ = await create_mcp_tools(configs, auto_connect=False)

# Connect only when needed
await manager.connect("filesystem")
tools = manager.get_tools("filesystem")

# Or use context manager
async with manager.managed_connection("github"):
    tools = manager.get_tools("github")
    # Use tools...
# Automatically disconnected
```

## 문제 해결

### MCP를 사용할 수 없음
```
❌ MCP not installed. Install with: pip install "picoagents[mcp]"   # requires mcp>=2.0.0 (protocol 2026-07-28)
```
**해결:** `pip install "picoagents[mcp]"   # requires mcp>=2.0.0 (protocol 2026-07-28)` 또는 `pip install mcp` 실행

### 서버(server) 접속 실패
```
ConnectionError: Failed to connect to MCP server 'filesystem': ...
```
**해결:** 다음을 확인하십시오:
- 서버 명령이 정확한지 (예: Node 서버(server)의 경우 `npx`가 설치되어 있는지)
- 필요한 환경 변수가 설정되었는지 (API 키 등)
- 서버에 접근 가능한지 (HTTP/SSE 트랜스포트(transport)의 경우)

### 도구가 검색(discovery)되지 않음
**해결:** MCP Inspector로 서버를 직접 테스트해 정상 작동인지 확인하십시오:
```bash
npx @modelcontextprotocol/inspector npx -y @modelcontextprotocol/server-filesystem /tmp
```

## 도구 승인(approval)

PicoAgents의 승인(approval) 시스템은 MCP 도구와 매끄럽게 함께 동작합니다. 민감한 작업 실행 전에 사용자 승인을 요구할 수 있습니다:

```python
from picoagents.tools import ApprovalMode

# Create MCP tools
manager, mcp_tools = await create_mcp_tools([filesystem_config])

# Enable approval for sensitive tools (write, delete operations)
for tool in mcp_tools:
    if "write" in tool.name or "delete" in tool.name:
        tool.approval_mode = ApprovalMode.ALWAYS

# Now the agent will request approval before executing these tools
agent = Agent(name="agent", tools=mcp_tools, ...)
```

도구가 승인을 요구하면:
1. 에이전트(agent)가 도구 호출 세부사항과 함께 `ToolApprovalEvent`를 방출
2. 승인 또는 거절 때까지 실행이 일시 정지
3. 사용자는 파라미터를 살펴보고 진행 여부를 결정할 수 있음
4. 파괴적(destructive) 작업에 추가 안전장치(safety layer) 제공

## 모범 규준

1. **항상 정리(cleanup)**: 작업이 끝나면 `await manager.disconnect_all()` 호출
2. **컨텍스트 관리자(context manager) 사용**: `managed_connection()`으로 자동 정리
3. **이름공간(namespacing) 인지**: 도구가 `mcp_{server_id}_` 접두사로 시작함을 기억
4. **오류(error) 처리**: 견고성(robustness)을 위해 MCP 작업을 try/except로 감싸기
5. **보안(security)**:
   - 파일시스템/데이터베이스(database) 접근 경로와 권한 제한
   - 쓰기/삭제 작업에는 승인 모드(approval mode) 사용
   - 분석 작업에는 읽기 전용 접근 고려

## 더 알아보기

- [MCP 사양(specification)](https://modelcontextprotocol.io/specification)
- [MCP 서버(server) 저장소](https://github.com/modelcontextprotocol/servers)
- [PicoAgents 문서](https://github.com/victordibia/designing-multiagent-systems)
