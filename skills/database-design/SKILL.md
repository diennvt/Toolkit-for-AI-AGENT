---
name: Database Design
description: Thiết kế database - schema, normalization, indexing, migrations, ORM patterns
---

# Database Design (Thiết kế Database)

## Tổng quan

Skill kit này hướng dẫn thiết kế database: từ schema design, normalization, indexing đến migrations và ORM patterns.

---

## Checklist

- [ ] Xác định entities chính từ requirements
- [ ] Thiết kế relationships (1:1, 1:N, N:N)
- [ ] Chuẩn hóa (Normalization) hợp lý
- [ ] Xác định data types phù hợp
- [ ] Thêm indexes cho queries thường dùng
- [ ] Thiết kế migration strategy
- [ ] Xác định seed data
- [ ] Xử lý soft delete (nếu cần)
- [ ] Timestamps (created_at, updated_at) cho mọi bảng

---

## Hướng dẫn chi tiết

### Quy trình thiết kế

```
1. Xác định Entities   →  User, Product, Order...
2. Xác định Attributes →  name, email, price...
3. Xác định Relations  →  User has many Orders
4. Normalize           →  Tách bảng nếu cần
5. Add Constraints     →  NOT NULL, UNIQUE, FK
6. Add Indexes         →  Cho fields thường query
7. Write Migrations    →  Up/Down migrations
```

### Data Types thường dùng

| Loại dữ liệu | PostgreSQL | MySQL | Khi nào dùng |
|--------------|-----------|-------|-------------|
| ID | `UUID` / `SERIAL` | `CHAR(36)` / `INT AUTO_INCREMENT` | Primary key |
| Short text | `VARCHAR(255)` | `VARCHAR(255)` | Name, title, email |
| Long text | `TEXT` | `TEXT` / `LONGTEXT` | Description, content |
| Integer | `INTEGER` | `INT` | Count, quantity |
| Decimal | `DECIMAL(10,2)` | `DECIMAL(10,2)` | Price, money |
| Boolean | `BOOLEAN` | `TINYINT(1)` | Flags (is_active) |
| Date | `DATE` | `DATE` | Birthday |
| DateTime | `TIMESTAMP` | `DATETIME` | Created at, updated at |
| JSON | `JSONB` | `JSON` | Flexible metadata |
| Enum | `VARCHAR` + CHECK | `ENUM(...)` | Status, type |

### Relationships

```sql
-- 1:1 (One-to-One)
-- User ← → Profile
CREATE TABLE profiles (
    id UUID PRIMARY KEY,
    user_id UUID UNIQUE REFERENCES users(id),  -- UNIQUE = 1:1
    bio TEXT,
    avatar_url VARCHAR(500)
);

-- 1:N (One-to-Many)
-- User ← → Orders
CREATE TABLE orders (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),  -- FK without UNIQUE = 1:N
    total DECIMAL(10,2),
    status VARCHAR(50)
);

-- N:N (Many-to-Many)
-- Products ← → Categories (qua junction table)
CREATE TABLE product_categories (
    product_id UUID REFERENCES products(id) ON DELETE CASCADE,
    category_id UUID REFERENCES categories(id) ON DELETE CASCADE,
    PRIMARY KEY (product_id, category_id)
);
```

### Template Schema chuẩn

```sql
-- Template cho mỗi bảng
CREATE TABLE [table_name] (
    -- Primary Key
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Core Fields
    [field_name] [TYPE] [CONSTRAINTS],
    
    -- Status / Type (nếu cần)
    status VARCHAR(50) DEFAULT 'active'
        CHECK (status IN ('active', 'inactive', 'deleted')),
    
    -- Foreign Keys
    [related]_id UUID REFERENCES [related_table](id) ON DELETE CASCADE,
    
    -- Soft Delete (nếu cần)
    deleted_at TIMESTAMP NULL,
    
    -- Timestamps (BẮT BUỘC)
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_[table]_[field] ON [table_name]([field]);
CREATE INDEX idx_[table]_created ON [table_name](created_at);
```

