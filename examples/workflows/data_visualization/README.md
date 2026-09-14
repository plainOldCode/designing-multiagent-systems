# 데이터 시각화(visualization) 워크플로(workflow)

이 워크플로는 LLM 기반 워크플로를 이용한 자동 데이터 시각화 생성을 시연합니다. 안전한 코드 생성과 실행을 위한 프로덕션 패턴을 보여줍니다.

## 주요 기능

- **지능형 데이터 요약(Summarization)**: LLM 기반 데이터 컨텍스트(context) 이해
- **구조화된 목표(goal) 생성**: 검증(verification)을 동반한 다양한 시각화 목표
- **안전한 코드 생성**: LLM 생성 코드에 대한 다층(multi-layer) 보안 검증
- **샌드박스(sandboxed) 실행**: 시각화 코드 실행을 위한 통제된 환경
- **프로덕션 패턴**: 체크포인팅(checkpointing), 오류(error) 처리, 독립적 테스트

## 빠른 시작

### 사전 요구 사항

1. 환경 변수 설정:
```bash
export AZURE_OPENAI_ENDPOINT="your-endpoint"
export AZURE_OPENAI_API_KEY="your-key"
```

2. 의존성 설치:
```bash
pip install pandas matplotlib seaborn numpy
```

### 워크플로 실행

```bash
# Basic usage with sample data
python workflow.py

# Test with your own data
python workflow.py --data-file your_data.csv
```

### 예시 출력

워크플로는 다음을 생성합니다:
- `viz_output/chart_*.png` - 생성된 시각화
- `viz_output/visualization_report.md` - 요약 보고서
- 재개(resume) 가능성을 위한 체크포인트(checkpoint) 파일

## 아키텍처(architecture)

이 워크플로는 YC 분석 예시와 동일한 패턴을 따릅니다:

1. **데이터 로드(load) 및 요약(Summarization)** (`load_and_summarize_data`)
   - 지능형 캐싱(caching)으로 CSV 데이터 로드
   - LLM 기반 컨텍스트 요약 생성
   - 시각화 기회 포착

2. **목표(goal) 생성** (`generate_visualization_goals`)
   - 다양한 분석 질문 생성
   - 적절한 차트 유형 권고
   - 열(column) 참조 검증(validate)

3. **코드 생성** (`generate_visualization_code`)
   - Python 시각화 코드 생성
   - 실행 전 코드 안전성 검증(validate)
   - 복수 시각화 라이브러리(library) 지원

4. **안전한 실행** (`execute_visualization_codes`)
   - 제한된 환경에서 코드 실행
   - 타임아웃(timeout) 및 자원 제한 적용
   - 정리(cleanup)를 동반한 차트 저장

## 안전 기능

### 코드 검증(validate)
- 위험한 패턴에 대한 정적 분석(static analysis)
- 임포트 화이트리스트(whitelisting) (안전한 라이브러리(library)만)
- 파일 I/O 또는 네트워크 접근 불허

### 실행 샌드박스(sandbox)
- 제한된 전역 네임스페이스(namespace)
- 타임아웃(timeout) 제어 (기본 30초)
- 자동 자원 정리(cleanup)
- 시각화 간 오류(error) 격리(isolation)

## 테스트

```bash
# Run unit tests
python test_workflow.py

# Test individual components
python -m pytest test_workflow.py -v
```

## 설정

`VisualizationConfig`의 주요 설정 옵션:

- `data_file`: CSV 데이터 파일 경로
- `output_dir`: 생성된 차트를 담을 디렉터리
- `max_goals`: 생성할 시각화 목표(goal) 수
- `grammar_preference`: 시각화 라이브러리(library) ("matplotlib", "seaborn", "plotly")
- `execution_timeout`: 코드 실행 최대 시간
- `force_refresh`: 캐싱(caching)을 건너뛰고 전체 재생성

## 파일

- `workflow.py` - 주요 워크플로 오케스트레이션(orchestration)
- `test_workflow.py` - 구성 요소 단위 테스트
- `viz_output/` - 생성된 차트와 보고서 (실행 시 생성)