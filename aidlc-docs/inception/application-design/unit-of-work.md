# 테이블오더 서비스 - 작업 단위 정의 (Unit of Work)

## 배포 모델

- **모놀리스**: 프론트엔드 1개 + 백엔드 1개
- **개발 방식**: 프론트엔드/백엔드 동시 진행 (Mock API 활용)
- **팀 구성**: 4명 (역할 기반 분배)

---

## 팀 역할 및 담당

| 역할 | 담당자 | CONSTRUCTION 주요 책임 |
| --- | --- | --- |
| Product Owner (PO) | 담당자 A | 비즈니스 로직 검증, 우선순위 조정, 기능 검증 |
| Developer | 담당자 B | 코드 리뷰, 설계 패턴 검증, 유닛 개발 |
| DevOps | 담당자 C | 인프라 설계 검증, CI/CD 파이프라인, 배포 |
| QA / 기획자 | 담당자 D | 테스트 전략 검토, 테스트 커버리지 확인, 품질 검증 |

---

## 작업 단위 목록

### Unit 1: Database Schema (데이터베이스)

| 항목 | 내용 |
| --- | --- |
| **기술 스택** | PostgreSQL + Flyway |
| **리뷰 담당** | PO (비즈니스 모델 검증), Developer (스키마 설계 검증) |

**책임 범위**:

- 데이터베이스 스키마 설계 및 생성 (9개 엔티티)
- Flyway 마이그레이션 스크립트 (V1~V10)
- 시드 데이터 (데모 매장, 관리자, 테이블, 메뉴)
- 인덱스 설계, 제약조건 정의

**엔티티 목록**: Store, Admin, StoreTable, TableSession, Category, MenuItem, Order, OrderItem, OrderHistory

**CONSTRUCTION 단계**: Functional Design → Code Generation

---

### Unit 2: Backend API (백엔드)

| 항목 | 내용 |
| --- | --- |
| **기술 스택** | Java + Spring Boot + JPA |
| **패키지 구조** | DDD (도메인별 패키지) |
| **리뷰 담당** | PO (비즈니스 로직), Developer (코드 품질), DevOps (인프라/보안) |

**책임 범위**:

- REST API 전체 (22개 엔드포인트 + SSE 2개)
- JWT 인증/인가 (테이블 로그인, 관리자 로그인)
- 비즈니스 로직 (주문 처리, 세션 관리, 메뉴 관리)
- SSE 실시간 이벤트 발행
- Spring Security 설정, CORS, 전역 예외 처리

**도메인 구조**:

- auth: 인증 (JWT 발급/검증, 테이블 로그인, 관리자 로그인)
- store: 매장 관리
- table: 테이블/세션 관리
- menu: 메뉴/카테고리 관리
- order: 주문 처리, SSE 이벤트
- common: 보안 설정, 예외 처리, 공통 DTO

**코드 구조**:

```
backend/
+-- src/main/java/com/tableorder/
|   +-- store/
|   +-- auth/
|   +-- table/
|   +-- menu/
|   +-- order/
|   +-- common/
+-- src/main/resources/
|   +-- application.yml
|   +-- db/migration/
+-- build.gradle (또는 pom.xml)
```

**CONSTRUCTION 단계**: Functional Design → NFR Requirements → NFR Design → Infrastructure Design → Code Generation

---

### Unit 3: Frontend App (프론트엔드)

| 항목 | 내용 |
| --- | --- |
| **기술 스택** | React + Vite + TypeScript + Zustand |
| **프로젝트 구조** | 단일 프로젝트, 라우팅으로 고객/관리자 분리 |
| **리뷰 담당** | PO (UX/기능 검증), QA/기획자 (테스트 시나리오) |

**책임 범위**:

- 고객용 UI (메뉴 조회, 장바구니, 주문, 주문 내역)
- 관리자용 UI (로그인, 대시보드, 테이블 관리, 메뉴 관리)
- Zustand 상태 관리 (인증, 장바구니, 주문, 메뉴)
- SSE 클라이언트 (실시간 주문 업데이트)
- Mock API (백엔드 완성 전 개발용)

**코드 구조**:

```
frontend/
+-- src/
|   +-- pages/
|   |   +-- customer/
|   |   +-- admin/
|   +-- components/
|   |   +-- common/
|   |   +-- customer/
|   |   +-- admin/
|   +-- stores/
|   +-- api/
|   +-- mocks/
|   +-- types/
|   +-- hooks/
|   +-- utils/
+-- index.html
+-- vite.config.ts
+-- tsconfig.json
+-- package.json
```

**CONSTRUCTION 단계**: Functional Design → Code Generation

---

## 역할별 CONSTRUCTION 단계 참여 매트릭스

| CONSTRUCTION 단계 | PO | Developer | DevOps | QA/기획자 |
| --- | --- | --- | --- | --- |
| Functional Design (DB) | 리뷰/승인 | 리뷰/승인 | - | - |
| Code Generation (DB) | - | 리뷰 | - | - |
| Functional Design (Backend) | 리뷰/승인 | 리뷰/승인 | - | - |
| NFR Requirements (Backend) | - | 리뷰 | 리뷰/승인 | 리뷰 |
| NFR Design (Backend) | - | 리뷰/승인 | 리뷰/승인 | - |
| Infrastructure Design (Backend) | - | 리뷰 | 리뷰/승인 | - |
| Code Generation (Backend) | 기능 검증 | 코드 리뷰 | 배포 설정 검증 | - |
| Functional Design (Frontend) | 리뷰/승인 | - | - | 리뷰/승인 |
| Code Generation (Frontend) | UX 검증 | - | - | 테스트 시나리오 |
| Build and Test | 최종 기능 검증 | 빌드 검증 | CI/CD 검증 | 테스트 실행/검증 |

---

## 개발 전략: 동시 진행 (Mock API)

### Phase 1: 공통 기반 (동시)

- DB: 스키마 설계 + 마이그레이션 스크립트
- Backend: 프로젝트 초기 설정, 공통 모듈(보안, 예외처리)
- Frontend: 프로젝트 초기 설정, 라우팅, 공통 컴포넌트, Mock API 정의

### Phase 2: 핵심 기능 (동시)

- Backend: 도메인별 API 구현 (auth → menu → order → table)
- Frontend: Mock API 기반 페이지 개발 (고객용 + 관리자용 동시)

### Phase 3: 통합

- Mock API를 실제 API로 교체
- SSE 실시간 통신 연동
- 통합 테스트

### Mock API 전략

- 프론트엔드 `src/mocks/` 디렉토리에 API 응답 Mock 정의
- API 클라이언트에서 환경 변수로 Mock/실제 전환
- component-methods.md의 API 시그니처를 기준으로 Mock 응답 생성
