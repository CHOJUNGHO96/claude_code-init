---
name: arch-doc
description: |
  Analyzes any project's architecture and generates a structured design document.
  Combines code-explorer subagent, sequential-thinking MCP, and context7 MCP
  to produce a comprehensive architecture document in the .claude/architecture/ directory.

  Use when you need to document or understand the architecture of any codebase.

  Triggers: arch-doc, architecture document, 아키텍처 문서, 설계 문서, 구조 분석
argument-hint: "[path] [guidelines] --author [작성자명]"
user-invocable: true
allowed-tools:
  - Read
  - Glob
  - Grep
  - Write
  - Bash
  - Task
---

# arch-doc Skill

> Analyzes project architecture and generates a design document in `.claude/ARCHITECTURE.md`

## Step 0: Argument Check

**If called with NO arguments:**

Stop immediately and ask the user:

```
분석 지침을 알려주세요. 예시:

  /arch-doc src/ "Flask REST API, API 레이어와 DB 레이어 중심으로 분석" --author 홍길동
  /arch-doc components/ "React 컴포넌트 구조와 상태관리 흐름 분석" --author John
  /arch-doc . "Spring Boot, 전체 레이어드 아키텍처 분석"

필요한 정보:
  1. 분석할 경로 (예: src/, ., components/)
  2. 분석 관점 또는 집중할 레이어 (예: API 구조, 컴포넌트 트리, 서비스 레이어)
  3. (선택) --author [작성자명]  — 생략 시 "Unknown"으로 기록
```

**인자가 있는 경우** — 아래 파싱 후 다음 단계 진행:

- `--author [이름]` 인자가 있으면 → `AUTHOR = [이름]`
- `--author` 인자가 없으면 → `AUTHOR = "Unknown"`
- 나머지 인자에서 경로와 분석 지침을 파싱

---

## Step 1: MCP 가용성 확인

다음 두 MCP 서버의 사용 가능 여부를 확인하세요.

### sequential-thinking MCP 확인

`mcp__sequential-thinking__sequentialthinking` 도구가 사용 가능한지 확인하세요.

**사용 불가 시 다음 메시지를 출력하고 계속 진행하세요 (MCP 없이도 동작):**

```
⚠️  sequential-thinking MCP가 연결되지 않았습니다.
    설치 방법: .mcp.json 에 아래 내용을 추가하세요.

    {
      "mcpServers": {
        "sequential-thinking": {
          "command": "npx",
          "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"]
        }
      }
    }

    이 MCP 없이 계속 진행합니다.
```

### context7 MCP 확인

`mcp__context7__resolve-library-id` 또는 `mcp__plugin_context7_context7__resolve-library-id` 도구가 사용 가능한지 확인하세요.

**사용 불가 시 다음 메시지를 출력하고 계속 진행하세요 (MCP 없이도 동작):**

```
⚠️  context7 MCP가 연결되지 않았습니다.
    설치 방법: .mcp.json 에 아래 내용을 추가하세요.

    {
      "mcpServers": {
        "context7": {
          "command": "npx",
          "args": ["-y", "@upstash/context7-mcp"]
        }
      }
    }

    이 MCP 없이 계속 진행합니다.
```

---

## Step 2: 빠른 전처리 (Glob + Grep)

분석 경로를 기준으로 파일 구조와 핵심 패턴을 빠르게 수집하세요.

```
- Glob: 전체 파일 트리 수집 (디렉토리 구조 파악)
- Grep: 진입점, 라우트, 클래스, import 패턴 탐색
```

---

## Step 3: code-explorer 심층 분석

`feature-dev:code-explorer` subagent를 Task tool로 실행하세요.

**전달할 프롬프트에 반드시 포함할 것:**
- 사용자가 입력한 분석 경로
- 사용자가 입력한 분석 지침 (그대로 전달)
- Step 2에서 수집한 파일 트리 요약

**분석 요청 항목:**
1. 레이어 아키텍처 구조 (레이어 간 의존 방향)
2. 핵심 모듈과 각 역할
3. 요청 처리 흐름 (진입점 → 처리 → 응답)
4. 주요 설계 패턴 식별
5. 외부 의존성 및 통합 포인트
6. 실제 코드에서 사용되는 핵심 패턴 예시 (클래스 구조, 데코레이터, 설정 방식 등)

