# 🧪 QA / 기획자 - CONSTRUCTION PHASE 프롬프트

## 역할 소개

당신은 테이블오더 서비스 프로젝트의 QA / 기획자입니다.
테스트 전략 검토, 테스트 커버리지 확인, 품질 검증을 담당합니다.

## Git 설정

```bash
# audit.md는 git 추적에서 제외 (개인 로컬 기록)
echo "aidlc-docs/audit.md" >> .gitignore

# Frontend 작업
git checkout -b construction/unit3-frontend

# QA/테스트 작업
git checkout -b construction/qa-test
```

---

## 담당 단계 및 작업

### 1단계: NFR Requirements (Backend) - 리뷰

**브랜치**: `construction/qa-test`

**체크포인트**:
- [ ] NFR 요구사항이 테스트 가능한 기준으로 정의되어 있는가?
- [ ] 성능 기준(SSE 2초 이내)이 측정 가능한가?
- [ ] 보안 요구사항이 테스트 케이스로 변환 가능한가?

**프롬프트 (AI에게 전달)**:
```
나는 QA/기획자 역할입니다. Backend NFR Requirements를 테스트 관점에서 리뷰합니다.

검증 기준:
1. 각 NFR 요구사항이 테스트 가능한 수치/기준으로 정의되어 있는가?
2. 성능 테스트 시나리오를 도출할 수 있는가?
3. 보안 테스트 체크리스트를 만들 수 있는가?

참조:
- NFR Requirements 산출물 (생성 후 참조)
- aidlc-docs/inception/requirements/requirements.md
```

---

### 2단계: Functional Design (Frontend) - 리뷰/승인

**브랜치**: `construction/unit3-frontend`

**체크포인트**:
- [ ] 고객 시나리오가 모두 커버되는가?
  - 메뉴 조회 → 장바구니 추가 → 주문 확인 → 주문 결과 → 주문 내역
- [ ] 관리자 시나리오가 모두 커버되는가?
  - 로그인 → 대시보드 모니터링 → 주문 상태 변경 → 테이블 이용 완료
  - 메뉴 관리 (등록/수정/삭제)
  - 테이블 관리 (등록/수정)
  - 과거 주문 내역 조회
- [ ] 에러 시나리오가 정의되어 있는가? (네트워크 오류, 인증 만료 등)
- [ ] 접근성(Accessibility) 요구사항이 반영되어 있는가?

**프롬프트 (AI에게 전달)**:
```
나는 QA/기획자 역할입니다. Frontend Functional Design을 진행해주세요.

AI-DLC 워크플로우의 Functional Design 단계를 실행합니다.
대상: Unit 3 (Frontend App)

기술 스택: React + Vite + TypeScript + Zustand

참조 문서:
- aidlc-docs/inception/application-design/components.md (페이지/컴포넌트 목록)
- aidlc-docs/inception/application-design/component-methods.md (API 시그니처)
- aidlc-docs/inception/user-stories/stories.md (사용자 스토리)
- aidlc-docs/inception/user-stories/personas.md (페르소나)

특히 다음을 포함해주세요:
1. 각 페이지의 사용자 인터랙션 흐름
2. 에러 상태 처리 (네트워크 오류, 인증 만료, 빈 데이터)
3. 로딩 상태 처리
4. 폼 검증 규칙
```

---

### 3단계: Code Generation (Frontend) - 테스트 시나리오

**브랜치**: `construction/unit3-frontend`

**체크포인트**:
- [ ] 각 페이지의 핵심 사용자 흐름이 구현되었는가?
- [ ] 에러/로딩 상태가 적절히 처리되는가?
- [ ] 테스트 시나리오를 도출할 수 있는가?

**프롬프트 (AI에게 전달)**:
```
나는 QA/기획자 역할입니다. Frontend Code Generation을 진행해주세요.

AI-DLC 워크플로우의 Code Generation 단계를 실행합니다.
대상: Unit 3 (Frontend App)

기술 스택: React 18+ / Vite / TypeScript / Zustand
패키지 매니저: npm

참조 문서:
- aidlc-docs/inception/application-design/components.md (컴포넌트 목록)
- aidlc-docs/inception/application-design/component-methods.md (API 시그니처)
- Frontend Functional Design 산출물 (생성 후 참조)

코드 위치: frontend/

Mock API도 함께 생성해주세요 (src/mocks/).
```

---

### 4단계: Build and Test - 테스트 실행/검증

**브랜치**: `construction/qa-test`

**체크포인트**:
- [ ] 기능 테스트: 사용자 스토리별 인수 기준 통과 여부
- [ ] 통합 테스트: Frontend ↔ Backend API 연동
- [ ] SSE 테스트: 실시간 주문 업데이트 동작 확인
- [ ] 보안 테스트: 인증/인가, 멀티테넌트 격리
- [ ] 에러 시나리오 테스트: 네트워크 오류, 동시성 충돌

**프롬프트 (AI에게 전달)**:
```
나는 QA/기획자 역할입니다. Build and Test 단계를 진행해주세요.

AI-DLC 워크플로우의 Build and Test 단계를 실행합니다.

다음 테스트 지침을 생성해주세요:
1. 기능 테스트 (사용자 스토리 12개 기반)
2. 통합 테스트 (Frontend ↔ Backend ↔ Database)
3. SSE 실시간 통신 테스트
4. 보안 테스트 (SECURITY-01~15 기반)
5. 성능 테스트 (SSE 응답 시간)

참조:
- aidlc-docs/inception/user-stories/stories.md (인수 기준)
- aidlc-docs/inception/requirements/requirements.md (요구사항)
```

---

## 테스트 시나리오 템플릿

각 사용자 스토리에 대해 다음 형식으로 테스트 시나리오를 작성합니다:

```
## [스토리 ID] - [스토리명]

### 정상 시나리오
- Given: [사전 조건]
- When: [사용자 행동]
- Then: [기대 결과]

### 에러 시나리오
- Given: [사전 조건]
- When: [비정상 행동]
- Then: [에러 처리 결과]
```

---

## 참조 문서

| 문서 | 경로 | 용도 |
| --- | --- | --- |
| 사용자 스토리 | `aidlc-docs/inception/user-stories/stories.md` | 인수 기준/테스트 기준 |
| 페르소나 | `aidlc-docs/inception/user-stories/personas.md` | 사용자 관점 테스트 |
| 요구사항 | `aidlc-docs/inception/requirements/requirements.md` | NFR/보안 테스트 기준 |
| 컴포넌트 | `aidlc-docs/inception/application-design/components.md` | 프론트엔드 구조 확인 |
| API 시그니처 | `aidlc-docs/inception/application-design/component-methods.md` | API 테스트 기준 |
| 작업 단위 | `aidlc-docs/inception/application-design/unit-of-work.md` | 역할 매트릭스 |
