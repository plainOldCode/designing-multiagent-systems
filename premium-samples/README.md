# 프리미엄 코드 샘플

"Designing Multi-Agent Systems"의 멀티 에이전트(multi-agent) 패턴을 보여주는 완전한 애플리케이션들입니다.

## 프리미엄 샘플이란?

이것은 멀티 에이전트 시스템을 어떻게 만드는지 보여주는 배포 가능한 애플리케이션입니다. 각 샘플에는 다음이 포함됩니다:

- 백엔드 및 프론트엔드 소스 코드
- Docker 구성
- 설정 지침
- 문서

## 이용 권한

프리미엄 샘플은 Professional 등급 도서 번들 구매자에게 제공됩니다.

**[Professional 번들 구매 →](https://buy.multiagentbook.com)**

이미 구매했다면 [buy.multiagentbook.com](https://buy.multiagentbook.com)에 로그인하여 대시보드에서 이용하십시오.

---

## 이용 가능한 샘플

### YC Analysis App

![YC Analysis App](yc-analysis-app/screenshot.jpg)

Y Combinator 기업 5,622곳을 분석하여 AI 에이전트(agent) 트렌드를 찾아냅니다. 필터링을 갖춘 4단계 워크플로(workflow)로 API 비용을 90% 줄입니다.

**다룬 주제:**
- 다단계 워크플로(workflow) 오케스트레이션(orchestration)
- 필터링을 통한 비용 최적화
- Server-Sent Events 기반 실시간 스트리밍(streaming)
- 이벤트 주도 아키텍처
- Docker 배포

**기술 스택:**
- 백엔드: FastAPI, PicoAgents, Python 3.11+
- 프론트엔드: React 19, TypeScript, Vite, Tailwind CSS v4
- 배포: Docker, Docker Compose

**주요 기능:**
- 4단계 워크플로 패턴 시연: Load → Filter → Classify → Analyze
- 2단계 필터링을 통한 비용 절감 방법 시연
- Server-Sent Events 기반 실시간 스트리밍을 보여주는 UI 통합
- 지표 시각화가 포함된 대화형 결과 대시보드
- 과거 실행 갤러리 및 비교
- 체크포인트(checkpoint) 및 재개 기능
- 통합 컨테이너 기반 Docker 배포

**비용 예상:**
- 기업 100곳: ~$0.30
- 기업 500곳: ~$1.50
- 전체 데이터셋(5,622곳): ~$15.00

**관련 장:** 5장(워크플로), 6장(오케스트레이션), 13장(사례 연구)

[📂 전체 README 보기](./yc-analysis-app/) • [📚 문서](https://github.com/victordibia/designing-multiagent-systems/blob/main/premium-samples/yc-analysis-app/README.md)

---

## 공개 예정

더 많은 프리미엄 샘플이 개발 중입니다:

- **Data Visualization Agent** - CSV 파일에서 AI로 차트 생성
- **Multi-Agent Research Assistant** - 문헌 조사를 위한 협업 에이전트
- **Code Review Workflow** - 자동 코드 분석 및 개선 제안

---

## 사용 방법

### 1. 이용 권한 구매
프리미엄 샘플은 Professional 등급 도서 번들($149)에 포함됩니다.

[지금 구매 →](https://buy.multiagentbook.com)

### 2. 다운로드
1. [buy.multiagentbook.com](https://buy.multiagentbook.com)으로 이동
2. "Sign In" 클릭 (오른쪽 상단)
3. 로그인하면 대시보드에서 샘플을 다운로드할 수 있습니다

### 3. 실행
각 샘플에는 Docker 배포용 빠른 시작 안내서가 포함되어 있습니다:

```bash
# Extract the zip
unzip yc-analysis-app.zip
cd yc-analysis-app/

# Configure API keys
cp .env.example backend/.env
# Edit backend/.env with your API credentials

# Start with Docker
docker-compose up --build

# Access at http://localhost:8000
```

---

## 지원

- **문서**: 각 샘플에 상세한 README와 QUICK_START 안내 포함
- **이메일 지원**: support@multiagentbook.com
- **대시보드**: https://buy.multiagentbook.com/dashboard

---

## 라이선스

프리미엄 샘플은 Professional 및 Enterprise 등급 구매자의 상업적 사용을 위해 MIT 라이선스로 제공됩니다. 세부 사항은 개별 샘플의 LICENSE 파일을 참조하십시오.

---

## 메타데이터 API

샘플 정보에 프로그래밍 방식으로 접근하려면 다음을 이용하십시오:

```
https://raw.githubusercontent.com/victordibia/designing-multiagent-systems/main/premium-samples/samples.json
```

이 JSON 파일에는 이용 가능한 모든 샘플의 구조화된 메타데이터가 담겨 있습니다. 제목, 설명, 기술 스택, 기능, 다운로드 정보가 포함됩니다.