---

## Step 4: 구조화 (sequential-thinking MCP 사용 시)

sequential-thinking MCP가 사용 가능한 경우, code-explorer 결과를 아래 구조로 정리하세요:

```
1. 프로젝트 개요 요약
2. 레이어 구조 → Mermaid 다이어그램 변환 (복잡도 평가 포함)
3. 요청 흐름 → Mermaid sequenceDiagram 변환
4. 모듈 목록 정리 (표 형식)
5. 외부 의존성 정리 (표 형식)
6. 보완 콘텐츠 목록: 텍스트 설명, 코드 예시, 전략 표
```

MCP 없는 경우: code-explorer 결과를 직접 아래 문서 형식으로 정리하세요.

---

## Step 5: context7 MCP 활용 (사용 가능 시)

context7 MCP가 사용 가능하고, 코드에서 알려진 프레임워크(Flask, React, Spring, Django 등)가 감지된 경우:

- 해당 프레임워크의 공식 아키텍처 패턴을 조회
- 현재 코드베이스가 권장 패턴과 어떻게 다른지 간략히 메모

MCP 없는 경우: 이 단계를 건너뛰세요.

---

## Step 5.5: 기존 문서 확인 및 버전 결정

문서를 쓰기 전에 동일 경로의 파일이 이미 존재하는지 확인하세요.

### 신규 생성인 경우

- `VERSION = "v1.0.0"`
- `CHANGE_NOTE = "초본 작성"`
- `AUTHOR` = Step 0에서 파싱한 값 유지

### 기존 문서가 있는 경우

1. 기존 파일을 Read 도구로 읽어 상단 메타데이터에서 `Version:` 줄을 파싱
2. `Author:` 줄을 파싱해 **기존 작성자를 AUTHOR로 유지** (Step 0의 `--author`는 무시)
3. 변경 규모를 판단해 버전 증가:

   | 변경 규모 | 기준 | 증가 |
   |-----------|------|------|
   | patch | 내용만 갱신, 섹션 구조 변화 없음 | +0.0.1 |
   | minor | 섹션 추가·삭제 등 구조 변경 | +0.1.0 |
   | major | 전면 재작성 또는 기술스택 변경 | +1.0.0 |

4. `CHANGE_NOTE`에 이번 변경 내용을 한 줄로 요약 (예: "DB 레이어 섹션 추가", "인증 흐름 다이어그램 수정")
5. 기존 변경이력 테이블 내용을 보존하고 새 행을 **최상단**에 추가

---

## Step 6: 문서 생성

분석 결과를 아래 템플릿으로 `.claude/architecture/{파일명}.md`에 저장하세요.

**저장 경로**: `{현재 작업 디렉토리}/.claude/architecture/{파일명}.md`

**파일명 생성 규칙**:
- 사용자가 입력한 분석 지침에서 핵심 키워드를 추출해 최대한 짧게 요약
- 단어 구분은 언더바(`_`) 사용, 소문자 영문 권장
- 예시: 지침 "Flask REST API, API 레이어와 DB 레이어 중심으로 분석" → `flask_api_db_layer.md`
- 예시: 지침 "PostgreSQL 연결 풀링 구조 분석" → `postgresql_connect.md`
- 예시: 지침 "React 컴포넌트 구조와 상태관리 흐름 분석" → `react_component_state.md`

---

## Mermaid 다이어그램 작성 규칙

> ⚠️ **CRITICAL — 줄바꿈은 반드시 `<br/>`** ⚠️
> Mermaid 노드 레이블 안에서 줄바꿈할 때 `\n`을 절대 사용하지 마세요.
> **`\n` 사용 금지 — `<br/>` 만 허용**

### 규칙 1 — 줄바꿈: `<br/>` 필수

노드 레이블에서 줄바꿈이 필요한 경우 **반드시 `<br/>`를 사용**하세요.
`\n`은 Mermaid에서 렌더링되지 않고 그대로 노출됩니다.

```
❌ 절대 금지:
  A["YAML 쿼리 로드\nquery/*.yaml 파싱"]
  B{"Cookie에\ntoken 있음?"}

✅ 올바른 예:
  A["YAML 쿼리 로드<br/>query/*.yaml 파싱"]
  B{"Cookie에<br/>token 있음?"}
```

