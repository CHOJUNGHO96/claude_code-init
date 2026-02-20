---
name: handoff
description: 현재 세션의 작업 컨텍스트를 다음 세션으로 효과적으로 전달하기 위한 HANDOFF.md를 생성한다. 세션 종료 전, 작업 중단 전, 또는 다른 AI 에이전트에게 작업을 넘길 때 사용한다.
---

# Handoff Skill

세션 컨텍스트를 보존하고 다음 세션에서 즉시 작업을 재개할 수 있도록 HANDOFF.md를 생성한다.

## When to Activate

- 세션을 종료하기 전
- 작업을 중단하고 나중에 이어서 해야 할 때
- 다른 AI 에이전트 또는 협업자에게 작업을 인계할 때
- `/compact` 실행 전 중요 컨텍스트를 보존하고 싶을 때
- 사용자가 "핸드오프", "handoff", "HANDOFF.md 만들어줘", "다음 세션에 이어서" 등을 언급할 때

## Output Location

반드시 프로젝트 루트의 `.claude/memories/HANDOFF.md`에 저장한다.

```bash
# 디렉토리가 없으면 생성
mkdir -p .claude/memories
```

## HANDOFF.md Template

아래 구조를 엄격히 따라 작성한다. 각 섹션은 구체적이고 실행 가능한 내용으로 채워야 한다. 추상적이거나 모호한 서술 금지.

```markdown
# HANDOFF — {작업명 또는 기능명}

> 생성일시: {YYYY-MM-DD HH:MM}
> 세션 요약: {이 세션에서 다룬 핵심 작업을 한 줄로}

---

## 🎯 Goal (목표)

현재 수행 중인 작업의 최종 목적을 명시한다.

- **최종 목표**: {완료 기준이 명확한 목표 서술}
- **범위**: {이번 작업의 경계 — 무엇을 하고 무엇을 하지 않는지}
- **완료 기준**: {이 작업이 "완료"됐다고 판단할 수 있는 구체적 조건}

---

## ✅ Current Progress (진행 상황)

### What's Been Done (완료된 작업)

완료된 항목을 체크리스트로 나열한다.

- [x] {완료된 작업 1} — `{관련 파일 경로}`
- [x] {완료된 작업 2} — `{관련 파일 경로}`
- [ ] {진행 중인 작업} — 현재 {진행률}% 완료

### What Worked (성공한 방식)

재사용할 수 있는 접근법, 발견한 유용한 패턴을 기록한다.

- {성공한 방법 1}: {왜 효과적이었는지}
- {성공한 방법 2}: {핵심 인사이트}

---

## ❌ Challenges (실패 및 문제점)

### What Didn't Work (실패한 시도)

같은 실수를 반복하지 않도록 실패한 접근법과 이유를 명시한다.

- **시도**: {무엇을 시도했는지}
  **결과**: {왜 실패했는지, 어떤 에러가 발생했는지}
  **교훈**: {다음에 어떻게 다르게 접근해야 하는지}

### Current Blockers (현재 막힌 문제)

지금 당장 해결되지 않아 다음 세션에서 풀어야 할 문제들.

- **문제**: {구체적인 문제 서술}
  **위치**: `{파일명:라인번호}`
  **시도한 것**: {이미 시도해본 해결책}
  **가설**: {다음에 시도해볼 접근법}

---

## 🚀 Next Steps (다음 단계)

새로운 세션에서 즉시 착수해야 할 작업을 우선순위 순으로 정렬한다.

### 즉시 해야 할 것 (Priority 1)

1. **{작업명}**
   - 파일: `{파일 경로}`
   - 구체적 작업: {무엇을 어떻게 해야 하는지 단계별로}
   - 예상 소요: {시간 또는 복잡도}

2. **{작업명}**
   - 파일: `{파일 경로}`
   - 구체적 작업: {무엇을 어떻게}

### 그 다음 해야 할 것 (Priority 2)

- {작업 3}: {간략한 설명}
- {작업 4}: {간략한 설명}

### 나중에 해도 되는 것 (Priority 3)

- {개선사항 또는 최적화 아이디어}

---

## 🗂️ Key Context (핵심 컨텍스트)

다음 세션의 AI가 반드시 알아야 할 배경 정보.

### 중요 파일

| 파일 | 역할 | 주의사항 |
|------|------|---------|
| `{파일 경로}` | {역할} | {건드리면 안 되는 이유 또는 주의점} |

### 결정된 사항 (Decision Log)

이번 세션에서 내린 중요한 기술적 결정과 근거.

- **결정**: {무엇을 선택했는지}
  **이유**: {왜 이 선택을 했는지}
  **대안**: {고려했지만 선택하지 않은 것}

### 환경 정보

- 실행 중인 서비스: {서비스명 및 포트}
- 미적용 마이그레이션: {있으면 명시}
- 임시 설정: {되돌려야 할 임시 변경사항}

---

## 💬 Resume Prompt (재개 프롬프트)

다음 세션 시작 시 이 문장을 그대로 붙여넣어 즉시 작업을 재개한다.

> `.claude/memories/HANDOFF.md`를 읽고 작업을 이어서 진행해줘.
> 현재 목표는 {목표 한 줄 요약}이고,
> 다음 우선순위 작업은 {Priority 1 작업명}이다.
```

