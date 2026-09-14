# 대화형 노트북

이 Jupyter 노트북은 PicoAgents 개념을 브라우저에서 대화형으로 배울 수 있게 안내합니다. 로컬 설치 없이 학습하기에 더없이 좋습니다!

## 📚 이용 가능한 노트북

| 노트북 | 장 | 주제 | Colab에서 열기 |
|----------|---------|-------|---------------|
| [`01_basic_agent.ipynb`](01_basic_agent.ipynb) | 4장 | 도구를 갖춘 에이전트(agent) 만들기 | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/victordibia/designing-multiagent-systems/blob/main/examples/notebooks/01_basic_agent.ipynb) |

**더 많은 노트북이 곧 공개됩니다!** 그동안 모든 예시는 [Python 스크립트](../agents/)로 이용 가능하며, 전체 개발 환경은 [GitHub Codespaces](../../README.md#getting-started)에서 사용할 수 있습니다.

## 🚀 빠른 시작

### 옵션 1: Google Colab (입문자에게 권장)
위의 "Open in Colab" 배지를 아무거나 클릭하십시오. 설치가 필요 없습니다!

**Colab에서:**
1. 배지를 클릭하여 노트북 열기
2. 입력을 요청받으면 OpenAI API 키 추가
3. Shift+Enter로 셀 실행

### 옵션 2: 로컬 Jupyter
```bash
# Install Jupyter
pip install jupyter

# Launch notebooks
jupyter notebook examples/notebooks/
```

### 옵션 3: VS Code
Jupyter 확장이 설치된 VS Code에서 임의의 `.ipynb` 파일을 여십시오.

## 💡 팁

- **API 키**: [Colab Secrets](https://x.com/GoogleColab/status/1719798406195867814)를 사용하여 OpenAI API 키를 안전하게 보관하십시오
- **GPU**: 대부분의 예시에는 GPU가 필요 없지만, Colab에서는 활성화할 수 있습니다: Runtime → Change runtime type → GPU
- **저장**: Colab은 Google Drive에 자동 저장하며, File → Download → Download .ipynb로 다운로드할 수 있습니다

## 🔗 관련 자료

- [전체 Python 예시](../agents/) - 모든 예시의 Python 스크립트 버전
- [GitHub Codespaces](../../README.md#getting-started) - 완전한 개발 환경
- [책](https://buy.multiagentbook.com) - 멀티 에이전트 시스템(multi-agent system) 완전 안내서
