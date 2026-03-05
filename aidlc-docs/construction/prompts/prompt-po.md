# 🎯 Product Owner (PO) - CONSTRUCTION PHASE 프롬프트

## 역할 소개

당신은 테이블오더 서비스 프로젝트의 Product Owner입니다.
비즈니스 로직 검증, 우선순위 조정, 기능 검증을 담당합니다.

## Git Branch

```bash
git checkout -b construction/po-review
```

---

## 담당 단계 및 작업

### 1단계: Functional Design (DB) - 리뷰/승인

**브랜치**: `construction/po-review` (위에서 생성한 브랜치 사용)

**리뷰 대상 파일**:
- `aidlc-docs/construction/database/functional-design/domain-entities.md`
- `aidlc-docs/construction/database/functional-design/business-rules.md`
- `aidlc-docs/construction/database/functional-design/business-logic-model.md`

**체크포인트**:
- [ ] 9개 엔티티(Store, Admin, StoreTable, TableSession, Category, MenuItem, Order, OrderItem, OrderHistory)가 요구사항의 비즈니스 개념을 모두 반영하는가?
- [ ] 주문 상태 전이(PENDING → PREPARING → COMPLETED)가 실제 매장 운영 흐름과 일치하는가?
- [ ] 주문번호 매일 리셋 규칙이 매장 운영에 적합한가?
- [ ] 시드 데이터(데모 매장, 메뉴)가 테스트에 충분한가?
- [ ] 멀티테넌트 격리(store_id 기반)가 비즈니스 요구를 충족하는가?

**프롬프트 (AI에게 전달)**:
```
나는 PO 역할입니다. Database Functional Design 산출물을 비즈니스 관점에서 리뷰합니다.

다음 파일들을 읽고 비즈니스 적합성을 검증해주세요:
- aidlc-docs/construction/database/functional-design/domain-entities.md
- aidlc-docs/construction/database/functional-design/business-rules.md
- aidlc-docs/construction/database/functional-design/business-logic-model.md

검증 기준:
1. 엔티티가 사용자 스토리(aidlc-docs/inception/user-stories/stories.md)를 모두 지원하는가?
2. 비즈니스 규칙이 실제 매장 운영 시나리오를 반영하는가?
3. 누락된 비즈니스 개념이 있는가?

리뷰 결과를 정리해주세요.
```

---

### 2단계: Functional Design (Backend) - 리뷰/승인

**리뷰 대상**: Backend Functional Design 산출물 (생성 후 리뷰)

**체크포인트**:
- [ ] 22개 REST API가 사용자 스토리의 모든 기능을 커버하는가?
- [ ] 주문 생성 → 상태 변경 → 이용 완료 흐름이 비즈니스 로직과 일치하는가?
- [ ] 관리자 권한과 고객 권한이 명확히 분리되어 있는가?

**프롬프트 (AI에게 전달)**:
```
나는 PO 역할입니다. Backend Functional Design 산출물을 비즈니스 관점에서 리뷰합니다.

다음을 검증해주세요:
1. API 엔드포인트가 사용자 스토리(stories.md)의 모든 기능을 지원하는가?
2. 주문 처리 흐름(생성→상태변경→이용완료)이 비즈니스 요구와 일치하는가?
3. 고객/관리자 권한 분리가 적절한가?

참조: aidlc-docs/inception/application-design/component-methods.md
참조: aidlc-docs/inception/user-stories/stories.md
```

---

### 3단계: Code Generation (Backend) - 기능 검증

**체크포인트**:
- [ ] 비즈니스 로직이 설계대로 구현되었는가?
- [ ] 주문번호 생성, 세션 관리, 상태 전이가 올바른가?

---

### 4단계: Functional Design (Frontend) - 리뷰/승인

**체크포인트**:
- [ ] 고객 화면 흐름: 메뉴 조회 → 장바구니 → 주문 확인 → 결과 → 주문 내역
- [ ] 관리자 화면 흐름: 로그인 → 대시보드(실시간) → 테이블/메뉴 관리
- [ ] 사용자 경험이 직관적이고 매장 운영에 적합한가?

**프롬프트 (AI에게 전달)**:
```
나는 PO 역할입니다. Frontend Functional Design 산출물을 UX/비즈니스 관점에서 리뷰합니다.

검증 기준:
1. 고객 화면 흐름이 직관적인가? (메뉴→장바구니→주문→결과)
2. 관리자 대시보드가 실시간 주문 모니터링에 적합한가?
3. 사용자 스토리의 인수 기준(Acceptance Criteria)을 모두 충족하는가?

참조: aidlc-docs/inception/user-stories/stories.md
```

---

### 5단계: Code Generation (Frontend) - UX 검증

**체크포인트**:
- [ ] 화면 흐름과 인터랙션이 설계대로 구현되었는가?
- [ ] 에러 상태, 로딩 상태 처리가 적절한가?

---

### 6단계: Build and Test - 최종 기능 검증

**체크포인트**:
- [ ] 전체 기능이 요구사항(requirements.md)대로 동작하는가?
- [ ] 사용자 스토리의 인수 기준을 모두 통과하는가?
- [ ] 비즈니스 가치가 충분히 전달되는가?

---

## 참조 문서

| 문서 | 경로 | 용도 |
| --- | --- | --- |
| 요구사항 | `aidlc-docs/inception/requirements/requirements.md` | 기능 요구사항 확인 |
| 사용자 스토리 | `aidlc-docs/inception/user-stories/stories.md` | 인수 기준 확인 |
| 페르소나 | `aidlc-docs/inception/user-stories/personas.md` | 사용자 관점 확인 |
| 컴포넌트 메서드 | `aidlc-docs/inception/application-design/component-methods.md` | API 시그니처 확인 |
| 작업 단위 | `aidlc-docs/inception/application-design/unit-of-work.md` | 역할 매트릭스 확인 |
