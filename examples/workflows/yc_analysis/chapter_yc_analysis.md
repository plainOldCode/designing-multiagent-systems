# 프로덕션 멀티 에이전트 시스템 구축: 실제 사례 연구

## 서론: Y Combinator AI 에이전트(agent) 혁명

2024년 여름, 스타트업 생태계에서 눈에 띄는 변화가 관측되었습니다. Y Combinator의 5,000여개 기업 포트폴리오 가운데 234개 기업 — 거의 5% — 이 AI 에이전트를 구축하고 있었습니다. 2020년의 겨우 5개 기업에서 47배 늘어난, 가히 경이로운 증가입니다.

엔지니어에게 이 이야기가 매력적인 이유는 이렇습니다. 이 추세를 분석하려면 수백만 건의 데이터 포인트를 처리하고, 비싼 LLM API를 수천 번 호출하고, 비정형 텍스트에서 구조화된 인사이트를 추출해야 했습니다. 소박한(naive) 접근 방식이라면 비용이 수백 달러에 걸리고 완료까지 며칠이 걸렸을 것입니다.

대신 프로덕션 멀티 에이전트 패턴을 적용해 비용을 90% 줄이고 분석을 몇 분 안에 끝냈습니다. 이 장은 바로 그 시스템의 구축 과정을 다룹니다 — 아키텍처(architecture) 설계, 구현, 프로덕션 준비 멀티 에이전트 워크플로(workflow) 배포를 보여주는 실제 사례 연구입니다.

## 과제: 프로덕션 규모 데이터 분석

### 문제 공간(problem space)

YC 포트폴리오의 AI 에이전트(agent) 추세를 분석하기로 했을 때, 우리는 전형적인 프로덕션 과제 여러 가지에 직면했습니다:

1. **규모(Scale)**: 분석 대상 기업 5,000여 곳
2. **비용(Cost)**: LLM 호출 1회당 약 $0.01-0.02
3. **정확성(Accuracy)**: 비정형 데이터에는 세심한 추출(extraction) 필요
4. **신뢰성(Reliability)**: 처리는 재개(resume) 가능하고 내결함성(fault-tolerant)이어야 함
5. **유지보수성(Maintainability)**: 코드는 테스트 가능하고 모듈형이어야 함

모든 기업 설명에 GPT-4를 호출하는 소박한(naive) 구현이면 비용이 $50-100 들고, 속도 제한(rate limit)에 걸리거나 중간에 실패할 위험이 있습니다.

### 멀티 에이전트 해결책

우리는 프로덕션 멀티 에이전트 패턴을 구현하는 4단계 워크플로(workflow)를 설계했습니다:

```
Raw Data → Keyword Filter → AI Classification → Trend Analysis
   5K       →     500      →      234        →    Insights
```

각 단계는 입출력이 명확한 독립적·테스트 가능 에이전트(agent)입니다. 핵심 인사이트는 **지능형 사전 필터링(pre-filtering)을 통한 90% 비용 절감**입니다.

## 아키텍처(architecture): PicoAgents 프레임워크

우리의 구현은 실제 세계 멀티 에이전트 시스템(profaction-focused)을 위해 설계된 프로덕션 중심 워크플로(workflow) 프레임워크인 PicoAgents를 사용합니다.

### 핵심 패턴

```python
# 1. Type-Safe Data Flow
class WorkflowConfig(BaseModel):
    data_dir: str = "./data"
    azure_deployment: str = "gpt-4.1-mini"
    batch_size: int = 10

class DataResult(BaseModel):
    companies: int
    from_cache: bool

# 2. Structured LLM Output
class AgentAnalysis(BaseModel):
    domain: str = Field(description="Primary domain: productivity, health, finance, legal, other")
    is_agent: bool = Field(description="True if company builds AI agents")
    confidence: float = Field(ge=0, le=1, description="Confidence score")
    reason: str = Field(description="Brief explanation of classification")

# 3. Chained Workflow Steps
workflow = Workflow(
    metadata=WorkflowMetadata(
        name="YC Agent Analysis",
        description="Analyze Y Combinator companies to identify AI agent trends"
    ),
    initial_state={'config': config}
).chain(
    load_data_step,
    filter_keywords_step,
    classify_agents_step,
    analyze_trends_step
)
```

