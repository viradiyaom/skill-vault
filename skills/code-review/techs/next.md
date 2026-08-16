# Next.js Rules

## Framework Detection Signals
`next.config.js` or `next.config.mjs` exists, `app/` or `pages/` directory present.

---

## Folder Structure

### ✅ Good
```
app/
  (auth)/
    login/
      page.tsx
      layout.tsx
  dashboard/
    page.tsx
    loading.tsx
    error.tsx
components/
  common/        ← shared, generic components
  [feature]/     ← feature-scoped components
hooks/           ← custom hooks (use*.ts)
store/           ← Redux / Zustand slices
utils/           ← pure helpers, no React
config/          ← API routes, constants
types/           ← shared TypeScript types
```

### ❌ Bad
```
components/Page1Stuff.tsx    ← not feature-grouped
utils/MyComponent.tsx        ← component inside utils
app/page1/components/        ← deeply nested component folders
```

---

## Coding Patterns

### Server vs Client Components
```tsx
// ✅ Default to Server Component — no 'use client' unless needed
export default async function Page() {
  const data = await fetchData();
  return <div>{data.name}</div>;
}

// ✅ Client Component — only when using hooks/events
"use client";
export default function Counter() {
  const [n, setN] = useState(0);
  return <button onClick={() => setN(n + 1)}>{n}</button>;
}

// ❌ Marking everything 'use client' — kills server-side benefits
"use client";
export default async function Page() { ... }  // async + use client = wrong
```

### Data Fetching
```tsx
// ✅ Server Component fetch with caching
const data = await fetch('/api/items', { next: { revalidate: 60 } });

// ✅ Client: use SWR or React Query for client-side fetching
const { data } = useSWR('/api/items', fetcher);

// ❌ useEffect + fetch in a component that could be a Server Component
useEffect(() => { fetch('/api/items').then(...) }, []);
```

### Routing & Navigation
```tsx
// ✅ Use next/navigation in App Router
import { useRouter, usePathname, useSearchParams } from 'next/navigation';

// ❌ Using next/router in App Router
import { useRouter } from 'next/router';  // pages/ only

// ✅ Link for navigation
import Link from 'next/link';
<Link href="/dashboard">Go</Link>

// ❌ <a> tags for internal routes
<a href="/dashboard">Go</a>
```

### Image & Fonts
```tsx
// ✅ Always next/image for images
import Image from 'next/image';
<Image src="/logo.png" width={200} height={50} alt="Logo" />

// ❌ Raw <img> tag
<img src="/logo.png" />

// ✅ next/font for fonts
import { Inter } from 'next/font/google';
const inter = Inter({ subsets: ['latin'] });
```

### API Routes (App Router)
```ts
// ✅ Route Handler with proper types
import { NextRequest, NextResponse } from 'next/server';
export async function GET(req: NextRequest) {
  return NextResponse.json({ data });
}

// ❌ Using req/res from Node (pages/ pattern)
export default function handler(req, res) { ... }  // wrong in app/
```

---

## Variable Declaration Rules

| Rule | ✅ Do | ❌ Don't |
|------|-------|---------|
| Prefer `const` | `const name = 'Om'` | `let name = 'Om'` when never reassigned |
| Avoid `var` | `const/let` always | `var x = 1` |
| Typed state | `useState<User \| null>(null)` | `useState(null)` |
| No `any` | `data: ApiResponse` | `data: any` |
| Constants SCREAMING_SNAKE | `const MAX_RETRIES = 3` | `const maxRetries = 3` for module-level constants |

---

## Things to Flag

| Issue | Severity |
|-------|----------|
| `'use client'` on async Server Component | 🔴 CRITICAL |
| `useRouter` from `next/router` in App Router | 🔴 CRITICAL |
| Hardcoded secrets in `next.config.js` | 🔴 CRITICAL |
| Missing `alt` on `<Image>` | 🟡 MAJOR |
| `<img>` instead of `next/image` | 🟡 MAJOR |
| `<a>` instead of `<Link>` for internal routes | 🟡 MAJOR |
| Missing `loading.tsx` or `error.tsx` for routes with data fetching | 🟡 MAJOR |
| `useEffect` fetch in a component that could be Server Component | 🟡 MAJOR |
| Components in `utils/` | 🟢 MINOR |
| Missing TypeScript types on props | 🟢 MINOR |
| `any` type escape | 🟢 MINOR |
| Raw `<img>` tag | 🟢 MINOR |
