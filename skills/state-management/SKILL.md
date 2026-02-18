---
name: State Management
description: State Management - global/local state, Redux/Zustand/Pinia patterns, state persistence
---

# State Management (Quản lý State)

## Tổng quan

Skill kit này hướng dẫn quản lý state trong frontend: khi nào cần state management, chọn library nào, và best practices.

---

## Checklist

- [ ] Xác định loại state (local, shared, server, URL)
- [ ] Chọn state management library phù hợp
- [ ] Thiết kế store structure
- [ ] Tách server state (API data) khỏi client state (UI state)
- [ ] Implement loading/error states
- [ ] State persistence (nếu cần)

---

## Hướng dẫn chi tiết

### Phân loại State

| Loại | Mô tả | Giải pháp |
|------|--------|-----------|
| **Local State** | Chỉ dùng trong 1 component | `useState` / `ref()` |
| **Shared State** | Chia sẻ giữa nhiều components | Context / Zustand / Pinia |
| **Server State** | Data từ API | React Query / SWR |
| **URL State** | Query params, route state | Router / URL search params |
| **Form State** | Form input values | React Hook Form / Formik |

### Khi nào cần State Management Library?

```
✅ CẦN:
- Data chia sẻ giữa nhiều components không liên quan
- Authentication state (user, token)
- Shopping cart
- Theme / language settings

❌ KHÔNG CẦN:
- State chỉ dùng trong 1 component → useState
- Data từ API → React Query / SWR
- Form state → React Hook Form
- Chỉ truyền qua 1-2 levels → props là đủ
```

### Zustand (React — Recommended)

```typescript
import { create } from 'zustand';

interface AuthStore {
  user: User | null;
  token: string | null;
  login: (user: User, token: string) => void;
  logout: () => void;
}

const useAuthStore = create<AuthStore>((set) => ({
  user: null,
  token: null,
  login: (user, token) => set({ user, token }),
  logout: () => set({ user: null, token: null }),
}));

// Usage in components
function Header() {
  const { user, logout } = useAuthStore();
  return user ? <button onClick={logout}>Logout</button> : <Link to="/login">Login</Link>;
}
```

### React Query (Server State)

```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

// GET — fetch data
function useProducts() {
  return useQuery({
    queryKey: ['products'],
    queryFn: () => api.get('/products'),
    staleTime: 5 * 60 * 1000, // 5 minutes
  });
}

// POST — mutation
function useCreateProduct() {
  const queryClient = useQueryClient();
  return useMutation({
    mutationFn: (data) => api.post('/products', data),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['products'] });
    },
  });
}

// Usage
function ProductsPage() {
  const { data, isLoading, error } = useProducts();
  const createProduct = useCreateProduct();

  if (isLoading) return <Loading />;
  if (error) return <Error message={error.message} />;
  return <ProductList products={data} />;
}
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Redux cho mọi thứ | Đúng tool cho đúng loại state |
| API data trong Redux | React Query / SWR cho server state |
| Giant monolithic store | Chia nhỏ stores theo domain |
| Mutate state trực tiếp | Immutable updates |
| Prop drilling 5+ levels | Context hoặc state management |

---

## Liên kết

- **Skill liên quan:** [frontend-development](../frontend-development/SKILL.md)