### 규칙 2 — 노드 레이블: 기능 설명, 코드 금지

노드 레이블은 **그 노드가 무엇을 하는지** 설명해야 합니다. 실제 함수명·변수명·코드 구문을 그대로 쓰지 마세요.

```
❌ 잘못된 예 (코드 구문 그대로):
  F --> G["app.config.from_object(GLOBAL_CONFIG)"]
  C --> D["init_query_dict()<br/>query/*.yaml 전체 로드 → QUERY{}"]

✅ 올바른 예 (기능 설명):
  F --> G["앱 설정 적용<br/>환경별 Config 주입"]
  C --> D["YAML 쿼리 로드<br/>전체 SQL 템플릿 파싱"]
```

### 규칙 3 — 복잡도 제한

하나의 다이어그램에서:
- **노드 수**: 최대 12개
- **엣지 수**: 최대 15개
- **중첩 subgraph**: 최대 2단계

복잡한 흐름은 **여러 개의 독립 다이어그램**으로 분리하세요.

```
❌ 잘못된 예: 앱 시작부터 요청 처리까지 하나의 다이어그램에 20개 노드
✅ 올바른 예:
  - 다이어그램 A: 앱 시작 및 초기화 흐름 (8개 노드)
  - 다이어그램 B: HTTP 요청 처리 흐름 (sequenceDiagram)
  - 다이어그램 C: 인증 흐름 (sequenceDiagram)
```

### 규칙 3.5 — erDiagram 제약조건

erDiagram 속성에 사용할 수 있는 제약조건(constraint)은 **`PK`, `FK`, `UK` 3가지만 유효**합니다.
`PK_FK` 등 임의의 복합 키워드는 Mermaid 파서가 인식하지 못해 cascading parse error를 유발합니다.

```
❌ 절대 금지:
  integer ai_no PK_FK

✅ 올바른 예:
  integer ai_no PK "자산 번호(FK 겸용)"
```

> PK와 FK를 동시에 표현해야 할 경우, `PK`로 표기하고 코멘트(따옴표 문자열)에 FK 역할을 명시하세요.

### 규칙 4 — 다이어그램 타입 선택 기준

| 상황 | 사용할 타입 | 이유 |
|------|------------|------|
| 요청/응답 흐름, 인증 흐름, 서비스 간 통신 | `sequenceDiagram` | 참여자와 메시지 순서가 명확 |
| 앱 시작 순서, 데이터 파이프라인 | `flowchart TD` | 단방향 흐름 표현에 적합 |
| 레이어 구조, 의존 관계 | `graph TD` 또는 `flowchart LR` | 계층·방향 표현에 적합 |
| 권한 계층, 트리 구조 | `graph LR` | 좌→우 계층 표현 |

### 규칙 5 — sequenceDiagram 가독성

sequenceDiagram에서 메시지 레이블은 **간결한 동작 + 핵심 파라미터** 형식으로:

```
❌ 잘못된 예:
  Auth->>RDS2: is_black_list_token(token) / redis.get(token_key) 호출

✅ 올바른 예:
  Auth->>RDS2: 블랙리스트 토큰 조회
  RDS2-->>Auth: 등록 여부 반환
```

---

### 보완 콘텐츠 작성 규칙

다이어그램만으로 표현하기 어려운 내용은 **텍스트 설명, 표, 코드 예시**로 보완하세요.

**표를 활용할 상황:**
- 설정값 목록 (DB, Redis, 인증 키 등)
- API 엔드포인트 구조
- 모듈별 역할
- 캐시 전략, 권한 계층

**코드 예시를 활용할 상황:**
- 프레임워크 패턴 (Resource 클래스 구조, 데코레이터 체인 등)
- 쿼리 파일 형식 (YAML 예시 등)
- 응답 포맷

**텍스트 설명을 활용할 상황:**
- 다이어그램에서 생략된 중요 조건/분기 설명
- 특이사항, 알려진 버그, 설계 결정 이유

---

### 출력 문서 템플릿

