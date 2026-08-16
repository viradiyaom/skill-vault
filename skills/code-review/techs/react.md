# React Rules

## Framework Detection Signals
`react` in `package.json` dependencies, `src/` or `components/` folder, no `next.config.*`.

---

## Folder Structure

### ✅ Good
```
src/
  components/
    common/          ← Button, Input, Modal (reusable)
    [feature]/       ← feature-scoped components
      index.ts       ← barrel export
  pages/             ← route-level components (if using React Router)
  hooks/             ← use*.ts custom hooks
  store/             ← Redux / Zustand / Context
  services/          ← API call functions
  utils/             ← pure helpers, no JSX
  types/             ← shared TypeScript interfaces
  assets/            ← images, fonts, icons
```

### ❌ Bad
```
src/components/page1.js      ← not feature-grouped
src/utils/UserCard.tsx       ← component inside utils
src/MyHook.ts                ← hook not in hooks/
src/components/Big.tsx       ← 500+ line monolith component
```

---

## Coding Patterns

### Component Structure
```tsx
// ✅ Functional component with typed props
interface CardProps {
  title: string;
  onClick: () => void;
}

export function Card({ title, onClick }: CardProps) {
  return <div onClick={onClick}>{title}</div>;
}

// ❌ Default export for everything (hard to refactor)
export default function (props: any) { ... }

// ❌ Class components (avoid in new code)
class MyComponent extends React.Component { ... }
```

### Hooks
```tsx
// ✅ Custom hook for reusable logic
function useUser(id: string) {
  const [user, setUser] = useState<User | null>(null);
  useEffect(() => { fetchUser(id).then(setUser); }, [id]);
  return user;
}

// ❌ Duplicated fetch logic in multiple components
// ❌ Hooks called conditionally
if (isLoggedIn) {
  const data = useData();  // ← breaks Rules of Hooks
}

// ✅ useEffect with correct deps
useEffect(() => {
  fetchData(userId);
}, [userId]);  // ← userId listed

// ❌ Missing dependency
useEffect(() => {
  fetchData(userId);
}, []);  // ← userId missing = stale closure bug
```

### State Management
```tsx
// ✅ Local state for component-only data
const [isOpen, setIsOpen] = useState(false);

// ✅ Derived state — compute, don't duplicate
const fullName = `${firstName} ${lastName}`;  // not a separate state

// ❌ Redundant state
const [fullName, setFullName] = useState('');
useEffect(() => setFullName(`${firstName} ${lastName}`), [firstName, lastName]);

// ✅ Redux for shared server/global state
dispatch(fetchUsers());

// ❌ Prop drilling 3+ levels deep — use context or store
<A user={user}><B user={user}><C user={user} /></B></A>
```

### Event Handlers
```tsx
// ✅ Named handler
const handleSubmit = (e: React.FormEvent) => {
  e.preventDefault();
  // ...
};

// ❌ Inline complex logic in JSX
<form onSubmit={(e) => { e.preventDefault(); validate(); mutate(); setLoading(true); }}>
```

### Lists
```tsx
// ✅ Stable unique key
{users.map(user => <UserCard key={user._id} user={user} />)}

// ❌ Index as key (breaks on reorder/delete)
{users.map((u, i) => <UserCard key={i} user={u} />)}
```

---

## Variable Declaration Rules

| Rule | ✅ Do | ❌ Don't |
|------|-------|---------|
| `const` by default | `const [x, setX] = useState(0)` | `let x = useState(0)` |
| No `var` | — | `var count = 0` |
| Type all state | `useState<string>('')` | `useState('')` when type isn't obvious |
| No `any` | `user: User` | `user: any` |
| Boolean prefix | `isLoading`, `hasError`, `canEdit` | `loading`, `error`, `edit` |
| Handler prefix | `handleClick`, `handleSubmit` | `click`, `submit`, `foo` |

---

## Things to Flag

| Issue | Severity |
|-------|----------|
| Hook called conditionally | 🔴 CRITICAL |
| `useEffect` infinite loop (missing/wrong deps) | 🔴 CRITICAL |
| Direct state mutation `state.items.push(x)` | 🔴 CRITICAL |
| XSS via `dangerouslySetInnerHTML` with user input | 🔴 CRITICAL |
| Missing `key` in list render | 🟡 MAJOR |
| Index used as `key` in dynamic list | 🟡 MAJOR |
| `any` type on props or state | 🟡 MAJOR |
| `useEffect` with missing dependencies | 🟡 MAJOR |
| Prop drilling 3+ levels | 🟡 MAJOR |
| Component over 200 lines (should be split) | 🟢 MINOR |
| Inline arrow function in JSX for complex logic | 🟢 MINOR |
| Boolean state without `is/has/can` prefix | 🟢 MINOR |
| Default export for utility functions | 🟢 MINOR |
