# Everything Claude Code — 스킬 가이드

스킬(Skill)은 특정 기술 스택, 언어, 워크플로우에 대한 전문 지식을 Claude Code에 주입하는 재사용 가능한 컨텍스트 파일이다. 슬래시 명령어(`/skillname`) 또는 CLAUDE.md에 포함하여 사용하며, 해당 도메인의 모범 사례와 패턴을 세션 전반에서 자동으로 적용한다.

---

## 카테고리별 스킬 목록 (총 29개)

### 프레임워크 & 언어 패턴 (16개)

#### `backend-patterns`
**역할**: Node.js, Express, Next.js API Routes를 위한 백엔드 아키텍처 패턴

- RESTful API 구조 설계 및 리소스 기반 URL 설계
- 레이어드 아키텍처(Controller → Service → Repository) 패턴
- 데이터베이스 커넥션 풀링, 에러 핸들링, 미들웨어 구성

---

#### `coding-standards`
**역할**: TypeScript, JavaScript, React, Node.js 범용 코딩 표준

- 가독성 우선의 명확한 변수·함수명 규칙
- DRY, SOLID, KISS 원칙 적용 기준
- 코드 리뷰 체크리스트 및 자기 문서화 코드 작성법

---

#### `django-patterns`
**역할**: Django 아키텍처 패턴 및 Django REST Framework(DRF) API 설계

- Django 프로젝트 구조 및 앱 분리 전략
- DRF 시리얼라이저, ViewSet, Permissions 패턴
- ORM 최적화(`select_related`, `prefetch_related`), 캐싱, 시그널, 미들웨어

---

#### `django-security`
**역할**: Django 보안 모범 사례 및 안전한 배포 설정

- 인증·인가(Authentication/Authorization) 구현 패턴
- CSRF, XSS, SQL Injection 방지 설정
- 시크릿 관리(`django-environ`), 프로덕션 보안 설정(`SECURE_*`)

---

#### `django-tdd`
**역할**: `pytest-django` 기반 Django TDD 방법론

- `factory_boy`로 테스트 픽스처 생성, 모킹 전략
- Django REST Framework API 테스트 작성법
- 커버리지 측정 및 80%+ 유지 방법

---

#### `frontend-patterns`
**역할**: React, Next.js 프론트엔드 개발 패턴

- 컴포넌트 합성(Composition over Inheritance) 패턴
- 상태 관리 전략, 성능 최적화(코드 스플리팅, 메모이제이션)
- Next.js App Router, Server Components, 접근성(Accessibility) 기준

---

#### `golang-patterns`
**역할**: 관용적 Go 패턴 및 모범 사례

- Functional Options, 인터페이스 기반 의존성 주입
- 에러 처리 패턴(`fmt.Errorf`, `errors.As`), 동시성(goroutine, channel)
- 패키지 설계 원칙 및 모듈 구조

---

#### `golang-testing`
**역할**: Go 테스트 패턴 및 TDD 방법론

- 테이블 기반 테스트(Table-Driven Tests) 작성법
- 서브테스트, 벤치마크, 퍼즈 테스트
- `-race` 플래그로 동시성 버그 탐지, 커버리지 측정

---

#### `java-coding-standards`
**역할**: Spring Boot 서비스를 위한 Java 코딩 표준

- Java 17+ 기준 네이밍, 불변성, Optional 사용법
- Streams API, 예외 처리, 제네릭 활용
- 패키지 구조 및 레이어 분리 기준

---

#### `python-patterns`
**역할**: Pythonic 관용 패턴 및 PEP 8 표준

- 타입 힌트 필수화, Protocol(Duck Typing), dataclass 사용
- 컨텍스트 매니저, 제네레이터, 데코레이터 패턴
- 모듈 구조 설계 및 패키지 분리 전략

---

#### `python-testing`
**역할**: `pytest` 기반 Python 테스트 전략

- TDD 워크플로우(Red → Green → Refactor) 적용
- fixtures, parametrize, 모킹(`unittest.mock`) 패턴
- `pytest-cov`로 커버리지 80%+ 유지

---

#### `springboot-patterns`
**역할**: Spring Boot REST API 및 레이어드 아키텍처 패턴

