# 에이전트(agent) 최적화(optimization)

11장을 위한 내용으로, 평가(evaluation) 피드백으로부터 에이전트의 구성을 자동으로 개선합니다.
저서 [Designing Multi-Agent Systems](https://buy.multiagentbook.com/?utm_source=github&utm_medium=readme-optimization)의 11장에 대응합니다.

최적화는 지표(metric)를 기준으로 에이전트 구성을 탐색(search)하는 일입니다. 여기에 있는 모든
방법은 동일한 루프입니다 - 에이전트를 실행하고, 채점하고, 변경안을 제안하고, 제안을
시험하고, 반복합니다 - 차이는 무엇을 변형하고, 어떤 신호가 제안을
이끄며, 무엇을 보존하는가뿐입니다.
구현은 [`picoagents.optim`](../../picoagents/src/picoagents/optim/)에 있습니다.

## 빠른 시작

```bash
cd picoagents && pip install -e ".[all]"
cp .env.example .env     # add your Azure OpenAI credentials

cd ../examples/optimization
python optimize-agent.py quick     # ~2 min: watch a score climb
```

최적화는 *능동적*입니다: 모든 후보(candidate)는 사용자의 작업에서 실행되고 채점됩니다.
더 나은지 알 방법이 그것 외에는 없기 때문입니다. 아래 예산(agent budget)은 에이전트
롤아웃(rollout) 단위인데, 돈이 나가는 지점이 바로 그곳입니다.

## 예시

| 파일 | 보여주는 것 | 실행 시간 |
|------|---------------|---------|
| `optimize-agent.py` | 핵심 루프. 실패를 성찰(reflection)하고, 지시문(instruction)을 재작성하고, 획득 이득을 측정. | quick 약 2분 / full 약 20분 |
| `generalization.py` | 학습(train) 분할에서 최적화, 보존된 테스트에서 채점. 규칙을 배운 것인가, 평가 문제를 외운 것인가? | 약 20분 |
| `compare-optimizers.py` | 동일 예산 하의 Reflective vs ReflectivePareto vs MIPRO vs 실제 GEPA. | 방법당 약 20분 |

```bash
python optimize-agent.py full
python generalization.py 240 0            # budget 240, seed 0
python compare-optimizers.py GEPA 0 120   # method, seed, budget
```

결과는 `results/`에 모든 숫자 뒤의 완전한 상세와 함께 기록됩니다: 시도된 각
후보, 각 작업에 대한 에이전트 응답, 판정자(judge)의 점수와 추론 근거. 의도된
설계입니다 -
대화록(transcript)으로 역추적할 수 없는 점수는 증거가 아닙니다.

## 데이터셋

세 스크립트 모두 내장 `support` 데이터셋(고객 지원 작업 46개, 그중 24개가
결정적(deterministic) 채점 대상)을 사용합니다:

```python
from picoagents.eval import load_builtin_dataset
dataset = load_builtin_dataset("support")
train = dataset.filter(lambda t: t.metadata.get("split") == "train")  # 30 tasks
test = dataset.filter(lambda t: t.metadata.get("split") == "test")    # 16 tasks
```

직접 만들 때 중요한 설계 선택:

- **사내 규칙은 루브릭(rubric)에만 존재**하며, 에이전트 프롬프트에는 결코 넣지 않습니다.
  에이전트는 30일 반품 기한이나 `TKT-######` 티켓 형식을 추측할 수 없으므로
  피드백으로부터 배워야 합니다. 진짜 최적화가 메워야 하는 간극이 바로 그것입니다.
- **학습과 테스트는 규칙 가문(rule family)을 공유하되 작업은 다릅니다.** 테스트 작업은
  동일 정책을 새로운 고객 접점에 적용합니다 - 지난달 티켓으로 최적화하고 다음달 티켓에
  배포하는 것처럼. 테스트 점수가 오르는 것은 규칙이 전이(transfer)되었다는 뜻입니다.
- **짝을 이룬 작업이 퇴화적(degenerate) 정책을 차단합니다.** 모든 "이 환불 거절"에
  "이건 승인"이 대응하므로, 무조건 거절하는 법을 배운 에이전트는 낮은 점수를 받습니다.
- **답이 객관적인 곳에서는 결정적 채점**(형식, 산술, 카드 번호 재출력 금지 등)을
  `metadata.deterministic`으로 하고, 모델 판정(judging)은 어조처럼 진정으로 주관적인
  기준에만 씁니다. 판정자 노이즈(judge noise)는 이 실험들에서 분산의 최대 원인이므로,
  규칙을 검증할 수 있는 곳이라면 어디든 제거할 가치가 있습니다.

## 무엇을 최적화할까

`OptimizationSpec`이 최적화자가 바꿔도 되는 표면을 선언합니다. 지시문(instruction)이
기본이지만, 표현하고 교체할 수 있는 것이면 무엇이든 대상이 됩니다:

```python
from picoagents.optim import (
    OptimizationSpec, InstructionTunable, SkillTunable,
    ToolSelectionTunable, ToolDescriptionTunable, CatalogEntry,
)

spec = OptimizationSpec([
    InstructionTunable(),                       # rewrite the system prompt
    SkillTunable(),                             # add/rewrite/remove SKILL.md procedures
    ToolDescriptionTunable(),                   # rewrite tool descriptions
    ToolSelectionTunable([                      # add/remove tools, from a catalog
        CatalogEntry("calculator", "Evaluate arithmetic expressions exactly"),
    ]),
])
```

생성형 튜너블(generative tunable) (지시문, 스킬)은 모델이 새 텍스트를
쓰게 합니다. 카탈로그 튜너블(도구, 모델)은 고정된 집합에서 선택하게 하므로
모든 후보가 구조상 반드시 유효합니다.

## 비용 읽기

최적화가 어디에 돈을 쓰는지에 대한 직관은 대개 틀려 있기 때문에,
모든 결과는 출처별 `CostLog`를 갖습니다:

```python
print(result.cost.summary())
# source           tokens  llm_calls
# agent            48,094         77
# judge            52,406         80
# reflection        4,858          2
```

토큰을 신뢰할 수 있는 숫자로 취급하십시오. `usd` 열은 모델 클라이언트가 채우며
그 가격표만큼만 정확하므로, 실제 비용 수치는 추정을 믿는 대신 토큰 수에 자체 배포
가격을 곱해서 구하십시오.

성찰(reflection) - 루프의 "지능" - 은 통상 청구서의 몇 퍼센트 수준입니다.
후보를 실행하고 *채점하는* 일이 거의 전부이며, 한 번뿐인 최적화 실행과 달리
평가 비용은 측정할 때마다 반복 발생합니다.
