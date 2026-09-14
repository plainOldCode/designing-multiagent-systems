# 고급 MCP 서버 구현

이 폴더에는 단순한 요청-응답 패턴을 넘어선 정교한 기능들을 보여주는 고급 MCP 서버 구현이 담겨 있습니다.

## 포함된 내용

이 서버는 다음을 선보입니다:

- **인터랙티브 · 장시간 실행 도구(Interactive & Long-Running Tools)**: 장시간 실행, 사용자 입력 요청, 실시간 진행 상황 업데이트가 가능한 도구
- **리소스 관리(Resource Management)**: 실시간 업데이트를 위한 클라이언트 구독 기능이 있는 서버 측 리소스
- **프롬프트(Prompts)**: 동적 인자를 지원하는 재사용 가능한 LLM 상호작용 템플릿
- **세션 재개(Session Resumption)**: 중단된 작업 재개를 위한 완전한 이벤트 스토어(event store) 지원

## 시연되는 주요 기능

### 도구
- **입력 요청(Elicitation)**: 일시정지하여 구조화된 사용자 입력을 요청하는 도구
- **샘플링(Sampling)**: 실행 중 LLM의 도움을 요청하는 도구
- **진행 알림(Progress Notifications)**: 장시간 실행 작업의 실시간 상태 업데이트
- **취소(Cancellation)**: 중단된 도구 호출의 안전한 처리
- **어노테이션(Annotations)**: 클라이언트가 도구 동작을 이해하도록 돕는 메타데이터

### 리소스
- 서버 측 리소스 정의 및 관리
- 클라이언트 작업: 리소스 나열, 읽기, 구독
- 리소스 변경 시 실시간 알림

### 고급 기능
- 세션 재개를 위한 이벤트 스토어
- 전송(Transport) 보안 설정
- 포괄적인 오류 처리

## 블로그 게시물

이 개념들과 구현 세부 사항에 대한 상세한 설명은 함께 제공되는 블로그 게시물을 참고하세요:

**[MCP For Software Engineers | Part 2: Interactive & Long-Running Tools](https://newsletter.victordibia.com/p/mcp-for-software-engineers-part-2)**

이 게시물에서는 이러한 고급 MCP 기능의 이론적 배경을 설명하고 단계별 구현 안내를 제공합니다.

## 서버 실행

```bash
# Navigate to the advanced directory
cd mcp/advanced

# Start the server with event store support (default)
python -m server.server

# Start on a different port
python -m server.server --port 8007

# Start without event store (no resumption support)
python -m server.server --no-event-store
```

서버는 `http://127.0.0.1:8006/mcp` (또는 지정한 포트)에서 사용할 수 있습니다.

## 관련 자료

- [Part 1: Getting Started with MCP](https://newsletter.victordibia.com/p/mcp-for-software-engineers-part-1)
- [MCP Python SDK Documentation](https://github.com/modelcontextprotocol/python-sdk)
- [MCP Specification](https://spec.modelcontextprotocol.io/)