- Controller → Service → Repository 레이어 설계
- 비동기 처리(`@Async`), 캐싱(`@Cacheable`), 로깅 전략
- JPA 데이터 접근, 예외 처리(`@ControllerAdvice`)

---

#### `springboot-security`
**역할**: Spring Security 보안 설정 및 모범 사례

- JWT/Opaque Token 인증, `OncePerRequestFilter` 검증
- CSRF, XSS, 입력 검증(`@Valid`), 시크릿 관리
- 레이트 리미팅, 보안 헤더, 의존성 취약점 관리

---

#### `springboot-tdd`
**역할**: Spring Boot TDD — JUnit 5, Mockito, Testcontainers

- MockMvc로 컨트롤러 레이어 테스트
- `@MockBean`으로 서비스 모킹, Testcontainers로 통합 테스트
- JaCoCo 커버리지 측정 및 80%+ 목표

---

#### `django-verification` / `springboot-verification`
**역할**: Django 및 Spring Boot 프로젝트의 배포 전 검증 루프

- 마이그레이션, 린팅, 테스트(커버리지 포함), 보안 스캔 순서로 실행
- PR 생성 전, 주요 변경 후, 배포 전 실행 권장
- 각 단계별 통과 기준 명시

---

### 데이터베이스 (3개)

#### `clickhouse-io`
**역할**: ClickHouse 분석 데이터베이스 패턴 및 쿼리 최적화

- 컬럼 지향 저장소 특성에 맞는 쿼리 설계
- MergeTree 엔진 선택 및 파티셔닝 전략
- 고성능 분석 쿼리 작성 및 데이터 엔지니어링 패턴

---

#### `jpa-patterns`
**역할**: JPA/Hibernate 엔티티 설계 및 관계 최적화 (Spring Boot)

- 엔티티 설계(`@Entity`, `@Index`, 적절한 컬럼 타입)
- N+1 문제 해결(`@EntityGraph`, `fetch join`)
- 트랜잭션 관리, 감사(Auditing), 페이지네이션, 커넥션 풀링

---

#### `postgres-patterns`
**역할**: PostgreSQL 쿼리 최적화 및 스키마 설계 (Supabase 모범 사례 기반)

- 인덱스 전략(B-tree, GiST, GIN), 파티셔닝
- Row Level Security(RLS) 구현
- 느린 쿼리 진단(`pg_stat_statements`), EXPLAIN ANALYZE 활용

---

### 워크플로우 & 품질 (9개)

#### `continuous-learning`
**역할**: 세션 종료 시 재사용 가능한 패턴 자동 추출 및 저장

- Stop 훅으로 세션 평가 자동 실행 (기본 10+ 메시지 기준)
- 패턴을 스킬 파일로 저장하여 미래 세션에서 재활용
- 학습 임계값 설정 가능 (노이즈 필터링)

---

#### `continuous-learning-v2`
**역할**: 원자적 Instinct 기반 학습 시스템 (v1의 고도화 버전)

- PreToolUse/PostToolUse 훅으로 100% 신뢰성 있는 세션 관찰
- 신뢰도 점수(Confidence Score)가 있는 작은 Instinct 단위로 학습
- Instinct → 스킬/커맨드/에이전트로 진화(`/evolve` 커맨드)

---

#### `eval-harness`
**역할**: Claude Code 세션에 대한 공식 평가(Eval) 프레임워크

- 구현 전 기대 동작 정의(Eval-Driven Development, EDD)
- 세션 중 지속적으로 eval 실행하여 퇴행 방지
- eval을 "AI 개발의 단위 테스트"로 취급하는 철학 적용

---

#### `iterative-retrieval`
**역할**: 서브에이전트 컨텍스트 문제 해결을 위한 점진적 검색 패턴

- 서브에이전트가 필요한 컨텍스트를 단계적으로 세분화하여 검색
- 초기 광범위 검색 → 관련 파일 식별 → 구체적 코드 검색 순서
- 멀티 에이전트 워크플로우에서 컨텍스트 효율성 극대화

---

#### `security-review`
**역할**: 코드 보안 검토 체크리스트 및 패턴

