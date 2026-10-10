# Python

> **TL;DR** — Write idiomatic, type-annotated Python checked by mypy or pyright, formatted and linted by Ruff, managed with uv. Model data with `dataclasses` (internal) and Pydantic (boundaries). FastAPI for typed APIs, Django for batteries-included apps. Test with pytest fixtures and parametrization. Know the classic pitfalls: mutable defaults, late-binding closures, blocking calls inside `async`.

Related: [Testing](testing.md) · [Code quality](code-quality-refactoring.md) · [Data & AI architecture](../07-data-ai-architecture/README.md)

---

## 1. Idioms

### PEP 20 — The Zen of Python (key lines, paraphrased)
- Beautiful beats ugly; explicit beats implicit; simple beats complex; complex beats complicated.
- Flat beats nested; sparse beats dense; readability counts.
- Errors should never pass silently, unless explicitly silenced.
- In the face of ambiguity, refuse to guess.
- There should be one obvious way to do it.
- If the implementation is hard to explain, it is a bad idea.

Run `import this` for the original text.

### PEP 8 essentials

| Item | Convention |
|---|---|
| Indentation | 4 spaces |
| Line length | 79 per PEP 8; teams commonly use 88 (Black/Ruff default) |
| Names | `snake_case` functions/variables/modules, `PascalCase` classes, `UPPER_SNAKE` constants, `_private` |
| Imports | stdlib, third-party, local — separated; absolute imports; no wildcard |
| Comparisons | `is None`, not `== None`; `if items:` for emptiness |
| Strings | f-strings |
| Formatting | Automate (Ruff format / Black) — don't debate |

### Idiomatic constructs

```python
# Comprehensions (keep them simple)
squares = [x * x for x in nums if x % 2 == 0]
index = {u.id: u for u in users}

# Unpacking
first, *rest = items
a, b = b, a

# enumerate / zip (strict=True catches length mismatch)
for i, (name, score) in enumerate(zip(names, scores, strict=True), start=1): ...

# Context managers
with open(path, encoding="utf-8") as f: data = f.read()

# EAFP (easier to ask forgiveness) over LBYL
try:
    value = mapping[key]
except KeyError:
    value = default

# Walrus
if (m := pattern.search(line)) is not None: print(m.group(1))

# Structural pattern matching (3.10+)
match event:
    case {"type": "click", "x": int(x), "y": int(y)}: handle_click(x, y)
    case {"type": "key", "key": str(k)}: handle_key(k)
    case _: raise ValueError(f"unknown event {event!r}")

# Generators for streaming
def read_lines(path):
    with open(path) as f:
        yield from (line.rstrip("\n") for line in f)

# pathlib over os.path
from pathlib import Path
config = Path(__file__).parent / "config.toml"
```

Useful stdlib: `collections` (`defaultdict`, `Counter`, `deque`), `itertools` (`batched` 3.12+, `chain`, `groupby`), `functools` (`cache`, `partial`, `singledispatch`), `contextlib`, `enum`, `tomllib`, `logging`, `concurrent.futures`, `datetime` with `zoneinfo`.

---

## 2. Typing

```python
from typing import Protocol, TypedDict, Literal, Final, Self, overload, TypeIs
from collections.abc import Iterable, Callable, Sequence

type UserId = int                         # 3.12 type alias syntax

def first[T](xs: Sequence[T]) -> T | None:    # 3.12 generics syntax (PEP 695)
    return xs[0] if xs else None

class SupportsClose(Protocol):            # structural typing (duck typing, checked)
    def close(self) -> None: ...

class Movie(TypedDict):
    title: str
    year: int

Mode = Literal["r", "w"]
MAX_RETRIES: Final = 3

def is_str_list(val: list[object]) -> TypeIs[list[str]]:   # narrowing (3.13)
    return all(isinstance(x, str) for x in val)
```

| Checker | Notes |
|---|---|
| mypy | Reference implementation, plugin ecosystem (Django, Pydantic), `--strict` |
| pyright / Pylance | Fast, great VS Code integration, strict mode |
| basedpyright | Community fork of pyright with extra checks |
| ty (Astral) / Pyrefly (Meta) | New Rust-based checkers (beta in 2025–26); very fast |

Recommended config:

```toml
[tool.mypy]
strict = true
warn_unreachable = true
disallow_any_explicit = false
plugins = ["pydantic.mypy"]

[tool.pyright]
typeCheckingMode = "strict"
```

