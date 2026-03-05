# Database - 도메인 엔티티 상세 설계

## 1. Store (매장)

| 컬럼 | 타입 | 제약조건 | 설명 |
|---|---|---|---|
| `id` | BIGSERIAL | PK | 매장 고유 ID |
| `name` | VARCHAR(100) | NOT NULL | 매장명 |
| `created_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 생성 시각 |
| `updated_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 수정 시각 |

**인덱스**: PK(id)

---

## 2. Admin (관리자)

| 컬럼 | 타입 | 제약조건 | 설명 |
|---|---|---|---|
| `id` | BIGSERIAL | PK | 관리자 고유 ID |
| `store_id` | BIGINT | FK → Store(id), NOT NULL | 소속 매장 |
| `username` | VARCHAR(50) | NOT NULL | 사용자명 |
| `password_hash` | VARCHAR(255) | NOT NULL | bcrypt 해싱된 비밀번호 |
| `login_attempts` | INT | NOT NULL, DEFAULT 0 | 연속 로그인 실패 횟수 |
| `locked_until` | TIMESTAMP | NULLABLE | 계정 잠금 해제 시각 |
| `created_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 생성 시각 |
| `updated_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 수정 시각 |

**인덱스**:
- PK(id)
- UNIQUE(store_id, username) — 매장 내 사용자명 고유

---

## 3. StoreTable (테이블)

| 컬럼 | 타입 | 제약조건 | 설명 |
|---|---|---|---|
| `id` | BIGSERIAL | PK | 테이블 고유 ID |
| `store_id` | BIGINT | FK → Store(id), NOT NULL | 소속 매장 |
| `table_number` | INT | NOT NULL | 테이블 번호 |
| `password_hash` | VARCHAR(255) | NOT NULL | bcrypt 해싱된 테이블 비밀번호 |
| `created_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 생성 시각 |
| `updated_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 수정 시각 |

**인덱스**:
- PK(id)
- UNIQUE(store_id, table_number) — 매장 내 테이블 번호 고유

---

## 4. TableSession (테이블 세션)