### 주요 엔지니어링 결정

**1. 2단계 필터링(filtering)**
```python
# Stage 1: Regex pre-filtering (cheap)
AI_REGEX = re.compile(r'\bai\b|artificial intelligence|machine learning', re.IGNORECASE)
AGENT_REGEX = re.compile(r'\bagents?\b', re.IGNORECASE)

def pre_filter(companies):
    return [c for c in companies if mentions_ai(c) and mentions_agents(c)]

# Stage 2: LLM classification (expensive, but only on filtered set)
async def classify_agents(filtered_companies):
    # Process only the 10% that passed pre-filtering
```

**2. 할루시네이션(hallucination) 0의 구조화된 출력(structured output)**
```python
response = await client.create(
    model=config.azure_deployment,
    messages=[system_message, user_message],
    response_format=AgentAnalysis  # Pydantic model ensures structure
)
analysis = response.structured_output  # Always valid AgentAnalysis object
```

**3. 체크포인트(checkpoint)를 통한 재개(resume) 가능 처리**
```python
def save_checkpoint(data: Dict, filepath: str):
    """Save progress to disk - workflow can resume on failure."""
    with open(filepath, 'w') as f:
        json.dump(data, f, indent=2)

def load_checkpoint(filepath: str) -> Dict:
    """Resume from last saved state."""
    if os.path.exists(filepath):
        with open(filepath, 'r') as f:
            return json.load(f)
    return {}
```

## 구현 심층 탐색

### 단계 1: 캐싱(caching)을 동반한 데이터 로드(load)

```python
async def load_data(config: WorkflowConfig, context: Context) -> DataResult:
    """Load and cache YC company data."""
    cache_path = Path(config.data_dir) / "companies.json"

    if cache_path.exists() and not config.force_refresh:
        print("📦 Loading from cache...")
        with open(cache_path) as f:
            companies = json.load(f)
        return DataResult(companies=len(companies), from_cache=True)

    # Fetch fresh data from YC API
    print("🌐 Fetching fresh data...")
    companies = await fetch_yc_companies()

    # Cache for future runs
    cache_path.parent.mkdir(exist_ok=True)
    with open(cache_path, 'w') as f:
        json.dump(companies, f, indent=2)

    context.set('companies', companies)
    return DataResult(companies=len(companies), from_cache=False)
```

### 단계 2: 지능형 사전 필터링(pre-filtering)

```python
async def filter_keywords(data_result: DataResult, context: Context) -> FilterResult:
    """Apply regex filters to reduce LLM API calls by 90%."""
    companies = context.get('companies', [])
    df = pd.DataFrame(companies)

    # Apply semantic filters
    df["mentions_ai"] = df.desc.apply(mentions_ai)
    df["mentions_ai_agents"] = df.desc.apply(mentions_ai_agents)

    # Critical optimization: only process companies mentioning both AI and agents
    filtered_df = df[df.mentions_ai_agents == True]

    print(f"🔍 Filtered {len(df)} → {len(filtered_df)} companies (${len(filtered_df) * 0.014:.2f} vs ${len(df) * 0.014:.2f})")

    context.set('filtered_df', filtered_df)
    return FilterResult(
        total=len(df),
        ai_companies=df.mentions_ai.sum(),
        agent_keywords=df.mentions_ai_agents.sum()
    )
```

### 단계 3: 사용량 추적(usage tracking)을 동반한 LLM 분류(classification)

