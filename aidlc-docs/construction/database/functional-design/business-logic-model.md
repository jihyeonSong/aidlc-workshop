# Database - 비즈니스 로직 모델

## 1. 시드 데이터 구조

### 1.1 매장 초기 데이터

매장 생성 시 자동으로 생성되는 기본 데이터입니다.

```sql
-- 매장 생성
INSERT INTO store (name) VALUES ('데모 매장');

-- 관리자 계정 (비밀번호: admin1234, bcrypt 해싱)
INSERT INTO admin (store_id, username, password_hash)
VALUES (1, 'admin', '$2a$10$...');
```

### 1.2 테이블 시드 데이터

```sql
-- 테이블 5개 생성 (비밀번호: 각 테이블별 설정, bcrypt 해싱)
INSERT INTO store_table (store_id, table_number, password_hash) VALUES
(1, 1, '$2a$10$...'),
(1, 2, '$2a$10$...'),
(1, 3, '$2a$10$...'),
(1, 4, '$2a$10$...'),
(1, 5, '$2a$10$...');
```

### 1.3 메뉴 카테고리 시드 데이터

```sql
-- 카테고리 생성
INSERT INTO category (store_id, name, display_order) VALUES
(1, '메인 메뉴', 1),
(1, '사이드', 2),
(1, '음료', 3);
```

### 1.4 메뉴 항목 시드 데이터

```sql
-- 메인 메뉴
INSERT INTO menu_item (store_id, category_id, name, price, description, display_order) VALUES
(1, 1, '불고기 정식', 12000, '소불고기와 밑반찬 세트', 1),
(1, 1, '김치찌개', 9000, '돼지고기 김치찌개', 2),
(1, 1, '된장찌개', 8000, '두부 된장찌개', 3),
(1, 1, '비빔밥', 10000, '야채 비빔밥', 4);

-- 사이드
INSERT INTO menu_item (store_id, category_id, name, price, description, display_order) VALUES
(1, 2, '계란말이', 5000, '치즈 계란말이', 1),
(1, 2, '김치전', 6000, '바삭한 김치전', 2);

-- 음료
INSERT INTO menu_item (store_id, category_id, name, price, description, display_order) VALUES
(1, 3, '콜라', 2000, NULL, 1),
(1, 3, '사이다', 2000, NULL, 2),
(1, 3, '맥주', 5000, '생맥주 500ml', 3);
```

---

## 2. 마이그레이션 전략

### 2.1 마이그레이션 도구

Flyway를 사용하여 데이터베이스 스키마 버전을 관리합니다.

### 2.2 마이그레이션 파일 구조

```
src/main/resources/db/migration/
+-- V1__create_store_table.sql
+-- V2__create_admin_table.sql
+-- V3__create_store_table_table.sql
+-- V4__create_table_session_table.sql
+-- V5__create_category_table.sql
+-- V6__create_menu_item_table.sql
+-- V7__create_order_table.sql
+-- V8__create_order_item_table.sql
+-- V9__create_order_history_table.sql
+-- V10__seed_demo_data.sql
```

### 2.3 마이그레이션 순서 원칙

| 순서 | 원칙 | 설명 |
| --- | --- | --- |
| 1 | 부모 테이블 우선 | FK 참조 대상 테이블을 먼저 생성 |
| 2 | 인덱스 포함 | 테이블 생성 시 인덱스도 함께 생성 |
| 3 | 시드 데이터 마지막 | 모든 테이블 생성 후 시드 데이터 삽입 |
| 4 | 롤백 가능 | 각 마이그레이션은 독립적으로 롤백 가능하도록 설계 |

### 2.4 환경별 전략

| 환경 | 시드 데이터 | 마이그레이션 실행 |
| --- | --- | --- |
| 개발 (dev) | 데모 데이터 포함 | 자동 실행 (spring.flyway.enabled=true) |
| 테스트 (test) | 테스트 전용 데이터 | 자동 실행 |
| 운영 (prod) | 시드 데이터 없음 | 수동 승인 후 실행 |

### 2.5 스키마 변경 규칙

- 기존 컬럼 삭제/변경 시 반드시 새 마이그레이션 파일 추가 (기존 파일 수정 금지)
- 컬럼 추가 시 DEFAULT 값 또는 NULLABLE 설정 필수
- 대규모 데이터 마이그레이션은 별도 스크립트로 분리
- 팀 내 스키마 변경 시 사전 공유 필수 (Flyway 버전 충돌 방지)

---

## 3. 주문번호 생성 로직

### 3.1 알고리즘

```
함수: generateOrderNumber(storeId)
  1. 현재 날짜(KST) 기준으로 해당 매장의 오늘 최대 주문번호 조회
  2. MAX(order_number) + 1 반환 (없으면 1 반환)
  3. UNIQUE 제약조건으로 동시성 충돌 시 재시도
```

### 3.2 동시성 제어

- UNIQUE(store_id, order_number, DATE(created_at)) 제약조건 활용
- INSERT 실패 시 (중복) 재시도 로직 (최대 3회)
- 비관적 잠금(Pessimistic Lock) 대신 낙관적 재시도 방식 채택 (성능 우선)

---

## 4. 세션 종료 시 데이터 이동 로직

### 4.1 이용 완료 처리 알고리즘

```
함수: completeSession(sessionId)
  1. 해당 세션의 모든 주문 + 주문항목 조회
  2. 주문 데이터를 JSON으로 직렬화
  3. OrderHistory 레코드 생성 (order_data_json에 저장)
  4. 해당 세션의 OrderItem 삭제 (CASCADE)
  5. 해당 세션의 Order 삭제
  6. TableSession.is_active = false, ended_at = NOW()
  7. 위 과정을 하나의 트랜잭션으로 처리
```

### 4.2 order_data_json 구조

```json
{
  "orders": [
    {
      "orderNumber": 1,
      "status": "COMPLETED",
      "totalAmount": 21000,
      "createdAt": "2026-03-05T12:30:00",
      "items": [
        {
          "menuName": "불고기 정식",
          "quantity": 1,
          "unitPrice": 12000
        },
        {
          "menuName": "김치찌개",
          "quantity": 1,
          "unitPrice": 9000
        }
      ]
    }
  ],
  "sessionTotalAmount": 21000,
  "orderCount": 1
}
```
