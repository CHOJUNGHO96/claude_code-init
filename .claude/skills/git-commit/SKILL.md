---
name: git-commit
description: >
  Git 변경사항을 분석하고 Conventional Commits 규칙에 따라 커밋 메시지를 자동 생성하여 커밋합니다.
  staged/unstaged 변경사항을 확인하고, 적절한 파일을 스테이징한 후 의미있는 커밋 메시지로 커밋합니다.
version: 1.0.0
author: local
---

# Git Commit 스킬

사용자가 git commit을 요청하면 다음 워크플로우를 따릅니다.

## 실행 순서

### 1단계: 현재 상태 파악 (병렬 실행)

다음 명령어를 **동시에** 실행하여 현재 상태를 파악합니다:

```bash
git status
git diff --staged
git diff
git log --oneline -5
```

### 2단계: 변경사항 분석

수집한 정보를 바탕으로 분석합니다:
- 어떤 파일이 변경/추가/삭제되었는가
- 변경의 성격은 무엇인가 (기능 추가, 버그 수정, 리팩토링 등)
- 스테이징되지 않은 파일이 있는가
- 민감한 파일(.env, credentials 등)이 포함되어 있는가

### 3단계: 스테이징

- 이미 staged된 파일이 있으면 그대로 사용
- staged 파일이 없으면 관련 파일을 `git add <파일명>` 으로 추가
- **절대 `git add -A` 또는 `git add .` 사용 금지** (민감 파일 포함 위험)
- .env, *.key, *.pem, credentials 파일은 절대 추가하지 않음

### 4단계: 커밋 메시지 생성

**Conventional Commits** 규칙을 따릅니다:

```
<type>[optional scope]: <description>

[optional body]

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
```

**타입 선택 기준**:
| 타입 | 사용 상황 |
|------|-----------|
| `feat` | 새로운 기능 추가 |
| `fix` | 버그 수정 |
| `docs` | 문서 변경 (README, 주석 등) |
| `style` | 코드 포맷팅, 세미콜론 누락 등 (기능 변경 없음) |
| `refactor` | 버그 수정도 기능 추가도 아닌 코드 변경 |
| `test` | 테스트 추가 또는 수정 |
| `chore` | 빌드 프로세스, 도구 설정 변경 |
| `perf` | 성능 개선 |
| `ci` | CI/CD 설정 변경 |
| `build` | 빌드 시스템 또는 외부 의존성 변경 |
| `revert` | 이전 커밋 되돌리기 |

**메시지 작성 규칙**:
- 제목은 영어 또는 한국어 (프로젝트 관행 따름)
- 제목은 50자 이내, 마침표 없음
- 명령형 사용 (예: "Add feature" not "Added feature")
- 본문은 변경 이유와 영향 설명

### 5단계: 커밋 실행

HEREDOC을 사용하여 커밋합니다:

```bash
git commit -m "$(cat <<'EOF'
feat(scope): 커밋 메시지

변경 이유 및 영향 (선택사항)

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
EOF
)"
```

### 6단계: 결과 확인

```bash
git status
git log --oneline -3
```

## 안전 규칙

- pre-commit hook이 실패하면 `--no-verify` 절대 사용 금지 → 문제를 수정 후 재시도
- 사용자가 명시적으로 요청하지 않으면 `git push` 하지 않음
- 비어있는 커밋 생성 금지
- force push는 사용자 명시 요청 시에만 수행
- amend는 사용자 명시 요청 시에만 수행

## 사용 예시

```
# 기본 사용
/git-commit

# 스킬 직접 호출
"git commit 해줘"
"변경사항 커밋해줘"
"현재 수정사항 커밋"
```
