# MCP 데모 서버(Demo Servers)

패키지 안에 함께 배포되는 다섯 개의 작은 MCP 서버(MCP server)로, `pip install picoagents`를
실행하면 빈 서버 목록이 아니라 동작하는 playground 프리셋(preset)을 얻게 됩니다. 각각
2026-07-28 프로토콜(protocol)의 서로 다른 부분을 시험합니다.

| 서버 | 검증 대상(Exercises) |
|---|---|
| `basic_server.py` | 평범한 도구(tool), 구조화된 출력(structured output), 진행률(progress) 보고 |
| `mrtr_server.py` | `Elicit`/`Resolve`를 통한 MRTR 중-call 입력(mid-call input) |
| `notify_server.py` | 런타임(runtime) 도구 등록부(tool-registry) 변경 (list-changed 알림(notification)) |
| `apps_server.py` | MCP Apps: 호스트(host) 브리지(bridge)로 도구를 역방향 호출하는 인터랙티브 UI |
| `auth_server.py` | OAuth 2.0 보호 리소스(protected resource): 401 챌린지(challenge), RFC 9728 메타데이터(metadata), 베어러(bearer) 토큰(token) |

WebUI MCP Playground에서 원클릭 프리셋(preset)으로 표시됩니다. 하나를 단독으로
실행하려면:

```bash
python -m picoagents.webui.mcp.servers.basic_server           # stdio
python -m picoagents.webui.mcp.servers.basic_server --http    # streamable HTTP
```

`auth_server.py`는 HTTP 전용(stdio에서는 bearer 인증이 의미가 없음)이며, 접속하기
전에 반드시 시작해야 합니다:

```bash
python -m picoagents.webui.mcp.servers.auth_server     # http://127.0.0.1:8931/mcp
```

`Authorization` 헤더(header) 없이 먼저 접속해 통신 내용(wire)에서 401 챌린지(challenge)를
관찰한 뒤, `{"Authorization": "Bearer pico-lab-token"}`를 추가하십시오.

`mcp>=2.0.0` 필요 (`pip install "picoagents[mcp]"`).