Guidelines: annotate public functions; accept abstract types (`Iterable`, `Mapping`) and return concrete ones; use `Protocol` over ABCs for interfaces; avoid `Any`; `from __future__ import annotations` is no longer necessary for most code on 3.14 (deferred evaluation, PEP 649).

---

## 3. Data modelling: dataclasses vs Pydantic vs attrs

| | `dataclasses` | Pydantic v2 | attrs | `TypedDict` / `NamedTuple` |
|---|---|---|---|---|
| Runtime validation | No | Yes (Rust core, fast) | Optional validators | No |
| Coercion/parsing | No | Yes (`"1"` → `1` in lax mode) | Converters | No |
| Serialization | `asdict` | `model_dump`, `model_dump_json`, JSON Schema | `asdict` | dict / tuple |
| Speed | Fastest (plain class) | Fast, but validation cost | Fast | Fast |
| Use | Internal domain/value objects | Trust boundaries: API I/O, config, external data | Advanced class building | Typing dicts from JSON, lightweight records |

```python
from dataclasses import dataclass, field

@dataclass(frozen=True, slots=True, kw_only=True)
class Money:
    amount: int          # minor units
    currency: str

@dataclass
class Cart:
    items: list[str] = field(default_factory=list)   # never items: list = []
```

```python
from pydantic import BaseModel, EmailStr, Field, field_validator
from pydantic_settings import BaseSettings

class UserIn(BaseModel):
    email: EmailStr
    name: str = Field(min_length=1, max_length=100)
    age: int = Field(ge=0)

    @field_validator("name")
    @classmethod
    def strip(cls, v: str) -> str:
        return v.strip()

class Settings(BaseSettings):
    database_url: str
    debug: bool = False
    model_config = {"env_prefix": "APP_"}

settings = Settings()  # reads env, fails fast
```

---

## 4. Environments and packaging

| Tool | Role | Notes |
|---|---|---|
| `venv` | Isolated interpreter env | stdlib; `python -m venv .venv` |
| pip | Installer | `pip install -r requirements.txt` |
| pip-tools | Lock requirements | `pip-compile` |
| **uv** (Astral) | Python installs, venvs, resolver, lockfile, tool runner, build/publish | 10–100× faster; `uv init`, `uv add`, `uv sync`, `uv run`, `uvx`; default choice for new projects |
| Poetry | Dependency management + packaging | Mature; supports PEP 621 `[project]` table since 2.0 |
| PDM / Hatch | PEP-compliant project managers | Hatch good for build/envs matrix |
| conda / pixi | Non-Python native deps (CUDA, GDAL) | Data science |
| pyenv / `uv python` | Multiple Python versions | — |
| pipx / `uv tool` | Install CLI tools globally isolated | — |

`pyproject.toml` (PEP 621):

```toml
[project]
name = "acme-billing"
version = "1.4.0"
requires-python = ">=3.12"
dependencies = ["fastapi>=0.115", "pydantic>=2.8", "sqlalchemy>=2.0"]

[project.optional-dependencies]
postgres = ["psycopg[binary]>=3.2"]

[dependency-groups]               # PEP 735 dev groups
dev = ["pytest>=8", "pytest-cov", "mypy", "ruff"]

[project.scripts]
billing = "acme_billing.cli:main"

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.ruff]
line-length = 100
[tool.ruff.lint]
select = ["E", "F", "I", "B", "UP", "SIM", "RUF", "ASYNC", "PT"]
```

Layout: prefer `src/` layout (`src/acme_billing/…`) so tests run against the installed package. Lock files: `uv.lock`, `poetry.lock`; PEP 751 standardizes `pylock.toml`. Publish wheels to PyPI or a private index (Azure Artifacts, GitHub Packages) via trusted publishing (OIDC) — no long-lived tokens.

---

## 5. FastAPI

```python
from typing import Annotated
from fastapi import FastAPI, Depends, HTTPException, status
from sqlalchemy.ext.asyncio import AsyncSession

app = FastAPI(title="Billing API")

async def get_session() -> AsyncIterator[AsyncSession]:
    async with SessionLocal() as session:
        yield session                         # dependency with teardown

SessionDep = Annotated[AsyncSession, Depends(get_session)]

class InvoiceOut(BaseModel):
    id: int
    total: int
    model_config = {"from_attributes": True}

@app.get("/invoices/{invoice_id}", response_model=InvoiceOut)
async def get_invoice(invoice_id: int, session: SessionDep) -> Invoice:
    invoice = await session.get(Invoice, invoice_id)
    if invoice is None:
        raise HTTPException(status.HTTP_404_NOT_FOUND, "invoice not found")
    return invoice
```

