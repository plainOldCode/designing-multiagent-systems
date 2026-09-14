# MCP 튜토리얼: 첫 번째 클라이언트와 서버 만들기

이 폴더에는 MCP 튜토리얼 기사의 전체 코드 예시가 담겨 있습니다.

## 파일

- `app.py` - 서버, 클라이언트, OpenAI 연동이 하나로 합쳐진 완전한 MCP 애플리케이션. 명령줄에서 작업을 받거나 기본값인 "What is the latest news on AI?"를 사용하며 단일 결과를 출력합니다.
- `requirements.txt` - Python 의존성

**참고**: 이 예시에서는 단순성과 쉬운 테스트를 위해 MCP 서버와 클라이언트를 한 파일에 합쳤습니다. 운영 환경에서는 일반적으로 이 둘을 별개의 서비스로 분리합니다.

## 설정

1. 의존성을 설치합니다:

   ```bash
   pip install -r requirements.txt
   ```

2. OpenAI API 키를 설정합니다:
   ```bash
   export OPENAI_API_KEY="your-api-key-here"
   ```

## 애플리케이션 실행

앱을 사용자 지정 작업으로 실행하거나 기본값을 사용하도록 둘 수 있습니다:

```bash
# With a custom task
python app.py "Show me startup news from TechCrunch"

# Or with no arguments (defaults to: What is the latest news on AI?)
python app.py
```

앱은 단일 작업에 대한 결과를 출력한 뒤 종료합니다. 학습, 테스트, 자동화에 최적입니다!

## 전송(Transport) 구성

이 예시에서는 단순성과 쉬운 테스트를 위해 **메모리 내 전송(in-memory transport)**을 사용합니다:

- **통합 방식**: 서버와 클라이언트가 같은 프로세스에서 실행됩니다
- **장점**: 네트워크 구성이 필요 없고, 학습과 개발에 완벽합니다
- **운영 참고**: 운영 배포에서는 HTTP 전송으로 서버와 클라이언트를 분리하는 것을 고려하세요

운영 환경에서는 다음을 원할 수 있습니다:

- `mcp.run(transport="streamable-http")`로 서버를 별도 서비스로 실행
- HTTP/SSE로 클라이언트를 연결하여 확장성과 관심사 분리 개선

## 사용 예시

이 애플리케이션은 명령줄에서 작업을 받아 단일 결과를 반환합니다. 작업이 주어지지 않으면 다음을 기본값으로 사용합니다:

    What is the latest news on AI?

직접 작업을 지정할 수도 있습니다:

    python app.py "Show me startup news from TechCrunch"
    python app.py "Any security news today?"
    python app.py "What's happening in venture capital?"

이 애플리케이션은 OpenAI의 함수 호출(function calling)을 사용해 요청을 바탕으로 어떤 MCP 도구를 쓸지 지능적으로 판단하고, TechCrunch에서 알맞은 뉴스를 가져와 결과를 반환합니다.

## 아키텍처

```
Command Line Task → Host App (OpenAI LLM) → MCP Client (In-Memory) → MCP Server → TechCrunch API
```

호스트 애플리케이션이 전체 흐름을 조율하며, LLM을 사용해 MCP 도구를 언제 어떻게 호출할지 판단합니다. 통신은 같은 프로세스 안에서 메모리 내 전송으로 이루어지므로 학습과 개발에 최적입니다.
