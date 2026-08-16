# Database Rules & Best Practices

## Framework / Library Detection Signals
Existence of ORMs like `typeorm`, `prisma`, `@prisma/client`, `mongoose`, `sequelize`, `micro-orm`, or database drivers like `pg`, `mysql2`, `mongodb`, `redis`, `sqlite3` in `package.json`.

---

## Coding Patterns

### SQL Injection Prevention
Always use parameterized queries, ORMs, or query builders. Never concatenate user input directly into queries.

```ts
// ✅ Good: Parameterized query / ORM finder
async findByEmail(email: string): Promise<User | null> {
  return this.usersRepository.findOne({ where: { email } });
}

// ✅ Good: Raw query with parameters
async findByEmailRaw(email: string): Promise<User[]> {
  return this.dataSource.query('SELECT * FROM users WHERE email = $1', [email]);
}

// ❌ Bad: SQL string concatenation (SQL Injection vulnerability!)
async findByEmailConcatenated(email: string): Promise<User[]> {
  return this.dataSource.query(`SELECT * FROM users WHERE email = '${email}'`);
}
```

### Transactions (ACID compliance)
Execute multi-table writes, ledger changes, and order creation inside transactions to ensure atomicity.

```ts
// ✅ Good: Atomic transaction block (auto-rollback on exception)
async purchaseProduct(userId: string, productId: string, price: number) {
  return this.dataSource.transaction(async (entityManager) => {
    // 1. Deduct balance
    const user = await entityManager.findOne(User, { where: { id: userId } });
    if (user.balance < price) throw new BadRequestException('Insufficient balance');
    user.balance -= price;
    await entityManager.save(user);

    // 2. Create order
    const order = entityManager.create(Order, { userId, productId, amount: price });
    await entityManager.save(order);
  });
}

// ❌ Bad: Multiple DB calls without a transaction (partial failures lead to corrupt state!)
async purchaseProductUnsafe(userId: string, productId: string, price: number) {
  const user = await this.userRepo.findOne({ where: { id: userId } });
  if (user.balance < price) throw new Error('Insufficient balance');
  
  user.balance -= price;
  await this.userRepo.save(user); // If next save fails, user lost money but got no order!
  
  const order = this.orderRepo.create({ userId, productId, amount: price });
  await this.orderRepo.save(order);
}
```

### Preventing the N+1 Query Problem
Avoid executing database queries inside loops. Use eager loading, relations/joins, or batching.

```ts
// ✅ Good: Joint query or eager loading relations
async getOrdersWithProducts(): Promise<Order[]> {
  return this.orderRepo.find({ relations: ['product'] });
}

// ❌ Bad: Querying database inside a loop (N+1 query overhead)
async getOrdersWithProductsNPlusOne(): Promise<Order[]> {
  const orders = await this.orderRepo.find();
  for (const order of orders) {
    // Executes a separate DB query for every single order!
    order.product = await this.productRepo.findOne({ where: { id: order.productId } });
  }
  return orders;
}
```

### Schema Synchronisation & Migrations
Avoid auto-creating/updating database schemas in production, as it can result in locking tables, schema corruption, or data loss.

```ts
// ✅ Good: Use TypeORM / Prisma migrations ran explicitly via CLI in CI/CD pipeline
// typeorm-cli migration:run

// ❌ Bad: Auto-synchronize enabled in production database configuration
TypeOrmModule.forRoot({
  type: 'postgres',
  synchronize: true, // 🔴 CRITICAL: Never use synchronize: true in production!
  // ...
})
```

---

## Things to Flag

| Issue | Severity |
|-------|----------|
| Raw SQL query string concatenation with user-supplied input | 🔴 CRITICAL |
| Database credentials committed directly in code/config files | 🔴 CRITICAL |
| `synchronize: true` (or auto-schema sync equivalent) enabled in production | 🔴 CRITICAL |
| Multi-step or financial DB operations executed without transactions | 🟡 MAJOR |
| DB query execution inside a loop (N+1 query pattern) | 🟡 MAJOR |
| Missing index on columns frequently used in `WHERE`, `JOIN`, or `ORDER BY` clauses | 🟡 MAJOR |
| Large tables lacking pagination (e.g. `findAll()` returning thousands of rows) | 🟡 MAJOR |
| Raw unparameterized queries | 🟡 MAJOR |
| Database connections not released or properly closed in custom scripts | 🟡 MAJOR |
| Tables missing standard tracking columns (e.g., `createdAt`, `updatedAt`) | 🟢 MINOR |
| Missing foreign key constraints between related tables | 🟢 MINOR |