### Indexing Strategy

```sql
-- Khi nào cần index:
-- 1. Fields dùng trong WHERE
CREATE INDEX idx_users_email ON users(email);

-- 2. Fields dùng trong JOIN (Foreign Keys)
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- 3. Fields dùng trong ORDER BY
CREATE INDEX idx_products_price ON products(price);

-- 4. Composite index cho query phức tạp
CREATE INDEX idx_orders_user_status ON orders(user_id, status);

-- 5. Partial index cho queries cụ thể
CREATE INDEX idx_users_active ON users(email) WHERE is_active = true;

-- ❌ KHÔNG nên index:
-- - Bảng nhỏ (< 1000 rows)
-- - Columns ít distinct values (boolean)
-- - Columns thường xuyên UPDATE
```

### Migration Template

```javascript
// migration: create_users_table.js
exports.up = async (knex) => {
  await knex.schema.createTable('users', (table) => {
    table.uuid('id').primary().defaultTo(knex.raw('gen_random_uuid()'));
    table.string('email', 255).unique().notNullable();
    table.string('password', 255).notNullable();
    table.string('full_name', 255).notNullable();
    table.string('role', 50).defaultTo('user');
    table.boolean('is_active').defaultTo(true);
    table.timestamps(true, true); // created_at, updated_at
  });
};

exports.down = async (knex) => {
  await knex.schema.dropTable('users');
};
```

---

## Python / SQLAlchemy

### Model Template

```python
# models/base.py
from sqlalchemy import Column, DateTime, func
from sqlalchemy.dialects.postgresql import UUID
import uuid

class BaseModel(Base):
    __abstract__ = True
    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, server_default=func.now(), onupdate=func.now())

# models/user.py
from sqlalchemy import Column, String, Boolean
from sqlalchemy.orm import relationship

class User(BaseModel):
    __tablename__ = "users"
    email = Column(String(255), unique=True, nullable=False, index=True)
    password = Column(String(255), nullable=False)
    full_name = Column(String(255), nullable=False)
    role = Column(String(50), default="user")
    is_active = Column(Boolean, default=True)
    orders = relationship("Order", back_populates="user")

# models/order.py
class Order(BaseModel):
    __tablename__ = "orders"
    user_id = Column(UUID(as_uuid=True), ForeignKey("users.id"), nullable=False, index=True)
    total = Column(Numeric(10, 2), nullable=False)
    status = Column(String(50), default="pending")
    user = relationship("User", back_populates="orders")
```

### Alembic Migration

```bash
# Setup
alembic init alembic
alembic revision --autogenerate -m "create users table"
alembic upgrade head
alembic downgrade -1
```

### Pydantic Schemas

```python
# schemas/user.py
from pydantic import BaseModel, EmailStr

class UserCreate(BaseModel):
    email: EmailStr
    password: str
    name: str

class UserResponse(BaseModel):
    id: str
    email: str
    name: str
    role: str

    class Config:
        from_attributes = True  # ORM mode
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Không có foreign keys | Luôn dùng FK để đảm bảo data integrity |
| Thiếu indexes | Index cho WHERE, JOIN, ORDER BY columns |
| Over-normalization | 3NF là đủ cho hầu hết cases |
| Không có timestamps | Luôn có created_at, updated_at |
| Hard delete data | Dùng soft delete cho data quan trọng |
| String cho money | `DECIMAL(10,2)` cho tiền tệ |
| Không có migration rollback | Luôn viết cả `up` và `down` |

---

## Liên kết

- **Process liên quan:** [04-System Design](../../processes/04-system-design/SKILL.md)
- **Skill liên quan:** [backend-development](../backend-development/SKILL.md), [performance-optimization](../performance-optimization/SKILL.md)