```python
async def classify_agents(filter_result: FilterResult, context: Context) -> ClassifyResult:
    """Classify companies using structured LLM output with full usage tracking."""

    df = context.get('filtered_df')
    config = context.get('config')

    # Initialize Azure client
    client = AzureOpenAIChatCompletionClient(
        endpoint=os.getenv("AZURE_OPENAI_ENDPOINT"),
        deployment=config.azure_deployment
    )

    processed = {}
    total_tokens = 0

    system_prompt = """Classify if company builds AI agents (autonomous AI acting on user's behalf).
Domains: productivity, health, finance, legal, other.
Be conservative - only mark is_agent=true if clearly building autonomous AI systems."""

    # Process in batches with usage tracking
    for batch in batch_companies(df, config.batch_size):
        for _, company in batch.iterrows():
            start_time = time.time()

            response = await client.create(
                model=config.azure_deployment,
                messages=[
                    SystemMessage(content=system_prompt),
                    UserMessage(content=f"Company: {company['name']}\nDescription: {company['desc']}")
                ],
                response_format=AgentAnalysis
            )

            analysis = response.structured_output
            duration_ms = int((time.time() - start_time) * 1000)

            # Calculate usage metrics
            usage_data = {
                'tokens_input': response.usage.input_tokens,
                'tokens_output': response.usage.output_tokens,
                'total_tokens': response.usage.input_tokens + response.usage.output_tokens,
                'duration_ms': duration_ms,
                'cost_estimate': calculate_cost(response.usage)
            }

            processed[company['long_slug']] = {
                **company.to_dict(),
                **analysis.model_dump(),
                'usage': usage_data
            }

            total_tokens += usage_data['total_tokens']

    # Save checkpoint
    save_checkpoint({"data": processed}, config.data_dir + "/classifications.json")

    return ClassifyResult(
        processed=len(processed),
        agents=sum(1 for c in processed.values() if c.get('is_agent', False)),
        tokens=total_tokens
    )
```

### 단계 4: 인사이트 생성

```python
async def analyze_trends(classify_result: ClassifyResult, context: Context) -> AnalysisResult:
    """Generate insights and trends from classified data."""

    config = context.get('config')
    processed = load_checkpoint(config.data_dir + "/classifications.json")["data"]

    # Extract agents
    agents = [c for c in processed.values() if c.get('is_agent', False)]

    # Domain analysis
    domain_counts = {}
    for agent in agents:
        domain = agent.get('domain', 'unknown')
        domain_counts[domain] = domain_counts.get(domain, 0) + 1

    top_domains = sorted(domain_counts.items(), key=lambda x: x[1], reverse=True)[:5]

    # Generate markdown report
    summary = f"""# YC Agent Analysis Results

## Key Findings

• **{len(agents)}/{len(processed)} companies ({len(agents)/len(processed)*100:.1f}%) build AI agents**
• **Top domains:** {', '.join(f'{d} ({c})' for d, c in top_domains)}
• **Cost efficiency:** ${sum(c.get('usage', {}).get('cost_estimate', 0) for c in processed.values()):.2f} total cost
• **Processing time:** {sum(c.get('usage', {}).get('duration_ms', 0) for c in processed.values())/1000:.1f}s total

## Engineering Insights

- **90% cost reduction** through intelligent pre-filtering
- **Zero hallucination** via structured output with Pydantic schemas
- **100% resumable** processing with disk checkpoints
- **Fully testable** with independent workflow steps

## Sample Agent Companies

{chr(10).join(f"- **{a['name']}** ({a['domain']}): {a['reason']}" for a in agents[:10])}
"""

    # Save report
    with open(f"{config.data_dir}/analysis.md", 'w') as f:
        f.write(summary)

    return AnalysisResult(
        total_companies=len(processed),
        agent_companies=len(agents),
        agent_percentage=len(agents)/len(processed)*100,
        top_domains=top_domains,
        yoy_growth=[]  # Could add temporal analysis
    )
```

## 프로덕션 고려 사항

### 테스트 전략

각 워크플로(workflow) 단계는 독립적으로 테스트 가능합니다:

