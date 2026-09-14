# 음성 지원 에이전트 어시스턴트

사용자가 AutoGen 에이전트 팀과 대화하고 음성 응답을 받을 수 있는 Chainlit 애플리케이션입니다.

## 기능

- **음성 입력**: 자동 무음 감지로 사용자 음성 수집 (타이핑 입력도 가능)
- **음성-텍스트 변환(Speech-to-Text)**: OpenAI Whisper를 사용해 음성을 텍스트로 변환
- **에이전트 처리**: AutoGen 에이전트 팀으로 요청 처리
- **텍스트-음성 변환(Text-to-Speech)**: OpenAI TTS로 응답을 다시 음성으로 변환
- **음성 맞춤 설정**: 다양한 음성 선택 및 말투 조정

## 설치

1. 이 저장소를 클론합니다
2. 필요한 패키지를 설치합니다:
   ```
   pip install -r requirements.txt
   ```
3. `.env.example`을 `.env`로 복사하고 API 키를 추가합니다:
   ```
   cp .env.example .env
   ```
4. `.env`를 편집해 OpenAI API 키를 입력합니다

## 사용법

1. Chainlit 애플리케이션을 시작합니다:
   ```
   chainlit run app.py
   ```
2. 브라우저에서 `http://localhost:8000`을 엽니다
3. `p`를 눌러 말하기를 시작하거나 요청을 입력합니다
4. 설정 패널로 음성 설정을 조정합니다

## 프로젝트 구조

- `app.py`: 메인 Chainlit 애플리케이션
- `agent_team.py`: AutoGen 팀 구성
- `chainlit.md`: Chainlit 환영 페이지

## 감사의 말

- [Chainlit](https://github.com/Chainlit/chainlit)
- [AutoGen](https://github.com/microsoft/autogen)
- [OpenAI](https://github.com/openai/openai-python)