```markdown
# {프로젝트명} — {기술스택} Architecture

> Author: {AUTHOR}
> Version: {VERSION}
> Last Updated: {날짜}
> Generated by /arch-doc | {날짜}
> Path analyzed: `{분석 경로}`
> Guidelines: {사용자 지침}

---

## 1. Overview

{프로젝트 한 줄 요약}

| 항목 | 내용 |
|------|------|
| 언어 | |
| 프레임워크 | |
| DB | |
| 캐시 | |
| 인증 | |
| 주요 패턴 | |

---

## 2. 디렉토리 구조

```
{주요 디렉토리/파일 트리 — 핵심 파일만, 설명 포함}
```

---

## 3. 레이어 아키텍처

{레이어 구조를 한 문장으로 설명}

```mermaid
graph TD
  {최대 12노드, 기능 설명 레이블, subgraph 활용}
```

---

## 4. 앱 실행 흐름

{시작 시 일어나는 일을 한 문장으로 설명}

```mermaid
flowchart TD
  {앱 초기화 순서 — 최대 10노드, 코드 금지}
```

---

## 5. 요청 처리 흐름

{HTTP 요청이 처리되는 방식을 한 문장으로 설명}

```mermaid
sequenceDiagram
  {참여자 명시, 간결한 메시지 레이블}
```

---

## 6. 인증 흐름

{인증 방식을 한 문장으로 설명}

```mermaid
sequenceDiagram
  {로그인 + 토큰 검증 흐름 — 분기 포함}
```

---

## 7. 핵심 모듈

| 모듈 | 경로 | 역할 |
|------|------|------|
| {모듈명} | `{경로}` | {역할} |

---

## 8. {도메인별 주요 기능 — 예: API 엔드포인트 구조}

{텍스트 설명}

### {서브섹션 — 예: 동적 라우팅 규칙}

{코드 예시 또는 표}

---

## 9. {전략/정책 섹션 — 예: 캐싱 전략, 보안 아키텍처}

| 항목 | 내용 |
|------|------|

---

## 10. 외부 의존성

| 패키지/서비스 | 용도 |
|--------------|------|
| {패키지} | {용도} |

---

## 11. 특이사항

- {알려진 버그, 설계 결정 이유, 주의할 점}

---

## 📝 변경 이력

| 버전 | 날짜 | 변경 사항 |
|------|------|---------|
| {VERSION} | {날짜} | {CHANGE_NOTE} |
```

> **템플릿 활용 지침**: 위 번호는 예시입니다. 프로젝트에 맞게 섹션을 추가·제거하세요.
> 다이어그램이 필요 없는 섹션은 표와 텍스트만으로 작성해도 됩니다.
> 변경이력 섹션은 반드시 문서 맨 마지막에 위치해야 합니다.

---

## Step 7: 가독성 자체 검증

문서를 Write 도구로 저장한 **직후**, 아래 Bash 명령으로 `\n` 오염 여부를 반드시 확인하세요:

```bash
# \n 이 노드 레이블 안에 남아있는지 검사
grep -n '\\n' .claude/architecture/{파일명}.md
```

- **출력이 있으면** → 해당 줄을 Edit 도구로 `<br/>`로 교체한 뒤 다시 검사
- **출력이 없으면** → 통과, 아래 체크리스트 진행

```
[ ] \n grep 검사 통과 (출력 없음)
[ ] 각 Mermaid 다이어그램의 노드 수가 12개 이하인가?
[ ] 노드 레이블에 함수명/변수명/괄호 코드가 없는가?
[ ] sequenceDiagram 메시지가 간결한 동작 설명인가?
[ ] 복잡한 흐름이 여러 다이어그램으로 분리되어 있는가?
[ ] 각 섹션에 다이어그램 외에 텍스트 설명 또는 표가 있는가?
```

하나라도 X라면 해당 다이어그램/섹션을 수정한 후 저장하세요.

---

## 완료 후 출력

문서 생성이 완료되면 다음 내용을 사용자에게 출력하세요:

```
✅ 아키텍처 문서가 생성되었습니다.
   → .claude/architecture/{파일명}.md
   📌 버전: {VERSION}  |  작성자: {AUTHOR}  |  날짜: {날짜}

사용된 도구:
  ✅ code-explorer subagent
  {✅ or ⚠️ (미설치)} sequential-thinking MCP
  {✅ or ⚠️ (미설치)} context7 MCP
```
