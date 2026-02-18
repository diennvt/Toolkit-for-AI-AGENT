---
name: Frontend Development
description: Frontend - component architecture, state management, responsive design, accessibility
---

# Frontend Development

## Tổng quan

Skill kit này hướng dẫn phát triển frontend hiện đại: component architecture, responsive design, accessibility, và best practices.

---

## Checklist

- [ ] Chọn framework (React, Vue, Angular, Svelte)
- [ ] Thiết kế component hierarchy
- [ ] Setup routing
- [ ] Thiết kế layout system (Header, Sidebar, Footer)
- [ ] Tạo shared UI components (Button, Input, Modal, Toast...)
- [ ] Kết nối API (HTTP client setup)
- [ ] Xử lý loading/error states
- [ ] Responsive design (mobile-first)
- [ ] Form handling + validation
- [ ] Accessibility (a11y) cơ bản

---

## Hướng dẫn chi tiết

### Component Architecture

```
src/
├── components/
│   ├── ui/                    # Reusable UI components
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   ├── Modal.tsx
│   │   ├── Toast.tsx
│   │   ├── Loading.tsx
│   │   └── DataTable.tsx
│   ├── layout/                # Layout components
│   │   ├── Header.tsx
│   │   ├── Sidebar.tsx
│   │   ├── Footer.tsx
│   │   └── Layout.tsx
│   └── features/              # Feature-specific components
│       ├── auth/
│       │   ├── LoginForm.tsx
│       │   └── RegisterForm.tsx
│       └── products/
│           ├── ProductList.tsx
│           ├── ProductCard.tsx
│           └── ProductForm.tsx
├── pages/                     # Page components (routes)
│   ├── HomePage.tsx
│   ├── LoginPage.tsx
│   └── ProductsPage.tsx
├── hooks/                     # Custom hooks
│   ├── useAuth.ts
│   ├── useFetch.ts
│   └── useForm.ts
├── services/                  # API calls
│   ├── api.ts                 # Axios/fetch config
│   ├── authService.ts
│   └── productService.ts
├── store/                     # State management
├── utils/                     # Helper functions
├── types/                     # TypeScript types
└── styles/                    # Global styles
```

### Component Best Practices

```tsx
// ✅ GOOD Component
interface ProductCardProps {
  product: Product;
  onAddToCart: (productId: string) => void;
}

const ProductCard: React.FC<ProductCardProps> = ({ product, onAddToCart }) => {
  return (
    <div className="product-card">
      <img src={product.imageUrl} alt={product.name} />
      <h3>{product.name}</h3>
      <p className="price">${product.price.toFixed(2)}</p>
      <button onClick={() => onAddToCart(product.id)}>
        Add to Cart
      </button>
    </div>
  );
};

// ❌ BAD Component — quá nhiều logic, quá dài
const ProductCard = ({ product }) => {
  const [cart, setCart] = useState([]);
  const [user, setUser] = useState(null);
  // ❌ Fetch data trong component hiển thị
  useEffect(() => { fetchUser()... }, []);
  // ❌ Business logic trong UI component
  const calculateDiscount = () => { ... };
  // ❌ 200+ lines of mixed concerns
};
```

### API Service Pattern

```typescript
// services/api.ts
import axios from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL || 'http://localhost:3000/api/v1',
  headers: { 'Content-Type': 'application/json' },
});

// Request interceptor — auto attach token
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});

// Response interceptor — handle errors globally
api.interceptors.response.use(
  (response) => response.data,
  (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error.response?.data || error);
  }
);

export default api;
```

### Custom Hook Pattern

```typescript
// hooks/useFetch.ts
function useFetch<T>(url: string) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await api.get(url);
        setData(response.data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    fetchData();
  }, [url]);

  return { data, loading, error };
}
```

### Responsive Design

```css
/* Mobile first approach */
.container {
  padding: 1rem;
  width: 100%;
}

/* Tablet */
@media (min-width: 768px) {
  .container {
    padding: 2rem;
    max-width: 768px;
    margin: 0 auto;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .container {
    max-width: 1200px;
  }
}

/* Breakpoints reference:
   Mobile:  < 768px
   Tablet:  768px - 1023px
   Desktop: 1024px - 1279px
   Large:   >= 1280px
*/
```

### Loading & Error States

```tsx
// Mỗi page/component gọi API cần xử lý 3 states:
function ProductsPage() {
  const { data: products, loading, error } = useFetch<Product[]>('/products');

  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage message={error} />;
  if (!products?.length) return <EmptyState message="No products found" />;

  return <ProductList products={products} />;
}
```

---

## ♿ Accessibility (a11y) — Không được bỏ qua

### Checklist a11y tối thiểu

```
□ Semantic HTML (header, nav, main, footer, section, article)
□ Alt text cho mọi image
□ Contrast ratio ≥ 4.5:1 (text), ≥ 3:1 (large text)
□ Keyboard navigation (Tab, Enter, Escape)
□ Focus visible (không dùng outline: none)
□ ARIA labels cho interactive elements
□ Skip to main content link
□ Form labels liên kết đúng (htmlFor / label wrapping)
```

### Semantic HTML

```html
<!-- ✅ GOOD — Semantic -->
<header>
  <nav aria-label="Main navigation">
    <ul>
      <li><a href="/home">Home</a></li>
      <li><a href="/products">Products</a></li>
    </ul>
  </nav>
</header>
<main>
  <h1>Products</h1>
  <section aria-label="Product list">...</section>
</main>
<footer>...</footer>

<!-- ❌ BAD — div soup -->
<div class="header">
  <div class="nav">
    <div class="link">Home</div>
  </div>
</div>
```

### ARIA Labels

```tsx
// Buttons without visible text
<button aria-label="Close dialog" onClick={onClose}>✕</button>
<button aria-label="Add to cart" onClick={onAdd}>🛒</button>

// Loading states
<div aria-busy={loading} aria-live="polite">
  {loading ? <Spinner /> : <ProductList />}
</div>

// Forms
<label htmlFor="email">Email</label>
<input id="email" type="email" aria-required="true" aria-invalid={!!errors.email} />
{errors.email && <span role="alert">{errors.email}</span>}
```

### Keyboard Navigation

```css
/* ✅ GOOD — Custom focus style */
:focus-visible {
  outline: 2px solid #4A90D9;
  outline-offset: 2px;
}

/* ❌ BAD — Removing focus indicator */
:focus { outline: none; }
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Giant component (>200 lines) | Chia nhỏ, mỗi component 1 việc |
| Fetch data trong UI component | Dùng hooks hoặc service layer |
| Không xử lý loading/error | Luôn có 3 states: loading, error, data |
| CSS inline everywhere | Dùng CSS modules / styled-components |
| Không responsive | Mobile-first approach |
| Prop drilling 5+ levels | Dùng Context / state management |
| `<div>` cho mọi thứ | Semantic HTML (nav, main, section...) |
| `outline: none` cho focus | Custom `:focus-visible` style |
| Thiếu alt text cho images | Mọi `<img>` cần `alt` attribute |

---

## Liên kết

- **Process liên quan:** [05-Implementation](../../processes/05-implementation/SKILL.md)
- **Skill liên quan:** [state-management](../state-management/SKILL.md), [api-design](../api-design/SKILL.md), [internationalization](../internationalization/SKILL.md)

