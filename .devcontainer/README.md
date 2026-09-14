# GitHub Codespaces 구성

이 디렉터리에는 GitHub Codespaces 구성이 들어 있어, 브라우저에서 클릭 한 번으로 개발 환경을 열 수 있습니다.

## 포함된 내용

### 컨테이너 구성 (`devcontainer.json`)

- **기본 이미지**: Python 3.11 (Debian Bullseye)
- **VS Code 확장 프로그램**:
  - Python 언어 지원
  - Pylance (고급 IntelliSense)
  - Black 포매터
  - Ruff 린터
  - Jupyter 노트북
- **포트 포워딩**:
  - 8000: PicoAgents WebUI
  - 8080: 개발 서버
- **자동 설정**: 컨테이너 생성 시 `setup.sh` 실행

### 설정 스크립트 (`setup.sh`)

자동으로 설치합니다:
1. 모든 의존성을 포함한 PicoAgents (`[all]` extras)
2. Playwright 브라우저 (컴퓨터 사용 에이전트(agent)용)
3. 템플릿에서 `.env` 파일 생성

## 사용법

### Codespaces 시작

1. 메인 README의 "Open in GitHub Codespaces" 배지 클릭
2. 컨테이너 빌드 대기 (첫 실행 시 약 2-3분)
3. `picoagents/.env`에 `OPENAI_API_KEY` 추가
4. 바로 코딩 시작!

### 빠른 명령

```bash
# Run an example
python examples/agents/basic-agent.py

# Launch Web UI
picoagents ui

# Run tests
cd picoagents && pytest tests/

# Type checking
cd picoagents && python -m mypy src/
```

## 커스터마이징

### VS Code 확장 추가

`devcontainer.json` → `customizations.vscode.extensions` 수정:

```json
"extensions": [
  "ms-python.python",
  "your.extension.id"
]
```

### 시스템 패키지 추가

`setup.sh`를 수정하여 `apt-get install` 명령을 추가하십시오.

### Python 버전 변경

`devcontainer.json` → `image` 수정:

```json
"image": "mcr.microsoft.com/devcontainers/python:1-3.10-bullseye"
```

## 문제 해결

### 설정 스크립트가 실패할 때

직접 다시 실행하십시오:
```bash
bash .devcontainer/setup.sh
```

### API 키가 없을 때

셸 세션에 추가:
```bash
export OPENAI_API_KEY='your-key-here'
```

또는 `picoagents/.env`에 추가:
```bash
echo "OPENAI_API_KEY=your-key-here" >> picoagents/.env
```

### Playwright 브라우저가 설치되지 않은 경우

```bash
playwright install chromium --with-deps
```

## 비용

- **무료 등급**: 개인 GitHub 계정 기준 월 60시간
- **Pro 등급**: 월 180시간
- **과금**: 사용 시간(분) 단위로 부과

자세히 알아보기: https://docs.github.com/en/billing/managing-billing-for-github-codespaces

## 로컬 개발 컨테이너

이 구성은 VS Code의 Dev Containers 확장으로도 동작합니다:

1. [Dev Containers 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) 설치
2. VS Code에서 저장소 열기
3. 명령 팔레트 → "Dev Containers: Reopen in Container"
