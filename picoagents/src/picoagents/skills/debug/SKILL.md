---
name: debug
description: 오류와 예상 밖 동작을 디버깅하는 체계적 접근법
triggers: debug, error, bug, fix, not working, broken
---

# 디버그(Debug) 스킬

## 디버깅(debugging) 절차

### 1. 재현
- 정확한 오류 메시지(error message)를 확보한다
- 오류를 발생시키는 최소 단위의 절차를 찾는다
- 환경을 기록한다 (Python 버전, OS, 의존성)

### 2. 위치 파악
- 전체 스택 트레이스(stack trace)를 읽는다 (아래 = 충돌한 지점, 위 = 근본 원인)
- 관련 파일과 줄을 찾는다
- 맥락을 위해 주변 코드를 읽는다

### 3. 이해
- 코드가 무엇을 시도하고 있었는가?
- 어떤 입력이 오류를 발생시켰는가?
- 어떤 전제가 위배되었는가?

### 4. 수정
- 문제를 고치는 최소한의 변경만 한다
- 디버깅 중에 리팩터링(refactoring)하지 않는다
- 이 문제를 사전에 잡아냈을 테스트(test)를 추가한다

### 5. 검증
- 실패했던 케이스를 다시 실행한다
- 전체 테스트 스위트(test suite)를 실행한다
- 다른 곳에 유사한 패턴이 있는지 확인한다

## 흔한 패턴

**NoneType 오류**: 무언가가 예상 없이 None을 반환했다. 호출 체인(call chain)을 확인한다.

**Import 오류**: 의존성 누락 또는 순환 import(circular import). requirements과 import 순서를 확인한다.

**Key/Index 오류**: 존재하지 않는 항목에 접근. 존재 여부 검사(existence check)를 추가한다.

**Type 오류**: 잘못된 타입(type)이 전달됨. 함수 시그니처(signature)와 호출자를 확인한다.

## 워크플로(Workflow)

1. 코드베이스에서 오류 메시지를 `grep`으로 검색
2. 스택 트레이스를 아래에서 위로 읽기
3. 실패한 함수를 읽기
4. 값 추적이 필요하면 로깅(logging)/print 추가
5. 수정하고 검증
