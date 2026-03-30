---
name: db-integration-checklist
description: 'Generate checklists for database integration testing. Use when testing database read/write operations, validating data persistence, checking migration scripts, testing transaction behavior, or verifying database constraints and referential integrity.'
---

# Database Integration Checklist

Generate checklists for database integration testing covering persistence, transactions, and data integrity.

## When to Use

- When testing database read/write operations
- When validating data persistence after operations
- When testing database migration scripts
- When verifying transaction behavior and isolation
- When checking referential integrity and constraints

## Procedure

1. Identify database operations under test
2. Generate checklist items for:
   - **CRUD operations**: Create, read, update, delete with validation
   - **Transactions**: Commit, rollback, isolation levels, deadlocks
   - **Constraints**: Foreign keys, unique constraints, check constraints, not null
   - **Migrations**: Schema changes, data migrations, rollback scripts
   - **Performance**: Query performance, index usage, N+1 queries
   - **Concurrency**: Simultaneous writes, optimistic/pessimistic locking
   - **Data types**: Precision, encoding, timezone handling, large objects
   - **Connection management**: Pool exhaustion, connection leaks, timeouts

## Output Format

```markdown
# Database Integration Checklist: [Feature/Entity]

## CRUD Operations
- [ ] Create: valid data persists correctly
- [ ] Create: duplicate key handled
- [ ] Read: returns correct data with filters
- [ ] Update: modifies only intended fields
- [ ] Delete: removes record and handles cascades

## Transaction Behavior
- [ ] Commit persists all changes
- [ ] Rollback reverts all changes
- [ ] Concurrent transactions don't corrupt data

## Constraints and Integrity
- [ ] Foreign key violations rejected
- [ ] Unique constraint violations rejected
- [ ] Not-null constraints enforced

## Migration Testing
- [ ] Forward migration applies cleanly
- [ ] Rollback migration reverts cleanly
- [ ] Data preserved during migration
```
