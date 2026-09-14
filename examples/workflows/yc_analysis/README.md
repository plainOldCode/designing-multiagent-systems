# YC 에이전트(agent) 분석 워크플로(workflow)

PicoAgents 패턴을 보여 주는, 프로덕션 사용에 준비된 데이터 분석입니다.

## 핵심 인사이트

• **YC 기업 5,000여 곳 중 234곳 (4.7%)이 이제 AI 에이전트를 구축** (2024 데이터)
• **성장**: 기업 5곳 (2020)에서 기업 234곳 (2024)으로 — 47배 증가
• **최상위 도메인(domain)**: 생산성(Productivity) (89), 건강(Health) (34), 금융(Finance) (28)
• **비용 효율**: 키워드 사전 필터링(pre-filtering)으로 90% 절감

## 엔지니어링 패턴

**2단계 필터링(filtering)**: 키워드 → AI 분류(classification)로 실행당 $4+ 절감
**구조화된 출력(structured output)**: Pydantic 스키마(schema)로 할루시네이션(hallucination) 0
**디스크 체크포인트(disk checkpoint)**: 중단 이후 처리 재개
**독립적 테스트**: 각 단계 단위 테스트 가능

## 빠른 시작

```bash
# Set credentials
export AZURE_OPENAI_ENDPOINT="https://your-resource.openai.azure.com/"
export AZURE_OPENAI_API_KEY="your-key"

# Run analysis
python workflow.py

# Run tests
python test_workflow.py
```

## 파일

- `models.py` - 타입 안정성(type safety)을 위한 Pydantic 스키마(schema)
- `steps.py` - 개별 워크플로(workflow) 함수 (테스트 가능)
- `workflow.py` - 주요 오케스트레이션(orchestration)
- `test_workflow.py` - 각 구성 요소의 단위 테스트
- `data/` - 캐싱(caching) 디렉터리 (gitignored)

생성된 보고서: `./yc_analysis/data/analysis.md`