Key points:
- Type hints drive validation, serialization and OpenAPI docs (`/docs`).
- `Depends` = dependency injection (DB sessions, auth, settings); override in tests via `app.dependency_overrides`.
- `async def` endpoints run on the event loop — **never** call blocking I/O inside; plain `def` endpoints run in a threadpool.
- Use `lifespan` context manager for startup/shutdown (DB pools, clients).
- Production: Uvicorn (multiple workers) behind a reverse proxy, or Gunicorn with Uvicorn workers; `fastapi run` for simple cases.
- Pair with SQLAlchemy 2.0 (async) or SQLModel; Alembic for migrations.

## 6. Django overview

| Component | Notes |
|---|---|
| ORM | Models, migrations (`makemigrations`/`migrate`), QuerySets (lazy) |
| N+1 | `select_related` (FK joins), `prefetch_related` (separate query for M2M/reverse) |
| Admin | Auto CRUD UI — huge productivity win for back-office |
| Auth | Users, groups, permissions, sessions |
| Views | Function or class-based; Django REST Framework or Django Ninja for APIs |
| Templates | Server-rendered; pairs well with htmx |
| Async | Async views and ORM methods (`aget`, `afilter`), still partly sync under the hood |
| Security | CSRF, XSS escaping, SQL injection protection, clickjacking middleware by default |

**FastAPI vs Django:** FastAPI for API-only services with strong typing and async needs; Django when you want admin, auth, ORM, migrations and conventions out of the box (monolithic web apps, back-offices). Flask/Litestar are alternatives.

---

## 7. Async

```python
import asyncio, httpx

async def fetch_all(urls: list[str]) -> list[str]:
    async with httpx.AsyncClient(timeout=10) as client:
        async with asyncio.TaskGroup() as tg:          # 3.11+ structured concurrency
            tasks = [tg.create_task(client.get(u)) for u in urls]
    return [t.result().text for t in tasks]

# Bounded concurrency
sem = asyncio.Semaphore(10)
async def limited(coro):
    async with sem:
        return await coro

# Timeouts
async with asyncio.timeout(5): await slow()

# Run blocking code without freezing the loop
result = await asyncio.to_thread(blocking_io, arg)
```

| Concurrency model | Good for |
|---|---|
| `asyncio` | Many concurrent I/O-bound tasks (HTTP, DB with async drivers) |
| Threads (`ThreadPoolExecutor`) | Blocking I/O libraries |
| Processes (`ProcessPoolExecutor`, multiprocessing) | CPU-bound work |
| Free-threaded build (3.13+ experimental, 3.14 officially supported, PEP 703/779) | CPU parallelism in threads without the GIL — check C-extension compatibility |
| Sub-interpreters (3.14 `concurrent.interpreters`) | Isolated parallelism |

Pitfalls: calling `time.sleep`/`requests` inside `async def`; forgetting `await` (coroutine never runs — "coroutine was never awaited" warning); creating tasks without keeping references (may be garbage collected); mixing event loops.

---

## 8. Testing with pytest

```python
# conftest.py
import pytest

@pytest.fixture
def user_factory():
    def make(**overrides):
        return User(**({"id": 1, "email": "a@example.com", "active": True} | overrides))
    return make

@pytest.fixture(scope="session")
def db_engine():
    engine = create_engine(TEST_DB_URL)
    yield engine                 # teardown after yield
    engine.dispose()

@pytest.fixture
def db(db_engine):
    with db_engine.begin() as conn:
        yield conn
        conn.rollback()          # isolate each test
```

```python
# test_pricing.py
import pytest

@pytest.mark.parametrize(
    ("qty", "unit", "expected"),
    [(1, 100, 100), (10, 100, 900), (0, 100, 0)],
    ids=["single", "bulk-discount", "empty"],
)
def test_price(qty, unit, expected):
    assert price(qty, unit) == expected

def test_rejects_negative():
    with pytest.raises(ValueError, match="negative"):
        price(-1, 100)

def test_uses_clock(monkeypatch):
    monkeypatch.setattr("billing.clock.now", lambda: FIXED_NOW)
    ...
```