- 인증/인가, 사용자 입력, 시크릿, API 엔드포인트 관련 코드 작성 시 활성화
- OWASP Top 10 기반 체크리스트 제공
- 언어별 안전한 코딩 패턴 가이드

---

#### `strategic-compact`
**역할**: 논리적 작업 구간에서 수동 컨텍스트 압축 권고

- 임의적 자동 압축 대신 작업 단계 경계에서 `/compact` 실행 권장
- 중요 컨텍스트 손실 방지 및 다음 단계를 위한 깔끔한 상태 유지
- `Edit`/`Write` 도구 사용 시 압축 시점 제안 훅과 연동

---

#### `tdd-workflow`
**역할**: 범용 TDD 워크플로우 — 신기능, 버그 수정, 리팩토링 적용

- 단위·통합·E2E 테스트 80%+ 커버리지 강제
- 인터페이스 스캐폴딩 → 테스트 작성 → 최소 구현 → 리팩토링 순서
- 다양한 언어와 프레임워크에 공통 적용 가능한 TDD 원칙

---

#### `verification-loop`
**역할**: PR 생성 전 또는 주요 변경 후 종합 품질 검증

- Phase 1: 빌드 검증 → Phase 2: 정적 분석 → Phase 3: 테스트 실행 → Phase 4: 보안 스캔
- 각 단계 실패 시 다음 단계 진행 중단
- 배포 준비 상태 최종 확인을 위한 안전망

---

### 설정 & 메타 (2개)

#### `configure-ecc`
**역할**: ECC 대화형 설치 마법사

- 스킬과 규칙을 선택적으로 설치하도록 단계별 안내
- 사용자 레벨(`~/.claude/`) 또는 프로젝트 레벨 설치 선택 가능
- 설치 경로 검증 및 설치된 파일 최적화 옵션 제공

---

#### `project-guidelines-example`
**역할**: 프로젝트별 커스텀 가이드라인 스킬 예시 템플릿

- 실제 프로덕션 앱(Zenith AI) 기반의 구체적인 예시 포함
- 아키텍처 개요, 파일 구조, 코드 패턴, 테스트 요구사항 포함 방법 안내
- 팀 프로젝트에 맞는 커스텀 스킬 제작 시 참조 템플릿

---

## 스킬 요약표

| 스킬 | 카테고리 | 주요 대상 |
|------|----------|-----------|
| `backend-patterns` | 프레임워크 | Node.js, Express, Next.js |
| `coding-standards` | 언어 | TypeScript, JavaScript, React |
| `django-patterns` | 프레임워크 | Django, DRF |
| `django-security` | 보안 | Django |
| `django-tdd` | 테스팅 | Django + pytest |
| `django-verification` | 검증 | Django |
| `frontend-patterns` | 프레임워크 | React, Next.js |
| `golang-patterns` | 언어 | Go |
| `golang-testing` | 테스팅 | Go |
| `java-coding-standards` | 언어 | Java 17+ |
| `python-patterns` | 언어 | Python |
| `python-testing` | 테스팅 | Python + pytest |
| `springboot-patterns` | 프레임워크 | Spring Boot |
| `springboot-security` | 보안 | Spring Security |
| `springboot-tdd` | 테스팅 | Spring Boot + JUnit |
| `springboot-verification` | 검증 | Spring Boot |
| `clickhouse-io` | 데이터베이스 | ClickHouse |
| `jpa-patterns` | 데이터베이스 | JPA/Hibernate |
| `postgres-patterns` | 데이터베이스 | PostgreSQL |
| `continuous-learning` | 워크플로우 | 세션 자동 학습 |
| `continuous-learning-v2` | 워크플로우 | Instinct 기반 학습 |
| `eval-harness` | 워크플로우 | Eval 기반 개발 |
| `iterative-retrieval` | 워크플로우 | 멀티 에이전트 컨텍스트 |
| `security-review` | 보안 | 모든 언어 |
| `strategic-compact` | 워크플로우 | 컨텍스트 관리 |
| `tdd-workflow` | 테스팅 | 모든 언어 |
| `verification-loop` | 검증 | 모든 언어 |
| `configure-ecc` | 메타 | ECC 설치 |
| `project-guidelines-example` | 메타 | 커스텀 스킬 템플릿 |