| 컬럼 | 타입 | 제약조건 | 설명 |
|---|---|---|---|
| `id` | BIGSERIAL | PK | 세션 고유 ID |
| `table_id` | BIGINT | FK → StoreTable(id), NOT NULL | 테이블 |
| `store_id` | BIGINT | FK → Store(id), NOT NULL | 매장 (비정규화, 조회 성능) |
| `is_active` | BOOLEAN | NOT NULL, DEFAULT TRUE | 활성 세션 여부 |
| `started_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 세션 시작 시각 |
| `ended_at` | TIMESTAMP | NULLABLE | 세션 종료 시각 (이용 완료 시) |

**인덱스**:
- PK(id)
- IDX(table_id, is_active) — 테이블별 활성 세션 조회
- IDX(store_id) — 매장별 세션 조회

---

## 5. Category (메뉴 카테고리)

| 컬럼 | 타입 | 제약조건 | 설명 |
|---|---|---|---|
| `id` | BIGSERIAL | PK | 카테고리 고유 ID |
| `store_id` | BIGINT | FK → Store(id), NOT NULL | 소속 매장 |
| `name` | VARCHAR(50) | NOT NULL | 카테고리명 |
| `display_order` | INT | NOT NULL, DEFAULT 0 | 노출 순서 |
| `created_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 생성 시각 |
| `updated_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 수정 시각 |

**인덱스**:
- PK(id)
- IDX(store_id, display_order) — 매장별 순서 조회

---

## 6. MenuItem (메뉴 항목)

| 컬럼 | 타입 | 제약조건 | 설명 |
|---|---|---|---|
| `id` | BIGSERIAL | PK | 메뉴 고유 ID |
| `store_id` | BIGINT | FK → Store(id), NOT NULL | 소속 매장 |
| `category_id` | BIGINT | FK → Category(id), NOT NULL | 소속 카테고리 |
| `name` | VARCHAR(100) | NOT NULL | 메뉴명 |
| `price` | INT | NOT NULL, CHECK(price >= 0 AND price <= 10000000) | 가격 (원) |
| `description` | TEXT | NULLABLE | 메뉴 설명 |
| `image_url` | VARCHAR(500) | NULLABLE | 이미지 URL |
| `display_order` | INT | NOT NULL, DEFAULT 0 | 노출 순서 |
| `created_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 생성 시각 |
| `updated_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 수정 시각 |

**인덱스**:
- PK(id)
- IDX(store_id, category_id, display_order) — 매장/카테고리별 순서 조회

---

## 7. Order (주문)

| 컬럼 | 타입 | 제약조건 | 설명 |
|---|---|---|---|
| `id` | BIGSERIAL | PK | 주문 고유 ID |
| `store_id` | BIGINT | FK → Store(id), NOT NULL | 매장 |
| `table_id` | BIGINT | FK → StoreTable(id), NOT NULL | 테이블 |
| `session_id` | BIGINT | FK → TableSession(id), NOT NULL | 세션 |
| `order_number` | INT | NOT NULL | 주문번호 (매장별 매일 리셋) |
| `status` | VARCHAR(20) | NOT NULL, DEFAULT 'PENDING' | 주문 상태 |
| `total_amount` | INT | NOT NULL, CHECK(total_amount >= 0) | 총 주문 금액 (원) |
| `created_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 주문 시각 |
| `updated_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 수정 시각 |

**status 허용값**: 'PENDING', 'PREPARING', 'COMPLETED'

**인덱스**:
- PK(id)
- IDX(store_id, session_id, created_at) — 매장/세션별 주문 조회
- IDX(store_id, status) — 매장별 상태 필터 조회
- UNIQUE(store_id, order_number, DATE(created_at)) — 매장별 일별 주문번호 고유

---

## 8. OrderItem (주문 항목)

| 컬럼 | 타입 | 제약조건 | 설명 |
|---|---|---|---|
| `id` | BIGSERIAL | PK | 주문 항목 고유 ID |
| `order_id` | BIGINT | FK → Order(id) ON DELETE CASCADE, NOT NULL | 주문 |
| `menu_item_id` | BIGINT | FK → MenuItem(id), NULLABLE | 원본 메뉴 (삭제 시 NULL) |
| `menu_name` | VARCHAR(100) | NOT NULL | 주문 시점 메뉴명 (스냅샷) |
| `quantity` | INT | NOT NULL, CHECK(quantity > 0) | 수량 |
| `unit_price` | INT | NOT NULL, CHECK(unit_price >= 0) | 주문 시점 단가 (스냅샷) |

**인덱스**:
- PK(id)
- IDX(order_id) — 주문별 항목 조회

---

## 9. OrderHistory (과거 주문 이력)

| 컬럼 | 타입 | 제약조건 | 설명 |
|---|---|---|---|
| `id` | BIGSERIAL | PK | 이력 고유 ID |
| `store_id` | BIGINT | FK → Store(id), NOT NULL | 매장 |
| `table_id` | BIGINT | FK → StoreTable(id), NOT NULL | 테이블 |
| `session_id` | BIGINT | NOT NULL | 세션 ID (FK 아님, 세션 삭제 후에도 유지) |
| `order_data_json` | JSONB | NOT NULL | 주문 데이터 JSON (주문목록, 메뉴, 수량, 금액) |
| `total_amount` | INT | NOT NULL | 세션 총 주문 금액 |
| `completed_at` | TIMESTAMP | NOT NULL, DEFAULT NOW() | 이용 완료 시각 |

**인덱스**:
- PK(id)
- IDX(store_id, table_id, completed_at DESC) — 매장/테이블별 이력 조회

---

## 10. 엔티티 관계 다이어그램 (ERD)

```
Store (1) ----< (N) Admin
Store (1) ----< (N) StoreTable
Store (1) ----< (N) Category
Store (1) ----< (N) MenuItem
Store (1) ----< (N) Order
Store (1) ----< (N) OrderHistory

StoreTable (1) ----< (N) TableSession
StoreTable (1) ----< (N) Order
StoreTable (1) ----< (N) OrderHistory

TableSession (1) ----< (N) Order

Category (1) ----< (N) MenuItem

Order (1) ----< (N) OrderItem
MenuItem (1) ----< (N) OrderItem (NULLABLE FK)
```

- 모든 엔티티는 store_id를 통해 멀티테넌트 데이터 격리
- OrderItem.menu_item_id는 NULLABLE (메뉴 삭제 후에도 주문 이력 유지)
- OrderHistory.session_id는 FK가 아님 (세션 데이터 독립 보존)
