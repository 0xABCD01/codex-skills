# codex-db-migration

Generate a database migration file from a schema change. Detect the ORM. Write the migration and its rollback.

## Prompt

```
Create a database migration for the change described below. Follow these steps:

STEP 1: DETECT ORM
Check for these files:
- alembic.ini or migrations/env.py → Alembic (SQLAlchemy)
- manage.py with Django imports → Django ORM
- prisma/schema.prisma → Prisma
- knexfile.js or knexfile.ts → Knex
- db/migrate/ directory → Rails ActiveRecord

If none found, stop and ask the user which ORM to use.

STEP 2: FIND EXISTING MIGRATIONS
Read the last 2-3 migration files to learn:
- Naming conventions (timestamp prefix, sequential number, description)
- Import patterns
- How up/down or upgrade/downgrade functions are structured
- Whether the project uses raw SQL or ORM methods

STEP 3: WRITE THE MIGRATION
Create the migration file following the existing patterns.

Rules:
- The "up" side applies the change
- The "down" side reverses it
- Add columns as nullable first, backfill, then add NOT NULL constraint
- For renames: use rename, not drop+create
- For indexes: name them (idx_tablename_columnname)
- Include a data migration if existing rows need updating

STEP 4: VERIFY
Run the migration:
- Alembic: alembic upgrade head
- Django: python manage.py migrate
- Prisma: npx prisma migrate dev
- Knex: npx knex migrate:latest
- Rails: rails db:migrate

If it fails, read the error, fix the migration, and retry.

STEP 5: VERIFY ROLLBACK
Run the rollback:
- Alembic: alembic downgrade -1
- Django: python manage.py migrate [app] [previous_number]
- Prisma: npx prisma migrate resolve --rolled-back
- Knex: npx knex migrate:rollback
- Rails: rails db:rollback

Confirm the database returns to the previous state.

STEP 6: COMMIT
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

The migration runs forward but the rollback breaks. Codex writes raw SQL when the ORM already has a method for the operation. It adds NOT NULL columns to tables with existing rows and no default value, which fails immediately. This skill verifies both upgrade and rollback work, and adds columns as nullable first.

## Example

```bash
codex exec "
Create a database migration for the change described below. Follow these steps:

STEP 1: DETECT ORM
Check for: alembic.ini, manage.py, prisma/schema.prisma, knexfile.js, db/migrate/
If none found, stop and ask.

STEP 2: FIND EXISTING MIGRATIONS
Read the last 2-3 migration files. Learn naming conventions and patterns.

STEP 3: WRITE THE MIGRATION
Create the migration file following existing patterns.
- Up applies the change
- Down reverses it
- Add columns as nullable first, then constrain
- Name indexes

STEP 4: VERIFY
Run the migration using the detected ORM's command.

STEP 5: VERIFY ROLLBACK
Run the rollback. Confirm database returns to previous state.

STEP 6: COMMIT
Commit with message: 'migration: <description>'

Change: Add a 'status' column to the 'orders' table with values pending, confirmed, shipped, delivered. Default to pending. Add an index on status.
"
```
