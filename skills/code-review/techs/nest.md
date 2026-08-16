# NestJS Rules

## Framework Detection Signals
`nest-cli.json` exists, or `@nestjs/core` in `package.json`, `src/main.ts` with `NestFactory`.

---

## Folder Structure

### ✅ Good
```
src/
  main.ts                    ← bootstrap only
  app.module.ts              ← root module
  common/
    decorators/              ← custom decorators
    filters/                 ← exception filters
    guards/                  ← auth guards
    interceptors/            ← logging, transform
    pipes/                   ← validation pipes
    dto/                     ← shared DTOs
  [feature]/                 ← one folder per domain
    feature.module.ts
    feature.controller.ts
    feature.service.ts
    feature.repository.ts    ← (optional, if not using ORM directly)
    dto/
      create-feature.dto.ts
      update-feature.dto.ts
    entities/
      feature.entity.ts
test/
  [feature]/
    feature.e2e.spec.ts
```

### ❌ Bad
```
src/controllers/user.ts      ← not colocated with feature
src/services/                ← flat services folder
src/UserController.ts        ← not in a module folder
src/feature/feature.ts       ← ambiguous filename
```

---

## Coding Patterns

### Controllers
```ts
// ✅ Thin controller — only routing, delegation, response shape
@Controller('users')
@UseGuards(JwtAuthGuard)
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Get(':id')
  async findOne(@Param('id') id: string) {
    return this.usersService.findOne(id);
  }
}

// ❌ Business logic in controller
@Get(':id')
async findOne(@Param('id') id: string) {
  const user = await this.db.findOne(id);
  if (!user) throw new NotFoundException();
  user.lastAccessed = new Date();
  await this.db.save(user);
  return user;
}
```

### Services
```ts
// ✅ Services own business logic, throw domain exceptions
async findOne(id: string): Promise<User> {
  const user = await this.usersRepository.findById(id);
  if (!user) throw new NotFoundException(`User ${id} not found`);
  return user;
}

// ❌ Returning raw DB errors to caller
async findOne(id: string) {
  return this.db.query(`SELECT * FROM users WHERE id = '${id}'`);  // injection!
}
```

### DTOs & Validation
```ts
// ✅ Always use class-validator on DTOs
import { IsString, IsEmail, MinLength } from 'class-validator';

export class CreateUserDto {
  @IsString()
  @MinLength(2)
  name: string;

  @IsEmail()
  email: string;
}

// ❌ No validation — raw body accepted
async create(@Body() body: any) { ... }

// ✅ Use ValidationPipe globally in main.ts
app.useGlobalPipes(new ValidationPipe({ whitelist: true, forbidNonWhitelisted: true }));
```

### Guards & Auth
```ts
// ✅ Use guards for auth, not middleware or manual checks in controllers
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles('admin')
@Get('admin-only')
adminRoute() { ... }

// ❌ Manual JWT verify in controller
@Get('admin-only')
async adminRoute(@Headers('authorization') token: string) {
  const decoded = jwt.verify(token, secret);  // manual = error-prone
}
```

### Exception Handling
```ts
// ✅ Use built-in Nest exceptions
throw new NotFoundException('User not found');
throw new BadRequestException('Invalid input');
throw new UnauthorizedException();

// ✅ Custom exception filter for consistent error shape
@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter { ... }

// ❌ Returning error manually from controller
return { success: false, error: 'not found' };  // inconsistent shape
```

### Modules
```ts
// ✅ Feature modules are self-contained
@Module({
  imports: [TypeOrmModule.forFeature([User])],
  controllers: [UsersController],
  providers: [UsersService],
  exports: [UsersService],  // only export what other modules need
})
export class UsersModule {}

// ❌ Everything in AppModule — no feature modules
```

### Payments & Webhooks
```ts
// ✅ Verify webhook signature using RAW body before processing
@Post('stripe-webhook')
async handleWebhook(
  @Headers('stripe-signature') signature: string,
  @Req() req: RawBodyRequest<Request>, // Requires rawBody: true in NestFactory options
) {
  const event = this.stripeService.constructEvent(req.rawBody, signature);
  return this.paymentsService.processWebhook(event);
}

// ❌ Trusting raw JSON body without signature verification or rawBody
@Post('stripe-webhook')
async handleWebhook(@Body() body: any) {
  const event = body; // CRITICAL: Attacker can send arbitrary JSON payload
  return this.paymentsService.processWebhook(event);
}
```

```ts
// ✅ Use Idempotency Keys and Database Transactions for critical operations
async createCharge(userId: string, amount: number, idempotencyKey: string): Promise<Payment> {
  return this.dataSource.transaction(async (entityManager) => {
    // 1. Check idempotency
    const existing = await entityManager.findOne(Payment, { where: { idempotencyKey } });
    if (existing) return existing;

    // 2. Perform payment call to external provider
    const charge = await this.stripeService.charge(amount, idempotencyKey);

    // 3. Save atomically
    const payment = entityManager.create(Payment, { userId, amount, chargeId: charge.id, idempotencyKey });
    return entityManager.save(payment);
  });
}
```

---

## Variable Declaration Rules

| Rule | ✅ Do | ❌ Don't |
|------|-------|---------|
| `readonly` injected deps | `constructor(private readonly svc: UsersService)` | `private svc: UsersService` |
| Typed return on service methods | `async findAll(): Promise<User[]>` | `async findAll()` |
| `const` for config values | `const MAX_LOGIN_ATTEMPTS = 5` | magic numbers inline |
| No `any` | `@Body() dto: CreateUserDto` | `@Body() body: any` |
| Enum for status values | `enum UserStatus { ACTIVE, INACTIVE }` | string literals scattered |

---

## Things to Flag

| Issue | Severity |
|-------|----------|
| SQL string concatenation with user input | 🔴 CRITICAL |
| No `ValidationPipe` on endpoints accepting user data | 🔴 CRITICAL |
| Auth bypass — missing guard on sensitive route | 🔴 CRITICAL |
| Hardcoded secrets (JWT secret, DB password, Payment API keys) | 🔴 CRITICAL |
| Webhook endpoint missing signature verification or using parsed `@Body` | 🔴 CRITICAL |
| Business logic in controller | 🟡 MAJOR |
| `@Body() body: any` — no DTO validation | 🟡 MAJOR |
| Throwing raw Error instead of Nest HttpException | 🟡 MAJOR |
| Missing `exports` in module for shared service | 🟡 MAJOR |
| `async` function with no `await` | 🟡 MAJOR |
| No error handling on async DB calls | 🟡 MAJOR |
| Critical workflow (e.g. payment) missing idempotency key check | 🟡 MAJOR |
| Financial or multi-step operations missing database transactions | 🟡 MAJOR |
| DTO missing `class-validator` decorators | 🟢 MINOR |
| Controller doing more than routing | 🟢 MINOR |
| Service directly accessing request/response objects | 🟢 MINOR |
| Missing `readonly` on injected dependencies | 🟢 MINOR |
