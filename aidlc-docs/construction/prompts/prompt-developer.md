# 💻 Developer - CONSTRUCTION PHASE 프롬프트

## 역할 소개

당신은 테이블오더 서비스 프로젝트의 Developer입니다.
코드 리뷰, 설계 패턴 검증, 유닛 개발을 담당합니다.

## Git 설정

```bash
# audit.md는 git 추적에서 제외 (개인 로컬 기록)
echo "aidlc-docs/audit.md" >> .gitignore

# DB 스키마 작업
git checkout -b construction/unit1-database

# Backend 작업
git checkout -b construction/unit2-backend
```

---

## 담당 단계 및 작업

### 1단계: Functional Design (DB) - 리뷰/승인

**브랜치**: `construction/unit1-database`

**리뷰 대상 파일**:
- `aidlc-docs/construction/database/functional-design/domain-entities.md`
- `aidlc-docs/construction/database/functional-design/business-rules.md`
- `aidlc-docs/construction/database/functional-design/business-logic-model.md`

**체크포인트**:
- [ ] 컬럼 타입이 적절한가? (BIGSERIAL, VARCHAR 길이, INT vs BIGINT 등)
- [ ] 인덱스 설계가 주요 쿼리 패턴을 커버하는가?
- [ ] FK 제약조건과 ON DELETE 동작(RESTRICT/CASCADE/SET NULL)이 적절한가?
- [ ] UNIQUE 제약조건이 비즈니스 규칙을 올바르게 반영하는가?
- [ ] Flyway 마이그레이션 순서가 FK 의존성을 준수하는가?
- [ ] 주문번호 동시성 제어(낙관적 재시도)가 실용적인가?

**프롬프트 (AI에게 전달)**:
```
나는 Developer 역할입니다. Database Functional Design 산출물을 기술 관점에서 리뷰합니다.

다음 파일들을 읽고 기술적 적합성을 검증해주세요:
- aidlc-docs/construction/database/functional-design/domain-entities.md
- aidlc-docs/construction/database/functional-design/business-rules.md
- aidlc-docs/construction/database/functional-design/business-logic-model.md

검증 기준:
1. 컬럼 타입, 인덱스, 제약조건이 PostgreSQL 모범 사례를 따르는가?
2. Flyway 마이그레이션 순서가 FK 의존성을 준수하는가?
3. 주문번호 동시성 제어 방식이 실용적인가?
4. 성능 병목이 될 수 있는 설계가 있는가?

리뷰 결과를 정리해주세요.
```

---

### 2단계: Code Generation (DB) - 리뷰

**브랜치**: `construction/unit1-database`

**체크포인트**:
- [ ] SQL 문법이 PostgreSQL 호환인가?
- [ ] 마이그레이션 스크립트가 멱등성을 보장하는가?
- [ ] 시드 데이터가 올바른 FK 참조를 가지는가?

**프롬프트 (AI에게 전달)**:
```
나는 Developer 역할입니다. Database Code Generation을 진행해주세요.

AI-DLC 워크플로우의 Code Generation 단계를 실행합니다.
대상: Unit 1 (Database)

참조 문서:
- aidlc-docs/construction/database/functional-design/domain-entities.md
- aidlc-docs/construction/database/functional-design/business-rules.md
- aidlc-docs/construction/database/functional-design/business-logic-model.md

Flyway 마이그레이션 스크립트(V1~V10)를 생성해주세요.
코드 위치: backend/src/main/resources/db/migration/
```

---

### 3단계: Functional Design (Backend) - 리뷰/승인

**브랜치**: `construction/unit2-backend`

**체크포인트**:
- [ ] DDD 패키지 구조가 도메인 경계를 올바르게 반영하는가?
- [ ] 서비스 간 의존성이 순환 참조 없이 설계되었는가?
- [ ] JPA 엔티티 매핑이 DB 스키마와 일치하는가?
- [ ] SSE 이벤트 모델이 실시간 요구사항을 충족하는가?

**프롬프트 (AI에게 전달)**:
```
나는 Developer 역할입니다. Backend Functional Design을 진행해주세요.

AI-DLC 워크플로우의 Functional Design 단계를 실행합니다.
대상: Unit 2 (Backend API)

참조 문서:
- aidlc-docs/inception/application-design/components.md
- aidlc-docs/inception/application-design/component-methods.md
- aidlc-docs/inception/application-design/services.md
- aidlc-docs/construction/database/functional-design/domain-entities.md

기술 스택: Java + Spring Boot + JPA + PostgreSQL
패키지 구조: DDD (도메인별)
```

---

### 4단계: NFR Design (Backend) - 리뷰/승인

**체크포인트**:
- [ ] 보안 패턴(JWT, bcrypt, CORS)이 구현 가능한가?
- [ ] SSE 성능 패턴이 적절한가?
- [ ] 에러 처리 패턴이 일관적인가?

---

### 5단계: Code Generation (Backend) - 코드 리뷰

**브랜치**: `construction/unit2-backend`

**체크포인트**:
- [ ] 코드 품질: 네이밍 컨벤션, 패키지 구조, 레이어 분리
- [ ] 설계 패턴: DDD, Repository 패턴, Service 레이어
- [ ] 보안: SQL Injection 방지, XSS 방지, 입력 검증
- [ ] 테스트 가능성: 의존성 주입, 인터페이스 분리

**프롬프트 (AI에게 전달)**:
```
나는 Developer 역할입니다. Backend Code Generation을 진행해주세요.

AI-DLC 워크플로우의 Code Generation 단계를 실행합니다.
대상: Unit 2 (Backend API)

기술 스택: Java 17+ / Spring Boot 3.x / JPA / PostgreSQL
빌드 도구: Gradle (Kotlin DSL)

참조 문서:
- aidlc-docs/inception/application-design/component-methods.md (API 시그니처)
- aidlc-docs/inception/application-design/services.md (서비스 설계)
- aidlc-docs/construction/database/functional-design/domain-entities.md (엔티티)

코드 위치: backend/
```

---

### 6단계: Build and Test - 빌드 검증

**체크포인트**:
- [ ] 컴파일 성공 여부
- [ ] 의존성 충돌 없는지
- [ ] 단위 테스트 통과 여부

---

## 참조 문서

| 문서 | 경로 | 용도 |
| --- | --- | --- |
| 컴포넌트 정의 | `aidlc-docs/inception/application-design/components.md` | 백엔드 구조 확인 |
| API 시그니처 | `aidlc-docs/inception/application-design/component-methods.md` | 엔드포인트/메서드 확인 |
| 서비스 설계 | `aidlc-docs/inception/application-design/services.md` | 오케스트레이션 패턴 |
| DB 엔티티 | `aidlc-docs/construction/database/functional-design/domain-entities.md` | 스키마 참조 |
| 비즈니스 규칙 | `aidlc-docs/construction/database/functional-design/business-rules.md` | 검증 규칙 |
| 작업 단위 | `aidlc-docs/inception/application-design/unit-of-work.md` | 역할 매트릭스 |
