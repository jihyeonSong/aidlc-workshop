# 테이블오더 서비스 - 스토리-단위 매핑

## 매핑 테이블

| 스토리 ID | 스토리명 | 우선순위 | Unit 1 (Database) | Unit 2 (Backend) | Unit 3 (Frontend) | 리뷰 담당 |
| --- | --- | --- | --- | --- | --- | --- |
| US-C01 | 테이블 태블릿 자동 로그인 | Must | O (StoreTable, TableSession) | O (auth API) | O (TableLoginPage) | PO, Developer |
| US-C02 | 메뉴 조회 및 탐색 | Must | O (Category, MenuItem) | O (menu API) | O (MenuPage, MenuCard) | PO, QA |
| US-C03 | 장바구니 관리 | Must | | | O (CartPage, CartStore) | PO, QA |
| US-C04 | 주문 생성 | Must | O (Order, OrderItem) | O (order API) | O (OrderConfirmPage, OrderResultPage) | PO, Developer |
| US-C05 | 주문 내역 조회 | Must | O (Order) | O (order API, SSE) | O (OrderHistoryPage) | PO, QA |
| US-A01 | 매장 인증 | Must | O (Admin) | O (auth API) | O (AdminLoginPage) | Developer, DevOps |
| US-A02 | 실시간 주문 모니터링 | Must | O (Order) | O (order API, SSE) | O (DashboardPage, TableCard) | PO, Developer |
| US-A03-1 | 테이블 초기 설정 | Must | O (StoreTable) | O (table API) | O (TableManagementPage) | Developer |
| US-A03-2 | 주문 삭제 | Must | O (Order) | O (order API) | O (DashboardPage) | PO, Developer |
| US-A03-3 | 테이블 세션 처리 | Must | O (TableSession, OrderHistory) | O (table API, order API) | O (DashboardPage) | PO, Developer |
| US-A03-4 | 과거 주문 내역 조회 | Should | O (OrderHistory) | O (order API) | O (TableManagementPage) | PO, QA |
| US-A04 | 메뉴 관리 | Must | O (Category, MenuItem) | O (menu API) | O (MenuManagementPage) | PO, QA |

---

## 단위별 스토리 수

| Unit | Must | Should | 합계 |
| --- | --- | --- | --- |
| Unit 1 (Database) | 9 | 1 | 10 |
| Unit 2 (Backend) | 10 | 1 | 11 |
| Unit 3 (Frontend) | 10 | 1 | 11 |

- US-C03(장바구니)은 클라이언트 전용 기능으로 Unit 3에만 해당
- 나머지 스토리는 모두 3개 Unit에 걸쳐 있음

---

## 역할별 리뷰 스토리 수

| 역할 | 리뷰 대상 스토리 수 | 주요 관심 영역 |
| --- | --- | --- |
| PO | 10 | 고객 경험, 비즈니스 로직, 기능 완성도 |
| Developer | 7 | 기술 설계, 코드 품질, 인증/보안 |
| DevOps | 1 | 인증 인프라, 배포 관련 |
| QA/기획자 | 5 | 사용자 시나리오, 테스트 커버리지 |

---

## 미할당 스토리 검증

미할당 스토리: **없음** - 12개 스토리 모두 최소 1개 이상의 Unit에 할당됨
