# 테이블오더 서비스 - 작업 단위 의존성

## 1. 의존성 매트릭스

| 단위 | Unit 1 (Database) | Unit 2 (Backend) | Unit 3 (Frontend) |
| --- | --- | --- | --- |
| **Unit 1 (Database)** | - | | |
| **Unit 2 (Backend)** | O (DB 필요) | - | |
| **Unit 3 (Frontend)** | | O (API 호출) | - |

- O = 의존 관계
- Unit 2 → Unit 1: 백엔드가 DB에 의존
- Unit 3 → Unit 2: 프론트엔드가 백엔드 API에 의존 (Mock으로 해소)

---

## 2. CONSTRUCTION PHASE 단위별 실행 순서

AI-DLC CONSTRUCTION PHASE에서는 각 Unit을 순차적으로 설계/구현합니다:

| 순서 | Unit | CONSTRUCTION 단계 | 리뷰 담당 |
| --- | --- | --- | --- |
| 1 | Unit 1 (Database) | Functional Design → Code Generation | PO, Developer |
| 2 | Unit 2 (Backend) | Functional Design → NFR Requirements → NFR Design → Infrastructure Design → Code Generation | PO, Developer, DevOps |
| 3 | Unit 3 (Frontend) | Functional Design → Code Generation | PO, QA/기획자 |
| 4 | 전체 | Build and Test | 전원 참여 |

- Unit 1(DB)을 먼저 설계하여 데이터 모델 확정
- Unit 2(Backend)에서 보안/성능/인프라 설계 포함 (NFR + Infrastructure)
- Unit 3(Frontend)는 NFR/Infrastructure 불필요 (백엔드에서 처리)

---

## 3. 역할별 단계 참여 상세

### PO (Product Owner)

| 단계 | 참여 방식 | 핵심 체크포인트 |
| --- | --- | --- |
| Functional Design (DB) | 리뷰/승인 | 비즈니스 엔티티가 요구사항을 충족하는지 |
| Functional Design (Backend) | 리뷰/승인 | API가 사용자 스토리를 구현하는지 |
| Functional Design (Frontend) | 리뷰/승인 | UI 흐름이 사용자 경험에 적합한지 |
| Code Generation (Backend) | 기능 검증 | 비즈니스 로직이 올바르게 구현되었는지 |
| Code Generation (Frontend) | UX 검증 | 화면 흐름과 인터랙션이 적절한지 |
| Build and Test | 최종 기능 검증 | 전체 기능이 요구사항대로 동작하는지 |

### Developer

| 단계 | 참여 방식 | 핵심 체크포인트 |
| --- | --- | --- |
| Functional Design (DB) | 리뷰/승인 | 스키마 설계, 인덱스, 제약조건 적절성 |
| Code Generation (DB) | 리뷰 | SQL 문법, 마이그레이션 순서 |
| Functional Design (Backend) | 리뷰/승인 | 도메인 모델, 서비스 설계 패턴 |
| NFR Design (Backend) | 리뷰/승인 | 보안/성능 패턴 구현 가능성 |
| Code Generation (Backend) | 코드 리뷰 | 코드 품질, 설계 패턴 준수 |
| Build and Test | 빌드 검증 | 컴파일, 의존성, 단위 테스트 |

### DevOps

| 단계 | 참여 방식 | 핵심 체크포인트 |
| --- | --- | --- |
| NFR Requirements (Backend) | 리뷰/승인 | 인프라 관련 NFR 실현 가능성 |
| NFR Design (Backend) | 리뷰/승인 | 배포 환경에서의 NFR 패턴 적합성 |
| Infrastructure Design (Backend) | 리뷰/승인 | AWS 리소스 설계, 비용, 확장성 |
| Code Generation (Backend) | 배포 설정 검증 | Dockerfile, 환경 설정 |
| Build and Test | CI/CD 검증 | 파이프라인 설정, 배포 스크립트 |

### QA / 기획자

| 단계 | 참여 방식 | 핵심 체크포인트 |
| --- | --- | --- |
| NFR Requirements (Backend) | 리뷰 | 테스트 가능한 NFR 기준 정의 |
| Functional Design (Frontend) | 리뷰/승인 | 사용자 시나리오 커버리지 |
| Code Generation (Frontend) | 테스트 시나리오 | UI 테스트 케이스 도출 |
| Build and Test | 테스트 실행/검증 | 기능 테스트, 품질 검증 |

---

## 4. 개발 순서 및 통합 전략

### 동시 진행 타임라인

```
Phase 1: 기반 설정
+------------------------------------------+
| Unit 1: DB 스키마 설계 + 마이그레이션     |
| Unit 2: 프로젝트 설정 + 공통 모듈        |
| Unit 3: 프로젝트 설정 + Mock API + 라우팅 |
+------------------------------------------+

Phase 2: 핵심 기능 개발 (동시)
+------------------------------------------+
| Unit 2: 도메인별 API 구현                 |
| Unit 3: 페이지 개발 (Mock API 기반)       |
+------------------------------------------+

Phase 3: 통합 + 테스트
+------------------------------------------+
| Mock API → 실제 API 교체                  |
| SSE 실시간 통신 연동                      |
| 통합 테스트 + 버그 수정                   |
+------------------------------------------+
```

### 통합 포인트

| 통합 항목 | 시점 | 관련 단위 | 설명 |
| --- | --- | --- | --- |
| DB 연동 | Phase 1 | Unit 2 + Unit 1 | JPA 엔티티 ↔ DB 스키마 매핑 |
| API 연동 | Phase 3 | Unit 3 + Unit 2 | Mock → 실제 API 교체 |
| SSE 연동 | Phase 3 | Unit 3 + Unit 2 | 실시간 주문 스트림 연결 |
| 인증 연동 | Phase 3 | Unit 3 + Unit 2 | JWT 토큰 발급/검증 흐름 |

### 통합 리스크 및 완화

| 리스크 | 영향 | 완화 방안 |
| --- | --- | --- |
| API 스펙 변경 | Mock과 실제 API 불일치 | component-methods.md를 단일 진실 소스(SSOT)로 사용 |
| SSE 연동 복잡도 | 실시간 통신 버그 | Phase 2 후반에 SSE 프로토타입 먼저 연동 |
| DB 스키마 변경 | 마이그레이션 충돌 | Flyway 버전 관리, 스키마 변경 시 팀 공유 |
