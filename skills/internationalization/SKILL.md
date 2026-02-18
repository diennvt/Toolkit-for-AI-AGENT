---
name: Internationalization
description: i18n/l10n - multi-language support, translation management, locale formatting
---

# Internationalization (Đa ngôn ngữ - i18n)

## Tổng quan

Skill kit này hướng dẫn implement multi-language support: i18n setup, translation management, locale formatting, và best practices.

---

## Checklist

- [ ] Chọn i18n library (react-i18next / vue-i18n / next-intl)
- [ ] Thiết kế translation file structure
- [ ] Tạo translation files cho default language
- [ ] Implement language switcher
- [ ] Format dates, numbers, currency theo locale
- [ ] Handle RTL languages (nếu cần)
- [ ] SEO cho multi-language (hreflang, URL structure)

---

## Hướng dẫn chi tiết

### Chọn Library

| Library | Framework | Features |
|---------|-----------|----------|
| **react-i18next** | React | Phổ biến nhất, flexible |
| **next-intl** | Next.js | Built for Next.js, SSR support |
| **vue-i18n** | Vue | Official Vue i18n |
| **FormatJS** | Any | ICU MessageFormat |

### React-i18next Setup

```typescript
// i18n/config.ts
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';
import en from './locales/en.json';
import vi from './locales/vi.json';

i18n.use(initReactI18next).init({
  resources: {
    en: { translation: en },
    vi: { translation: vi },
  },
  lng: localStorage.getItem('language') || 'vi',
  fallbackLng: 'en',
  interpolation: { escapeValue: false },
});

export default i18n;
```

### Translation Files

```json
// i18n/locales/en.json
{
  "common": {
    "save": "Save",
    "cancel": "Cancel",
    "delete": "Delete",
    "search": "Search...",
    "loading": "Loading...",
    "noData": "No data found"
  },
  "auth": {
    "login": "Login",
    "register": "Register",
    "email": "Email",
    "password": "Password",
    "forgotPassword": "Forgot password?",
    "loginSuccess": "Login successful",
    "loginError": "Invalid email or password"
  },
  "user": {
    "profile": "Profile",
    "settings": "Settings",
    "greeting": "Hello, {{name}}!",
    "itemCount_one": "{{count}} item",
    "itemCount_other": "{{count}} items"
  },
  "validation": {
    "required": "{{field}} is required",
    "email": "Invalid email address",
    "minLength": "{{field}} must be at least {{min}} characters"
  }
}
```

```json
// i18n/locales/vi.json
{
  "common": {
    "save": "Lưu",
    "cancel": "Hủy",
    "delete": "Xóa",
    "search": "Tìm kiếm...",
    "loading": "Đang tải...",
    "noData": "Không có dữ liệu"
  },
  "auth": {
    "login": "Đăng nhập",
    "register": "Đăng ký",
    "email": "Email",
    "password": "Mật khẩu",
    "forgotPassword": "Quên mật khẩu?",
    "loginSuccess": "Đăng nhập thành công",
    "loginError": "Email hoặc mật khẩu không đúng"
  },
  "user": {
    "profile": "Hồ sơ",
    "settings": "Cài đặt",
    "greeting": "Xin chào, {{name}}!",
    "itemCount": "{{count}} mục"
  },
  "validation": {
    "required": "{{field}} là bắt buộc",
    "email": "Email không hợp lệ",
    "minLength": "{{field}} phải có ít nhất {{min}} ký tự"
  }
}
```

### Sử dụng trong Components

```tsx
import { useTranslation } from 'react-i18next';

function LoginPage() {
  const { t } = useTranslation();

  return (
    <form>
      <h1>{t('auth.login')}</h1>
      <input placeholder={t('auth.email')} />
      <input placeholder={t('auth.password')} type="password" />
      <button type="submit">{t('auth.login')}</button>
      <a href="/forgot">{t('auth.forgotPassword')}</a>
    </form>
  );
}

// Dynamic values
<p>{t('user.greeting', { name: user.name })}</p>
// → "Hello, John!" hoặc "Xin chào, John!"

// Plurals
<p>{t('user.itemCount', { count: items.length })}</p>
// → "5 items" hoặc "5 mục"
```

### Language Switcher

```tsx
function LanguageSwitcher() {
  const { i18n } = useTranslation();

  const changeLanguage = (lng: string) => {
    i18n.changeLanguage(lng);
    localStorage.setItem('language', lng);
    document.documentElement.lang = lng;
  };

  return (
    <select value={i18n.language} onChange={(e) => changeLanguage(e.target.value)}>
      <option value="vi">🇻🇳 Tiếng Việt</option>
      <option value="en">🇺🇸 English</option>
      <option value="ja">🇯🇵 日本語</option>
    </select>
  );
}
```

### Locale-aware Formatting

```typescript
// Format theo locale — KHÔNG hard-code format
const formatDate = (date: Date, locale: string) => {
  return new Intl.DateTimeFormat(locale, {
    year: 'numeric', month: 'long', day: 'numeric'
  }).format(date);
};
// vi: "18 tháng 2, 2026"
// en: "February 18, 2026"

const formatCurrency = (amount: number, locale: string, currency: string) => {
  return new Intl.NumberFormat(locale, {
    style: 'currency', currency
  }).format(amount);
};
// vi, VND: "1.000.000 ₫"
// en, USD: "$1,000,000.00"

const formatNumber = (num: number, locale: string) => {
  return new Intl.NumberFormat(locale).format(num);
};
// vi: "1.234.567"
// en: "1,234,567"
```

### Backend i18n (API responses)

```typescript
// Detect language from Accept-Language header
const getLocale = (req) => {
  return req.headers['accept-language']?.split(',')[0] || 'en';
};

// Return translated error messages
const errorMessages = {
  en: { NOT_FOUND: 'Resource not found', UNAUTHORIZED: 'Unauthorized' },
  vi: { NOT_FOUND: 'Không tìm thấy', UNAUTHORIZED: 'Chưa xác thực' },
};
```

---

## File Structure

```
src/
├── i18n/
│   ├── config.ts           # i18n initialization
│   ├── locales/
│   │   ├── en.json          # English
│   │   ├── vi.json          # Vietnamese
│   │   └── ja.json          # Japanese
│   └── useLocaleFormat.ts   # Custom formatting hooks
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Hard-code text trong components | Dùng translation keys |
| Hard-code date/number format | Dùng `Intl` API theo locale |
| Chỉ dịch UI, quên error messages | Dịch cả API error messages |
| Nested keys quá sâu | Max 2 levels (`auth.login`) |
| Không có fallback language | Set fallbackLng = 'en' |
| Dịch từng word riêng lẻ | Dịch full sentence (context quan trọng) |

---

## Liên kết

- **Skill liên quan:** [frontend-development](../frontend-development/SKILL.md), [api-design](../api-design/SKILL.md)