| Feature | Use |
|---|---|
| Fixtures + scopes (`function`, `class`, `module`, `package`, `session`) | Setup/teardown, DI for tests |
| `conftest.py` | Shared fixtures per directory |
| `parametrize` | Table-driven tests |
| Built-ins | `tmp_path`, `monkeypatch`, `capsys`, `caplog` |
| Markers | `@pytest.mark.slow`, `skip`, `xfail` |
| Plugins | `pytest-cov`, `pytest-xdist` (parallel), `pytest-asyncio`/`anyio`, `pytest-mock`, `pytest-django`, `hypothesis` (property-based), `testcontainers`, `respx` (httpx mocking), `freezegun`/`time-machine` |

FastAPI: `TestClient(app)` or `httpx.AsyncClient(transport=ASGITransport(app))`.

---

## 9. Common pitfalls

```python
# 1. Mutable default arguments — evaluated ONCE at definition time
def add(item, bucket=[]):          # BUG: shared across calls
    bucket.append(item); return bucket
def add(item, bucket=None):        # FIX
    bucket = [] if bucket is None else bucket
    bucket.append(item); return bucket

# 2. Late-binding closures — variables looked up at call time
fns = [lambda: i for i in range(3)]
[f() for f in fns]                 # [2, 2, 2]
fns = [lambda i=i: i for i in range(3)]   # FIX: bind default → [0, 1, 2]
# or functools.partial

# 3. Modifying a list while iterating
for x in items:
    if bad(x): items.remove(x)     # skips elements
items = [x for x in items if not bad(x)]

# 4. is vs ==
a == b   # value equality;  a is b  # identity (use for None, sentinels)

# 5. Shallow copies
grid = [[0] * 3] * 3               # three references to the same row
grid = [[0] * 3 for _ in range(3)]

# 6. Catching too broadly
try: ...
except Exception: pass             # swallows bugs — catch specific, log, re-raise

# 7. Naive datetimes
datetime.now()                     # naive local time
datetime.now(tz=UTC)               # aware

# 8. Float money
0.1 + 0.2 == 0.3                   # False → Decimal or integer cents

# 9. Class attribute shared mutable state
class A: cache = {}                # shared by all instances
```

Others: circular imports (restructure or import inside function), shadowing builtins (`list`, `id`, `type`), relying on dict order pre-3.7 (fine now), `print` instead of `logging`, forgetting `encoding="utf-8"` (default UTF-8 mode becomes standard in 3.15).

---

## 10. Tooling summary

| Concern | Tool |
|---|---|
| Format + lint | Ruff (replaces Black, isort, Flake8, pyupgrade, many plugins) |
| Types | mypy / pyright |
| Env + deps | uv |
| Tests | pytest |
| Security | `pip-audit`, Bandit (or Ruff `S` rules), Dependabot/Renovate |
| Pre-commit | `pre-commit` framework |
| Docs | MkDocs Material, Sphinx |
| Profiling | `cProfile`, `py-spy`, `scalene`, `memray` |

## References

- Python docs — https://docs.python.org/3/
- PEP 8 — https://peps.python.org/pep-0008/
- PEP 20 — https://peps.python.org/pep-0020/
- PEP 484 (type hints) — https://peps.python.org/pep-0484/
- PEP 621 (pyproject metadata) — https://peps.python.org/pep-0621/
- PEP 695 (type parameter syntax) — https://peps.python.org/pep-0695/
- typing module — https://docs.python.org/3/library/typing.html
- dataclasses — https://docs.python.org/3/library/dataclasses.html
- asyncio — https://docs.python.org/3/library/asyncio.html
- Python Packaging User Guide — https://packaging.python.org
- mypy — https://mypy.readthedocs.io
- pyright — https://microsoft.github.io/pyright/
- uv — https://docs.astral.sh/uv/
- Ruff — https://docs.astral.sh/ruff/
- Pydantic — https://docs.pydantic.dev
- FastAPI — https://fastapi.tiangolo.com
- Django — https://docs.djangoproject.com
- SQLAlchemy — https://docs.sqlalchemy.org
- pytest — https://docs.pytest.org
- Hypothesis — https://hypothesis.readthedocs.io
- *Fluent Python* (Luciano Ramalho, 2nd ed.)
- *Effective Python* (Brett Slatkin, 3rd ed.)
- *Architecture Patterns with Python* (Percival & Gregory) — https://www.cosmicpython.com