## Writing Guidelines

HANDOFF.md를 작성할 때 반드시 지켜야 할 원칙:

1. **구체성**: "작업 중"이 아니라 "app/api/cve.py 43번째 줄의 filter_by_severity 함수 미구현"처럼 정확하게
2. **실행 가능성**: 다음 세션의 AI가 HANDOFF.md만 보고 즉시 작업 재개 가능해야 함
3. **실패 기록 필수**: 시도했다가 실패한 것을 반드시 포함 — 같은 실수 반복 방지가 핵심
4. **파일 경로 명시**: 관련 파일은 항상 상대 경로로 포함
5. **Resume Prompt 포함**: 복붙만 하면 되는 재개 프롬프트 필수

## Example

```markdown
# HANDOFF — CVE 검색 API 필터링 구현

> 생성일시: 2026-02-20 15:30
> 세션 요약: PostgreSQL full-text search 기반 CVE 검색 엔드포인트 구현 중

---

## 🎯 Goal

- **최종 목표**: GET /api/cve/search?severity=HIGH&keyword=buffer+overflow API 완성
- **범위**: 검색 로직만 — UI는 다음 스프린트
- **완료 기준**: pytest tests/test_cve_search.py 전체 통과 + 커버리지 80%+

## ✅ Current Progress

### What's Been Done
- [x] CVE 모델 정의 — `app/models/cve.py`
- [x] 기본 CRUD 엔드포인트 — `app/api/cve.py`
- [ ] 필터링 로직 — 현재 30% 완료

### What Worked
- SQLAlchemy `func.to_tsvector`로 full-text search 구현: 속도 문제 없음
- Pydantic v2 `model_validator`로 쿼리 파라미터 검증: 깔끔하게 동작

## ❌ Challenges

### What Didn't Work
- **시도**: Elasticsearch 도입
  **결과**: 현재 규모에서 over-engineering, 인프라 비용 문제
  **교훈**: PostgreSQL FTS로 충분, Elasticsearch는 10만건 이상일 때 고려

### Current Blockers
- **문제**: severity 필터와 keyword 필터 AND 조합 시 쿼리 성능 저하
  **위치**: `app/api/cve.py:87`
  **시도한 것**: 인덱스 추가했으나 미적용 상태
  **가설**: GIN 인덱스 + partial index 조합 시도 예정

## 🚀 Next Steps

### Priority 1
1. **GIN 인덱스 마이그레이션 작성**
   - 파일: `migrations/versions/add_cve_search_index.py`
   - 작업: `CREATE INDEX CONCURRENTLY idx_cve_fts ON cve USING GIN(to_tsvector('english', description))`

2. **필터 AND 조합 로직 완성**
   - 파일: `app/api/cve.py:87`
   - 작업: `build_search_query()` 함수의 severity + keyword 복합 필터 구현

## 💬 Resume Prompt

> `.claude/memories/HANDOFF.md`를 읽고 작업을 이어서 진행해줘.
> 현재 목표는 CVE 검색 API 필터링 구현이고,
> 다음 우선순위 작업은 GIN 인덱스 마이그레이션 작성이다.
```