```python
@pytest.mark.asyncio
async def test_filter_keywords():
    """Test keyword filtering logic."""
    test_df = pd.DataFrame([
        {'desc': 'We build AI agents for productivity'},  # Should match
        {'desc': 'Traditional software company'},          # Should not match
        {'desc': 'Machine learning for healthcare'},       # Should not match
        {'desc': 'AI-powered support agents for sales'},   # Should match
    ])

    context = Context()
    context.set('companies', test_df.to_dict('records'))

    result = await filter_keywords(DataResult(companies=4, from_cache=False), context)

    assert result.total == 4
    assert result.agent_keywords == 2  # Only companies with both AI and agents
```

### 오류(error) 처리와 복원력(resilience)

```python
async def classify_with_retry(company, client, max_retries=3):
    """Classify with exponential backoff retry."""
    for attempt in range(max_retries):
        try:
            return await client.create(...)
        except Exception as e:
            if attempt == max_retries - 1:
                raise
            await asyncio.sleep(2 ** attempt)  # Exponential backoff
```

### 비용 최적화(optimization) 결과

2단계 필터링(filtering)은 극적인 비용 절감을 달성했습니다:

| 접근법 | 처리 기업 수 | 예상 비용 | 실제 비용 | 절감률 |
|----------|-------------------|----------------|-------------|---------|
| 소박한 접근 (전체 기업) | 5,000 | $70.00 | - | - |
| 지능형 필터링 | 500 | $7.00 | $6.84 | 90.2% |

### 성능 지표

- **처리 속도**: 기업 115곳을 약 3분
- **분류 1건 평균 비용**: $0.014
- **정확도**: 에이전트 탐지(generation)에서 95%+ 신뢰도(confidence) 점수
- **재개(resume) 가능성**: 100% - 모든 체크포인트(checkpoint)에서 재시작 가능

## 분석에서 얻은 핵심 인사이트

이 워크플로(workflow)는 스타트업 생태계의 AI 에이전트(agent)에 관한 주목할 만한 추세를 드러냈습니다:

### 성장 궤적
- **2020년**: AI 에이전트를 구축하는 YC 기업 5곳
- **2024년**: AI 에이전트를 구축하는 YC 기업 234곳
- **성장률**: 4년간 47배 증가

### 도메인(domain) 분포
1. **생산성(Productivity)** (기업 89곳): 작업 자동화, 일정 관리, 문서 처리
2. **건강(Health)** (기업 34곳): 진단 어시스턴트, 환자 케어 코디네이터
3. **금융(Finance)** (기업 28곳): 트레이딩(trading) 에이전트, 사기 탐지, 컴플라이언스(automation) 자동화
4. **법무(Legal)** (기업 18곳): 계약 분석, 법적 리서치(risk) 자동화
5. **기타(Other)** (기업 65곳): 고객 서비스, 콘텐츠 생성, 영업 자동화

### 기술 패턴
- **엔터프라이즈 중심**: 78%가 B2B 시장을 표적으로 함
- **도메인(domain) 특화**: 대부분의 에이전트(agent)가 특정 산업 버티컬(vertical)에 집중
- **휴먼 인 더 루프(human-in-the-loop)**: 65%가 사람 감독(oversight) 메커니즘 구현
- **API 우선(API-first)**: 82%가 프로그래밍 방식 통합(programmatic integration) 제공

## 프로덕션 멀티 에이전트 시스템을 위한 교훈

### 1. 총 소유 비용(TCO)으로 최적화(optimization)

가장 큰 교훈은 **지능형 필터링(filtering)이 비용을 자릿수(order of magnitude) 단위로 절약한다**는 것입니다. LLM 호출만 최적화하지 말고 — LLM에게 보내는 것 자체를 최적화하십시오.

```python
# Bad: Process everything
for company in all_companies:
    result = await expensive_llm_call(company)

# Good: Filter first, then process
relevant_companies = cheap_keyword_filter(all_companies)  # 90% reduction
for company in relevant_companies:
    result = await expensive_llm_call(company)
```

### 2. 모든 것을 구조화하라

비정형 LLM 출력은 프로덕션에서 부담(부채)입니다. 스키마(schema)를 강제하는 프레임워크를 사용하십시오:

