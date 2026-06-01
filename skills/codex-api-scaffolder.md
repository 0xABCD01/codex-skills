# codex-api-scaffolder

Build a REST API from a description or OpenAPI spec. Routes, models, validation, error handling, docs.

## Prompt

```
Build a REST API for the description below. Follow these steps:

STEP 1 — DETECT FRAMEWORK
Check project files to pick the right framework:
- If requirements.txt or pyproject.toml has fastapi → FastAPI
- If requirements.txt or pyproject.toml has flask → Flask
- If package.json has express → Express
- If go.mod exists → net/http or chi
- If none of the above → use FastAPI for Python, Express for Node, chi for Go

STEP 2 — DEFINE MODELS
For each resource in the description, create:
- A data model with typed fields
- Input validation (required fields, types, ranges)
- A response model (what the API returns, not the raw DB row)

STEP 3 — BUILD ROUTES
For each resource, implement:
- GET /resources — list with pagination (offset/limit or cursor)
- GET /resources/:id — single item, 404 if missing
- POST /resources — create, validate input, return 201
- PUT /resources/:id — full update, 404 if missing
- PATCH /resources/:id — partial update
- DELETE /resources/:id — delete, return 204

STEP 4 — ERROR HANDLING
Every endpoint must handle:
- Invalid input → 400 with specific field errors
- Not found → 404 with resource name
- Server errors → 500 with request ID for debugging

Use a consistent error response format:
{"error": {"code": "NOT_FOUND", "message": "User not found", "request_id": "..."}}

STEP 5 — DOCS
If using FastAPI: auto-generated at /docs (built in).
For others: add an OpenAPI spec file or route docstrings.

STEP 6 — TESTS
Write one test per endpoint (happy path + error case).
Run them. Fix failures before finishing.

Description: [PASTE DESCRIPTION OR SPEC HERE]
```

## When to Use

- User describes a set of resources and wants an API
- User provides an OpenAPI/Swagger spec
- Starting a new project that needs CRUD endpoints

## When Not to Use

- Existing API that needs new endpoints (use quick-fix or manual)
- GraphQL API (different skill)
- API that needs auth/permissions (add those after scaffolding)

## What Codex Does Wrong Without This

Codex mixes Flask and FastAPI patterns. It skips input validation. It returns raw database rows instead of response models. It uses bare `except` blocks. It forgets pagination and returns 10,000 rows. This skill forces consistent structure.

## Example

```bash
codex exec "
Build a REST API for the description below. Follow these steps:

STEP 1 — DETECT FRAMEWORK
Check project files to pick the right framework:
- If requirements.txt or pyproject.toml has fastapi → FastAPI
- If requirements.txt or pyproject.toml has flask → Flask
- If package.json has express → Express
- If go.mod exists → net/http or chi
- If none of the above → use FastAPI for Python, Express for Node, chi for Go

STEP 2 — DEFINE MODELS
For each resource in the description, create:
- A data model with typed fields
- Input validation (required fields, types, ranges)
- A response model (what the API returns, not the raw DB row)

STEP 3 — BUILD ROUTES
For each resource, implement:
- GET /resources — list with pagination (offset/limit or cursor)
- GET /resources/:id — single item, 404 if missing
- POST /resources — create, validate input, return 201
- PUT /resources/:id — full update, 404 if missing
- PATCH /resources/:id — partial update
- DELETE /resources/:id — delete, return 204

STEP 4 — ERROR HANDLING
Every endpoint must handle:
- Invalid input → 400 with specific field errors
- Not found → 404 with resource name
- Server errors → 500 with request ID for debugging

Use a consistent error response format:
{'error': {'code': 'NOT_FOUND', 'message': 'User not found', 'request_id': '...'}}

STEP 5 — DOCS
If using FastAPI: auto-generated at /docs (built in).
For others: add an OpenAPI spec file or route docstrings.

STEP 6 — TESTS
Write one test per endpoint (happy path + error case).
Run them. Fix failures before finishing.

Description: A task management API with users and tasks. Users have name and email. Tasks have title, description, status (todo/in_progress/done), and assigned user.
"
```
