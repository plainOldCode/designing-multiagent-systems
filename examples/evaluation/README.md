# 멀티 에이전트 평가(evaluation) 스위트

직접 모델 호출, 단일 에이전트(agent), 멀티 에이전트 시스템을 비교하기 위한 종합 평가 프레임워크입니다.

## 빠른 시작

### 하나의 스크립트, 두 가지 모드

`comprehensive-evaluation.py` 스크립트에 모든 것이 통합되어 있습니다:

```bash
# Quick test (3 tasks, 4 configs, ~1 minute) - RECOMMENDED FIRST
python comprehensive-evaluation.py quick

# Full evaluation (10 tasks, 4 configs, ~5-10 minutes)
python comprehensive-evaluation.py full
# or simply:
python comprehensive-evaluation.py
```

**자동 생성:**
- 점수와 **추론 근거**(점수가 그렇게 나온 이유)를 포함한 CSV 결과
- 시각화 차트(성능 대 효율)
- 요약 통계

**결과:**
- `quick_results/quick_results.csv` + `evaluation_results.png`
- `comprehensive_results/comprehensive_results.csv` + `evaluation_results.png`

## 무엇을 테스트하는가

### 구성

1. **Direct-Model** - 기준선 (에이전트 래퍼 없음)
2. **Single-Agent-Tools** - 도구를 갖춘 에이전트 (Calculator, DateTime, Think)
3. **Multi-Agent-RoundRobin** - 고정 순서 팀 (Planner → Solver → Reviewer)
4. **Multi-Agent-AI** - 동적 오케스트레이션(orchestration) (AI가 화자 선택)

### 작업 범주

**빠른 테스트 (작업 3개):**
- 수학 단어 문제
- 계산기 사용
- 논리 퍼즐

**종합 (4개 범주에 걸친 작업 10개):**
- **단순 추론** (작업 3개) - 수학, 논리, 독해
- **도구 집중** (작업 3개) - 실시간 데이터, 계산, 날짜 연산
- **복잡한 계획** (작업 2개) - 다중 제약 최적화
- **검증** (작업 2개) - 팩트 체크, 논증 분석

### 평가 지표

- **종합 점수** (0-10) - 복합 품질 평가
- **정확성(Accuracy)** - 응답의 옳고 그름
- **완전성(Completeness)** - 답변의 꼼꼼한 정도
- **유용성(Helpfulness)** - 실질적 가치
- **명료성(Clarity)** - 소통 품질
- **토큰(Tokens)** - 자원 소비 (입력 + 출력)
- **소요 시간(Duration)** - 실측 시간(ms)
- **LLM 호출 수** - API 호출 횟수

## 결과 해석

### 성능 대 효율

핵심 인사이트: **멀티 에이전트 시스템은 그 오버헤드를 정당화해야 합니다.**

**빠른 테스트의 예:**
```
Configuration    Score    Tokens    Efficiency (pts/1K tok)
Direct-Model     7.4/10   156       47.5
Multi-Agent-RR   7.2/10   2157      3.4
```

**가르침의 순간:** 단순 작업에서 멀티 에이전트는 토큰을 14배 더 쓰면서 점수는 더 낮습니다!

### 멀티 에이전트가 이겨야 할 때

멀티 에이전트 시스템은 다음에서 우위를 보여야 합니다:
- **복잡한 계획** - 다단계 분해
- **도구 집중 작업** - 전문화된 도구 사용
- **검증 작업** - 비평과 검토 주기
- **다중 제약** - 상충하는 요구사항의 균형

### 작업별 분석

패턴을 찾아보십시오:
- 어떤 작업이 멀티 에이전트 조정(coordination)의 이득을 보는가?
- 오케스트레이션 오버헤드가 성능을 해치는 지점은 어디인가?
- 전문 에이전트가 범용 에이전트보다 나은가?

## 구성 튜닝

### 일반적인 조정

**팀이 타임아웃되면:**
```python
# Increase message limits
termination=MaxMessageTermination(max_messages=50)  # was 30

# Increase iterations
max_iterations=15  # was 10
```

**품질이 낮으면:**
```python
# Improve agent instructions
# Add more specific tool guidance
# Adjust evaluation criteria
```

**비용이 너무 높으면:**
```python
# Use fewer evaluation runs
# Reduce task suite size
# Skip expensive composite judges
```

## 적용된 버그 수정

이 평가 스위트는 PicoAgents의 중요한 버그 하나를 발견해 수정했습니다:

**문제:** `LLMEvalJudge`가 잘못된 `BaseEvalJudge` 클래스에서 임포트하던 중
**수정:** `from .._base import BaseEvalJudge` → `from ._base import BaseEvalJudge`
**위치:** `picoagents/src/picoagents/eval/judges/_llm.py:14`

## 다음 단계

1. **빠른 테스트 실행** - 설정 검증 및 파라미터 튜닝
2. **결과 분석** - 패턴과 인사이트 탐색
3. **구성 반복** - 발견 사항에 따른 조정
4. **종합 실행** - 책/논문용 전체 평가
5. **장(chapter) 갱신** - 결과와 시각화 통합

## 파일 구조

```
evaluation/
├── README.md                           # This file
├── comprehensive-evaluation.py         # Main script (quick + full modes, auto-viz)
├── agent-evaluation.py                 # Original example (educational reference)
├── reference-based-evaluation.py       # Judge type demonstrations
├── quick_results/
│   ├── quick_results.csv               # Scores + reasoning
│   └── evaluation_results.png          # Auto-generated charts
└── comprehensive_results/
    ├── comprehensive_results.csv       # Full dataset + reasoning
    └── evaluation_results.png          # Auto-generated charts
```

## 요구 사항

- Azure OpenAI 자격 증명(`AZURE_OPENAI_ENDPOINT`, `AZURE_OPENAI_API_KEY` 설정)
- 선택 사항: 웹 검색 작업용 Google Search API (`GOOGLE_API_KEY`, `GOOGLE_CSE_ID` 설정)
- Python 패키지: `picoagents`, `pandas`, `matplotlib`
