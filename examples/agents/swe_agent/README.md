# 소프트웨어 엔지니어링 에이전트(Software Engineering Agent)

GitHub Copilot, Cursor, Claude Code가 사용하는 **에이전트 + 도구 + 메모리(memory)** 패턴을 시연하는 자율 코딩 에이전트입니다.

## 핵심 개념

**에이전트 능력은 세 가지 구성 요소에서 나옵니다:**
• **도구(Tools)**: 파일 조작, 코드 실행, 메모리, 메타인지(metacognition)
• **프롬프트(Prompts)**: 모범 규준을 인코딩하는 구조화된 워크플로(workflow)
• **메모리(Memory)**: 세션 간에 지속되는 지식

## 엔지니어링 패턴

**5단계 워크플로**: 메모리 확인 → 계획 → 실행 → 학습 → 완료
**정밀 편집(surgical edits)**: 정확한 변경을 위한 `str_replace` 모드
**마크다운(Markdown) 추적**: `/memories/current_task.md`의 체크박스
**명시적 평가(evaluation)**: `TaskStatusTool`이 조기 종료를 방지

## 빠른 시작

```bash
# Set credentials
export AZURE_OPENAI_ENDPOINT="https://your-resource.openai.azure.com/"
export AZURE_OPENAI_API_KEY="your-key"

# Run example
cd examples/agents/swe_agent
python agent.py
```

## 수행 내용

메모리 사용을 보여주는 세 가지 작업을 실행합니다:

**작업 1**: 계산기 모듈 + 테스트 생성
**작업 2**: 거듭제곱 함수 추가 (작업 1의 테스트 패턴 재사용)
**작업 3**: 문서 추가 (학습된 관례 적용)

## 에이전트 아키텍처

**파일 도구**: `read_file`, `write_file` (3가지 모드), `list_directory`, `grep_search`
**실행**: `python_repl`, `bash_execute`
**메모리**: `view`, `create`, `search`, `append`, `str_replace`
**메타인지**: `ThinkTool`, `TaskStatusTool`

**메모리 구조**:
```
agent_memory/
├── patterns/           # Reusable solutions
├── decisions/          # Dated decision logs
├── current_task.md     # Active plan with checkboxes
└── project_context.md  # High-level understanding
```

## 설정

**반복(iteration) 한도** (`Agent` 내부):
- 단순 스크립트: 10-20
- 다중 파일 프로젝트: 30-50 (기본값)
- 복잡한 리팩터링: 50-100

**Bash 타임아웃** (`create_coding_tools` 내부):
- 30초: 빠른 테스트
- 60초: 테스트 스위트 (기본값)
- 120초 이상: 대형 빌드

## 파일

- `agent.py` - 주요 에이전트 설정과 예시 작업
- `scratch/agent_workspace/` - 생성된 코드 (격리됨)
- `scratch/agent_memory/` - 지속 메모리

## 핵심 인사이트

**도구는 필요조건이지 보장이 아닙니다.** 품질은 LLM 능력, 프롬프트 안내, 실행으로부터의 피드백에 달려 있습니다.

**프롬프트는 소프트웨어입니다.** 프롬프트는 워크플로, 모범 규준, 완료 기준을 인코딩합니다. 반복하고 테스트하십시오.

**메모리는 학습을 가능하게 합니다.** 패턴이 쌓이고, 실수가 기록되며, 결정에 근거가 남습니다. 에이전트는 시간이 지날수록 개선됩니다.

**완료에는 명시적 기준이 필요합니다.** 명확한 요구사항 + `TaskStatusTool` + 테스트 = 신뢰할 수 있는 작업 완료.