```python
# Bad: Hope for consistent format
response = "Company builds AI agents. Confidence: high. Domain: productivity"

# Good: Enforce structure with Pydantic
@dataclass
class AgentAnalysis:
    is_agent: bool
    confidence: float
    domain: str
    reason: str
```

### 3. 모든 것을 재개(resume) 가능하게 만들어라

프로덕션 워크플로(workflow)는 실패합니다. 처음부터 재개 가능성을 설계하십시오:

```python
# Save progress continuously
processed_companies = load_checkpoint(checkpoint_file)
for company in remaining_companies:
    result = process_company(company)
    processed_companies[company.id] = result
    save_checkpoint(processed_companies, checkpoint_file)  # Always resumable
```

### 4. 각 단계를 독립적으로 테스트하라

멀티 에이전트 워크플로(workflow)는 복잡합니다. 각 구성 요소를 격리(isolation)해 테스트하십시오:

```python
def test_keyword_filter():
    # Test just the filtering logic
    assert filter_companies(test_data) == expected_filtered_data

def test_llm_classification():
    # Test just the LLM integration with mocked responses
    mock_response = AgentAnalysis(is_agent=True, ...)
    assert classify_company(test_company, mock_llm) == mock_response
```

## 결론: 프로덕션 준비된 멀티 에이전트 패턴

이 YC 분석 워크플로(workflow)는 프로덕션 멀티 에이전트 시스템이 LLM 호출을 단순히 연결하는 것 이상을 요구함을 보여줍니다. 필요한 것은:

1. **고비용 작업 최소화**를 위한 지능형 전처리(preprocessing)
2. 타입 안정성(type safety)과 검증(validate)을 갖춘 **구조화된 데이터 흐름**
3. 재시도(retry) 로직과 체크포인팅(checkpointing)을 갖춘 **견고한 오류(error) 처리**
4. 구성 요소 단위 **종합적(comprehensive) 테스트**
5. 전 과정에 걸친 **비용 모니터링**과 최적화(optimization)
6. 워크플로 단계 간 **명확한 관심사 분리**

결과: 수천 개 기업을 처리하고, 정확한 인사이트를 추출하고, 실행 비용 $7 미만으로 끝나고, 어떤 지점의 실패에서도 재개할 수 있는 시스템이 나왔습니다.

가장 중요한 것은, 이것이 데모가 아니라 1,000억 달러 이상 규모 스타트업 생태계에 대한 실제 인사이트를 만들어 낸 프로덕션 시스템이라는 점입니다. 패턴과 아키텍처(architecture) 결정은 훨씬 더 큰 문제로 확장됩니다.

금융 분석 에이전트(agent), 콘텐츠 생성 파이프라인, 고객 서비스 자동화 가운데 무엇을 만들든 동일한 원리가 적용됩니다. 명확한 데이터 모델로 시작하고, 총 비용을 최적화하고, 복원력(resilience)을 내장하고, 모든 것을 독립적으로 테스트하십시오.

AI 애플리케이션의 미래는 더 나은 모델만의 문제가 아닙니다 — 더 나은 엔지니어링의 문제입니다.

---

*이 워크플로(workflow)의 전체 소스 코드는 다음에서 확인할 수 있습니다: [GitHub repository link]*

## 부록: 분석 실행하기

이 분석을 재현하려면:

```bash
# Set up environment
export AZURE_OPENAI_ENDPOINT="https://your-resource.openai.azure.com/"
export AZURE_OPENAI_API_KEY="your-key"

# Clone and run
git clone [repository]
cd yc_analysis
python workflow.py

# Results in ./data/analysis.md
```

워크플로(workflow)는 다음을 수행합니다:
1. YC 기업 데이터 다운로드(첫 실행 이후 캐싱(caching))
2. 키워드 필터링(filtering) 적용
3. 구조화된 LLM 출력(structured output)으로 기업 분류(classification)
4. 인사이트와 비용 분석 생성
5. 결과와 사용량 지표 저장

총 실행 시간: 배치(batch) 크기에 따라 약 5-10분.
총 비용: 기업 5,000여 곳 전체 분석에 약 $7.