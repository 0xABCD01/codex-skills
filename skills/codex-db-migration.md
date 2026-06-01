# codex-db-migration

Generate a database migration file from a schema change. Detect the ORM. Write the migration and its rollback.

## Prompt

```
Create a database migration for the change described below. Follow these steps:

STEP 1 — DETECT ORM
Check for these files:
- alembic.ini or migrations/env.py → Alembic (SQLAlchemy)
- manage.py with Django imports → Django ORM
- prisma/schema.prisma → Prisma
- knexfile.js or knexfile.ts → Knex
- db/migrate/ directory → Rails ActiveRecord

If none found, stop and ask the user which ORM to use.

STEP 2 — FIND EXISTING MIGRATIONS
Read the last 2-3 migration files to learn:
- Naming conventions (timestamp prefix, sequential number, description)
- Import patterns
- How up/down or upgrade/downgrade functions are structured
- Whether raw SQL or ORM methods are used

STEP 3 — WRITE THE MIGRATION
Create the migration file following the existing patterns.

Rules:
- The "up" side applies the change
- The "down" side reverses it completely
- Add columns as nullable first, backfill, then add NOT NULL constraint
- For renames: use rename, not drop+create
- For indexes: name them explicitly (idx_tablename_columnname)
- Include a data migration if existing rows need updating

STEP 4 — VERIFY
Run the migration:
- Alembic: alembic upgrade head
- Django: python manage.py migrate
- Prisma: npx prisma migrate dev
- Knex: npx knex migrate:latest
- Rails: rails db:migrate

If it fails, read the error, fix the migration, and retry.

STEP 5 — VERIFY ROLLBACK
Run the rollback:
- Alembic: alembic downgrade -1
- Django: python manage.py migrate [app] [previous_number]
- Prisma: npx prisma migrate resolve --rolled-back
- Knex: npx knex migrate:rollback
- Rails: rails db:rollback

Confirm the database returns to the previous state.

STEP 6 — COMMIT
Commit the migration file with message: "migration: <description of change>"

Change description: [PASTE SCHEMA CHANGE HERE]
```

## When to Use

- Adding a column, table, or index
- Renaming a column or table
- Changing a column type
- Adding a constraint or foreign key

## When Not to Use

- No ORM in use (write raw SQL manually)
- Schema change is in a new project with no existing migrations

## What Codex Does Wrong Without This

Codex writes a migration that works going up but breaks on rollback. It uses raw SQL when the ORM has methods for the same thing. It adds NOT NULL columns without default values, which fails on tables with existing rows. This skill forces rollback verification and safe column addition.

## Example

```bash
codex exec "
Create a database migration for the change described below. Follow these steps:

STEP 1 — DETECT ORM
Check for: alembic.ini, manage.py, prisma/schema.prisma, knexfile.js, db/migrate/
If none found, stop and ask.

STEP 2 — FIND EXISTING MIGRATIONS
Read the last 2-3 migration files. Learn naming conventions and patterns.

STEP 3 — WRITE THE MIGRATION
Create the migration file following existing patterns.
- Up applies the change
- Down reverses it
- Add columns as nullable first, then constrain
- Name indexes explicitly

STEP 4 — VERIFY
Run the migration using the detected ORM's command.

STEP 5 — VERIFY ROLLBACK
Run the rollback. Confirm database returns to previous state.

STEP 6 — COMMIT
Commit with message: 'migration: <description>'

Change: Add a 'status' column to the 'orders' table with values pending, confirmed, shipped, delivered. Default to pending. Add an index on status.
"
```
