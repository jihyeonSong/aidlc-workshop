# Database - 비즈니스 규칙

## 1. 데이터 검증 규칙

### Store
| 필드 | 규칙 |
|---|---|
| name | 필수, 1~100자 |

### Admin
| 필드 | 규칙 |
|---|---|
| username | 필수, 1~50자, 매장 내 고유 |
| password_hash | 필수, bcrypt 해싱 |
| login_attempts | 5회 초과 시 계정 잠금 (30분) |

### StoreTable
| 필드 | 규칙 |
|---|---|
| table_number | 필수, 양의 정수, 매장 내 고유 |
| password_hash | 필수, bcrypt 해싱 |

### Category
| 필드 | 규칙 |
|---|---|
| name | 필수, 1~50자 |
| display_order | 0 이상 정수 |

### MenuItem
| 필드 | 규칙 |
|---|---|
| name | 필수, 1~100자 |
| price | 필수, 0 ~ 10,000,000 (원) |
| category_id | 필수, 유효한 카테고리 |
| description | 선택, 최대 1000자 |
| image_url | 선택, 최대 500자, URL 형식 |
| display_order | 0 이상 정수 |

### Order
| 필드 | 규칙 |
|---|---|
| order_number | 필수, 매장별 매일 자동 생성 (001부터 시작) |
| status | 필수, PENDING/PREPARING/COMPLETED 중 하나 |
| total_amount | 필수, 0 이상, OrderItem 합계와 일치 |

### OrderItem
| 필드 | 규칙 |
|---|---|
| menu_name | 필수, 주문 시점 메뉴명 스냅샷 |
| quantity | 필수, 1 이상 정수 |
| unit_price | 필수, 0 이상, 주문 시점 가격 스냅샷 |

---

## 2. 참조 무결성 규칙

| 부모 | 자식 | 삭제 시 동작 |
|---|---|---|
| Store | Admin, StoreTable, Category, MenuItem, Order, OrderHistory | RESTRICT (매장 삭제 불가, 하위 데이터 존재 시) |
| StoreTable | TableSession, Order, OrderHistory | RESTRICT |
| TableSession | Order | RESTRICT (활성 세션 삭제 불가) |
| Category | MenuItem | RESTRICT (메뉴가 있는 카테고리 삭제 불가) |
| MenuItem | OrderItem | SET NULL (메뉴 삭제 시 OrderItem.menu_item_id = NULL) |
| Order | OrderItem | CASCADE (주문 삭제 시 항목도 삭제) |

---

## 3. 주문 상태 전이 규칙

```
PENDING → PREPARING → COMPLETED
```

| 현재 상태 | 허용 전이 | 설명 |
|---|---|---|
| PENDING | PREPARING | 주문 접수 → 준비 시작 |
| PREPARING | COMPLETED | 준비 완료 |
| PENDING | (삭제) | 관리자 직권 삭제 가능 |
| PREPARING | (삭제) | 관리자 직권 삭제 가능 |
| COMPLETED | (삭제) | 관리자 직권 삭제 가능 |

- 역방향 전이 불가 (COMPLETED → PREPARING 불가)
- 삭제는 모든 상태에서 관리자만 가능

---

## 4. 주문번호 생성 규칙

- 매장별(store_id) + 일별(DATE(created_at)) 기준으로 1부터 시작
- 같은 매장의 같은 날 주문은 순차 증가 (1, 2, 3, ...)
- 다음 날이 되면 1부터 리셋
- 동시성 제어: SELECT MAX(order_number) + 1 with row-level lock 또는 시퀀스 활용

---

## 5. 테이블 세션 규칙

| 규칙 | 설명 |
|---|---|
| 세션 시작 | 해당 테이블의 첫 주문 생성 시 자동 시작 (활성 세션이 없을 때) |
| 세션 종료 | 관리자가 "이용 완료" 처리 시 (is_active = false, ended_at 기록) |
| 활성 세션 제한 | 테이블당 활성 세션은 최대 1개 |
| 세션 종료 시 | 현재 세션의 주문을 OrderHistory로 이동, Order/OrderItem 삭제 |

---

## 6. 멀티테넌트 데이터 격리 규칙

- 모든 쿼리에 store_id 조건 필수
- API 레벨에서 JWT 토큰의 store_id와 요청 경로의 storeId 일치 검증
- 크로스 매장 데이터 접근 불가
