---
name: code-review
description: 코드 변경 사항에서 버그, 보안 문제, 개선점을 검토
triggers: review, code review, check code, review PR
---

# 코드 검토(Code Review) 스킬

## 검토 체크리스트

### 1. 정확성
- 코드가 주장하는 대로 동작하는가?
- 경계 케이스(null, 빈 값, 범위 한계)가 처리되는가?
- 오류 처리(error handling)가 적절한가?

### 2. 보안
- 입력 검증(input validation)이 있는가?
- SQL 인젝션(injection), XSS, 명령어(command) 인젝션이 없는가?
- 시크릿(secret)이 하드코딩되지 않았는가?
- 권한(permission)이 확인되는가?

### 3. 성능
- N+1 쿼리가 없는가?
- 적절한 데이터 구조(data structure)를 사용하는가?
- 불필요한 루프(loop)나 할당(allocation)이 없는가?

### 4. 유지보수성
- 명명이 명확한가?
- 함수는 한 가지만 수행하는가?
- 매직 넘버(magic number)가 없는가?
- 테스트(test)가 포함되었는가?

## 검토 형식

각 문제를 발견한 경우:

```
**[SEVERITY] file:line - Brief description**

Problem: What's wrong
Impact: Why it matters
Suggestion: How to fix
```

심각도(severity): CRITICAL, HIGH, MEDIUM, LOW, NIT

## 워크플로(Workflow)

1. diff 또는 변경된 파일을 읽는다
2. 의도를 이해한다 (PR 설명, 커밋 메시지)
3. 각 파일을 체크리스트에 따라 점검한다
4. 심각도 수준과 함께 발견 사항을 요약한다
