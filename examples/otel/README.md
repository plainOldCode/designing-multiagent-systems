# OpenTelemetry 통합(integration) 예시

이 예시는 OpenTelemetry를 사용해 PicoAgents에서 자동 원격 측정(telemetry)을 수집하는 방법을 시연합니다.

## 무엇이 계측(instrument)되는가?

OpenTelemetry를 활성화하면 PicoAgents가 자동으로 다음을 수집합니다:

- **추적(Traces)**: 에이전트(agent) 작업, LLM 호출, 도구 실행의 스팬(span)
- **미터(Metrics)**: 토큰 사용량 히스토그램(histogram)과 작업 소요 시간
- **시맨틱 컨벤션(Semantic Conventions)**: OpenTelemetry Gen-AI 표준을 따름

## 빠른 시작

### 1. 의존성 설치

```bash
# From the repository root
cd picoagents
pip install -e ".[otel]"
```

### 2. Jaeger 시작

```bash
# From the repository root
cd examples/otel
docker-compose up -d
```

다음에 Jaeger가 시작됩니다:
- UI: http://localhost:16686
- OTLP 엔드포인트(endpoint): http://localhost:4318

### 3. 환경 변수 설정

```bash
export PICOAGENTS_ENABLE_OTEL=true
export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318
export OTEL_SERVICE_NAME=picoagents-example
export OPENAI_API_KEY=your-api-key
```

### 4. 예시 실행

```bash
python agent_with_telemetry.py
```

### 5. Jaeger에서 추적 보기

1. http://localhost:16686 열기
2. 서비스 선택: `picoagents-example`
3. "Find Traces" 클릭
4. 추적(trace) 계층 구조 탐색

## 볼 수 있는 것

### 추적(Trace) 계층 구조
```
agent weather_assistant
├─ chat gpt-4o-mini
│  └─ Attributes: gen_ai.usage.input_tokens, gen_ai.usage.output_tokens
├─ tool get_weather
│  └─ Attributes: gen_ai.tool.name, gen_ai.tool.success
└─ chat gpt-4o-mini
   └─ Final response with usage stats
```

### 미터(Metrics) (Jaeger에서)
- `gen_ai.client.token.usage`: 토큰 소비 히스토그램(histogram)
- `gen_ai.client.operation.duration`: 작업 지연(latency)

## 환경 변수

| 변수 | 기본값 | 설명 |
|----------|---------|-------------|
| `PICOAGENTS_ENABLE_OTEL` | `false` | OpenTelemetry 활성화 |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | `http://localhost:4318` | OTLP 엔드포인트(endpoint) URL |
| `OTEL_SERVICE_NAME` | `picoagents` | 추적(trace)용 서비스 이름 |

## 정리

```bash
docker-compose down
```

## 다른 백엔드와 함께 사용

PicoAgents는 OTLP 호환 백엔드라면 무엇과든 동작합니다:

### Datadog
```bash
export OTEL_EXPORTER_OTLP_ENDPOINT=https://api.datadoghq.com
export DD_API_KEY=your-datadog-api-key
```

### Honeycomb
```bash
export OTEL_EXPORTER_OTLP_ENDPOINT=https://api.honeycomb.io
export HONEYCOMB_API_KEY=your-api-key
```

### New Relic
```bash
export OTEL_EXPORTER_OTLP_ENDPOINT=https://otlp.nr-data.net
export NEW_RELIC_LICENSE_KEY=your-license-key
```

## 더 알아보기

- [OpenTelemetry Gen-AI 컨벤션(Conventions)](https://opentelemetry.io/docs/specs/semconv/gen-ai/)
- [Jaeger 문서](https://www.jaegertracing.io/docs/)
- [PicoAgents 미들웨어(Middleware)](../../picoagents/src/picoagents/_middleware.py)
