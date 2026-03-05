# 테이블오더 서비스 - 컴포넌트 의존성

## 1. 서비스 의존성 매트릭스

| 서비스 | AuthService | StoreService | MenuService | TableService | OrderService | OrderSseService |
|---|---|---|---|---|---|---|
| **AuthService** | - | | | | | |
| **StoreService** | | - | | | | |
| **MenuService** | | | - | | | |
| **TableService** | | | | - | O | |
| **OrderService** | | | | O | - | O |
| **OrderSseService** | | | | | | - |

- O = 의존 관계 있음
- TableService ↔ OrderService: 양방향 의존 (세션 종료 시 주문 이력 이동)
  - 순환 의존 해결: 이벤트 기반 또는 인터페이스 분리로 처리

---

## 2. 통신 패턴

### 2.1 클라이언트 → 서버 (REST)

```
+-------------------+         +-------------------+
|                   |  REST   |                   |
|  React Frontend   | ------> |  Spring Boot API  |
|  (Vite + TS)      |  HTTPS  |  (Java)           |
|                   |         |                   |
+-------------------+         +-------------------+
```

- 모든 API 호출은 HTTPS + JWT 인증 헤더
- Content-Type: application/json
- 에러 응답: 공통 ApiResponse 래퍼

### 2.2 서버 → 클라이언트 (SSE)

```
+-------------------+         +-------------------+
|                   |   SSE   |                   |
|  React Frontend   | <------ |  Spring Boot API  |
|  (EventSource)    |  HTTPS  |  (SseEmitter)     |
|                   |         |                   |
+-------------------+         +-------------------+
```

- 관리자: 매장 전체 주문 이벤트 수신
- 고객: 해당 테이블 주문 상태 이벤트 수신
- 이벤트 타입: ORDER_CREATED, ORDER_STATUS_CHANGED, ORDER_DELETED, SESSION_COMPLETED

### 2.3 서버 → 데이터베이스

```
+-------------------+         +-------------------+
|                   |  JDBC   |                   |
|  Spring Boot API  | ------> |   PostgreSQL      |
|  (JPA/Hibernate)  |   TLS   |                   |
|                   |         |                   |
+-------------------+         +-------------------+
```

- Spring Data JPA + Hibernate
- 커넥션 풀: HikariCP
- TLS 암호화 연결

---

## 3. 데이터 흐름 다이어그램

### 3.1 고객 주문 흐름

```
+----------+    +----------+    +----------+    +----------+    +----------+
|          |    |          |    |          |    |          |    |          |
|  고객    |--->|  메뉴    |--->| 장바구니 |--->|   주문   |--->| 주문내역 |
|  로그인  |    |  조회    |    |  관리    |    |   생성   |    |   조회   |
|          |    |          |    |          |    |          |    |          |
+----------+    +----------+    +----------+    +----------+    +----------+
     |               |              |               |               |
     v               v              v               v               v
 AuthService    MenuService     (Client)      OrderService     OrderService
                                  Local       TableService    OrderSseService
                                Storage      OrderSseService
```

### 3.2 관리자 운영 흐름

```
+----------+    +----------+    +----------+    +----------+
|          |    |          |    |          |    |          |
|  관리자  |--->|  주문    |--->|  테이블  |--->|   메뉴   |
|  로그인  |    | 모니터링 |    |   관리   |    |   관리   |
|          |    |  (SSE)   |    |          |    |          |
+----------+    +----------+    +----------+    +----------+
     |               |              |               |
     v               v              v               v
 AuthService    OrderService   TableService    MenuService
               OrderSseService  OrderService
```

---

## 4. 프론트엔드 라우팅 구조

```
/                          -> /menu (리다이렉트)
/login                     -> TableLoginPage (태블릿 초기 설정)
/menu                      -> MenuPage (기본 화면)
/cart                      -> CartPage
/order/confirm             -> OrderConfirmPage
/order/result/:orderId     -> OrderResultPage
/orders                    -> OrderHistoryPage
/admin/login               -> AdminLoginPage
/admin/dashboard           -> DashboardPage
/admin/menus               -> MenuManagementPage
/admin/tables              -> TableManagementPage
```

- `/login`, `/menu`, `/cart`, `/order/*`, `/orders`: 테이블 JWT 인증 필요
- `/admin/*`: 관리자 JWT 인증 필요
- 인증 실패 시 각각의 로그인 페이지로 리다이렉트
