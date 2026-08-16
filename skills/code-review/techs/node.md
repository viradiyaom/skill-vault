# Node.js Rules

## Framework Detection Signals
`package.json` with `express`, `fastify`, `koa`, or plain `node` project. No Next/Nest/React markers.

---

## Folder Structure

### ✅ Good (Express / Fastify)
```
src/
  index.ts               ← server bootstrap only
  app.ts                 ← app setup, middleware
  routes/
    index.ts             ← router aggregator
    user.routes.ts       ← route definitions per domain
  controllers/
    user.controller.ts   ← request/response handling
  services/
    user.service.ts      ← business logic
  middlewares/
    auth.middleware.ts
    error.middleware.ts
  models/                ← DB models / schemas
    user.model.ts
  utils/                 ← pure helpers
  config/
    index.ts             ← env vars, config object
  types/                 ← shared interfaces
```

### ❌ Bad
```
src/index.js             ← everything in one file
routes/userStuff.js      ← vague naming
controllers/all.js       ← mixed controllers
src/db.js               ← raw DB access spread everywhere
```

---

## Coding Patterns

### Environment Config
```ts
// ✅ Centralize all env access in config/
// config/index.ts
export const config = {
  port: process.env.PORT ?? 3000,
  jwtSecret: process.env.JWT_SECRET!,
  dbUri: process.env.MONGODB_URI!,
};

// ✅ Validate at startup — fail fast
if (!process.env.JWT_SECRET) throw new Error('JWT_SECRET is required');

// ❌ process.env scattered throughout codebase
const secret = process.env.JWT_SECRET;  // inside service/route file
```

### Async Error Handling
```ts
// ✅ async/await with try/catch in controllers
async function getUser(req: Request, res: Response, next: NextFunction) {
  try {
    const user = await userService.findById(req.params.id);
    res.json(user);
  } catch (err) {
    next(err);
  }
}

// ✅ Or use an asyncWrapper utility
const asyncHandler = (fn: Function) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);

// ❌ Unhandled promise — crashes server
app.get('/user/:id', async (req, res) => {
  const user = await userService.findById(req.params.id);  // no try/catch!
  res.json(user);
});

// ❌ Swallowed error
try {
  await doSomething();
} catch (e) {}  // silent failure
```

### Error Middleware
```ts
// ✅ Centralized error handler as last middleware
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.error(err);
  res.status(500).json({ message: err.message });
});

// ❌ res.send/json inside every catch block — inconsistent shape
```

### Input Validation
```ts
// ✅ Validate before processing (zod, joi, express-validator)
import { z } from 'zod';
const schema = z.object({ email: z.string().email(), name: z.string().min(2) });
const body = schema.parse(req.body);

// ❌ Using req.body directly without validation
const { email, name } = req.body;
await createUser(email, name);  // no validation = injection risk
```

### Database
```ts
// ✅ Use parameterized queries
const user = await db.query('SELECT * FROM users WHERE id = $1', [userId]);

// ❌ String concatenation in queries
const user = await db.query(`SELECT * FROM users WHERE id = '${userId}'`);

// ✅ Connection pooling — reuse pool, never create per-request
const pool = new Pool({ connectionString: config.dbUri });
// Use pool throughout, not new Client() per request

// ❌ New connection per request
app.get('/users', async (req, res) => {
  const client = new Client();
  await client.connect();  // slow + leaks if not closed
  ...
});
```

### Security
```ts
// ✅ Helmet for security headers
import helmet from 'helmet';
app.use(helmet());

// ✅ Rate limiting
import rateLimit from 'express-rate-limit';
app.use(rateLimit({ windowMs: 15 * 60 * 1000, max: 100 }));

// ✅ CORS — explicit origins, not wildcard in prod
app.use(cors({ origin: process.env.ALLOWED_ORIGIN }));

// ❌ cors() with no config in production
app.use(cors());  // allows all origins
```

---

## Variable Declaration Rules

| Rule | ✅ Do | ❌ Don't |
|------|-------|---------|
| `const` always | `const router = express.Router()` | `let router = ...` |
| No `var` | — | `var app = express()` |
| Config via object | `config.jwtSecret` | `process.env.JWT_SECRET` inline |
| Async functions | `async function getUser()` | callbacks in new code |
| Typed params | `(req: Request, res: Response)` | `(req, res)` untyped |
| Descriptive names | `userId`, `userEmail` | `id`, `e`, `d` |

---

## Things to Flag

| Issue | Severity |
|-------|----------|
| SQL string concatenation with user input | 🔴 CRITICAL |
| `cors()` with no config (open CORS) in prod file | 🔴 CRITICAL |
| Hardcoded credentials / API keys | 🔴 CRITICAL |
| Unhandled promise rejection in route handler | 🔴 CRITICAL |
| `process.env` access without fallback/validation | 🟡 MAJOR |
| No input validation on `req.body` | 🟡 MAJOR |
| New DB connection per request | 🟡 MAJOR |
| `catch(e) {}` — swallowed error | 🟡 MAJOR |
| Missing error middleware | 🟡 MAJOR |
| Business logic inside route file | 🟢 MINOR |
| Missing `helmet()` | 🟢 MINOR |
| `console.log` left in production code | 🟢 MINOR |
| `any` type on req.body | 🟢 MINOR |
| `var` declaration | 🟢 MINOR |
