# optional-extras

## Feature exercised

This probe exercises Poetry's optional dependency / extras feature: dependencies declared as
`optional = true` in `[tool.poetry.dependencies]` and grouped into named extras under
`[tool.poetry.extras]`, so that Mend SCA must resolve and surface extras members as
conditional dependencies of the root package.

## Probe metadata

| Field        | Value                          |
|--------------|-------------------------------|
| Pattern      | optional-extras               |
| Target PM    | Poetry                        |
| Python       | ^3.11                         |
| Generated    | 2026-04-21T17:21:35           |

## Extras declared

| Extra name | Members                       |
|------------|-------------------------------|
| `api`      | `fastapi`, `uvicorn`          |
| `ml`       | `scikit-learn`, `pandas`      |

## Expected dependency tree

The Mend scanner should report one root project (`poetry-extras`) with the following
direct dependencies:

### Always-present (non-optional)

- `requests ^2.32` — resolved to `requests 2.33.1`
  - transitive: `certifi`, `charset-normalizer`, `idna`, `urllib3`

### Extra `api` (optional)

- `fastapi ^0.136` — resolved to `fastapi 0.136.0`
  - transitive: `annotated-types`, `anyio`, `pydantic`, `pydantic-core`,
    `starlette`, `typing-extensions`
- `uvicorn ^0.45` — resolved to `uvicorn 0.45.0`
  - transitive: `click`, `h11`

### Extra `ml` (optional)

- `scikit-learn ^1.8` — resolved to `scikit-learn 1.8.0`
  - transitive: `joblib`, `numpy`, `scipy`, `threadpoolctl`
- `pandas ^3.0` — resolved to `pandas 3.0.2`
  - transitive: `numpy`, `python-dateutil`, `pytz`, `tzdata`

All optional dependencies carry `optional = true` in the lockfile entries for the extras
groups. Mend should detect the `[tool.poetry.extras]` section and associate each extras
member with its respective extra name.