---
name: dependency-injection
description: Enforces dependency injection patterns for testable architecture. No hardcoded dependencies, everything injected. Auto-applied to service and repository files.
globs: ["app/services/**/*.py", "app/**/*service*.py", "app/**/*repository*.py", "src/**/*service*.py", "src/**/*repository*.py"]
---

## Core Principle

No class should create its own dependencies. Everything is passed in from
outside. This makes every class testable in isolation without mocking
framework internals.

## The Rule

```
❌ class UserService:
       def __init__(self):
           self.db = MysqlClient()          # Creates its own dependency
           self.cache = Redis("localhost")   # Hardcoded config

✅ class UserService:
       def __init__(self, db: MysqlClient, cache: CacheClient) -> None:
           self._db = db                    # Injected
           self._cache = cache              # Injected
```

## Python Patterns

### Constructor Injection (preferred)

```python
class UserService:
    """Handles user business logic."""

    def __init__(
        self,
        repository: UserRepository,
        email_client: EmailClient,
    ) -> None:
        self._repository = repository
        self._email_client = email_client

    async def create_user(self, data: UserCreateModel) -> UserResponseModel:
        user = await self._repository.create(data)
        await self._email_client.send_welcome(user.email)
        return UserResponseModel.model_validate(user)
```

### FastAPI Dependency Injection

```python
# app/dependencies.py
from functools import lru_cache

from app.config.settings import Settings
from app.services.mysql.mysql_client import MysqlClient
from app.services.user_service import UserService
from app.services.mysql.user_repository import UserRepository


@lru_cache
def get_settings() -> Settings:
    return Settings()


async def get_db(settings: Settings = Depends(get_settings)) -> MysqlClient:
    return MysqlClient(settings.database_url)


async def get_user_service(
    db: MysqlClient = Depends(get_db),
) -> UserService:
    repository = UserRepository(db)
    return UserService(repository=repository)
```

```python
# In router
@router.post("/users", response_model=UserResponseModel)
async def create_user(
    data: UserCreateModel,
    service: UserService = Depends(get_user_service),
) -> UserResponseModel:
    return await service.create_user(data)
```

### Testing (the payoff)

```python
# tests/services/test_user_service.py
class FakeUserRepository:
    """In-memory repository for testing."""

    def __init__(self) -> None:
        self._users: dict[int, User] = {}
        self._next_id = 1

    async def create(self, data: UserCreateModel) -> User:
        user = User(id=self._next_id, **data.model_dump())
        self._users[self._next_id] = user
        self._next_id += 1
        return user


class FakeEmailClient:
    """Records sent emails for assertion."""

    def __init__(self) -> None:
        self.sent: list[str] = []

    async def send_welcome(self, email: str) -> None:
        self.sent.append(email)


async def test_create_user_sends_welcome_email():
    repo = FakeUserRepository()
    email = FakeEmailClient()
    service = UserService(repository=repo, email_client=email)

    await service.create_user(UserCreateModel(name="Ada", email="ada@test.com"))

    assert len(email.sent) == 1
    assert email.sent[0] == "ada@test.com"
```

No mocking framework needed. No `@patch`. No `MagicMock`. Just real
objects with predictable behavior.

## What To Inject

| Dependency type | Inject? | Example |
|----------------|---------|---------|
| Database connections | ✅ Always | `MysqlClient`, `RedisClient` |
| External API clients | ✅ Always | `StripeClient`, `EmailClient` |
| Repositories | ✅ Always | `UserRepository` |
| Configuration | ✅ Always | `Settings` |
| Loggers | ✅ Prefer | `Logger` (or use module-level `logging`) |
| Pure utility functions | ❌ No | `hash_password()`, `format_date()` |
| Standard library | ❌ No | `json`, `datetime`, `pathlib` |
| Constants | ❌ No | `MAX_RETRIES`, `DEFAULT_TIMEOUT` |

## Anti-Patterns To Flag

```python
# ❌ Importing and instantiating inside a method
class OrderService:
    def process(self, order_id: int):
        from app.services.payment import PaymentGateway  # Hidden dep
        gateway = PaymentGateway()                        # Untestable
        gateway.charge(order_id)

# ❌ Module-level singleton
db = MysqlClient(os.getenv("DATABASE_URL"))  # Created at import time

class UserService:
    def get_user(self, user_id: int):
        return db.query(...)  # Coupled to module-level state

# ❌ God object that creates everything
class App:
    def __init__(self):
        self.db = MysqlClient()
        self.cache = Redis()
        self.email = SmtpClient()
        self.user_service = UserService(self.db, self.cache, self.email)
        self.order_service = OrderService(self.db, self.cache)
        # 20 more services...
    # Use a proper composition root / DI container instead
```

## Composition Root

Wire everything together in ONE place — the app factory or entry point.
This is the ONLY place where concrete classes are instantiated:

```python
# app/main.py — this is the composition root
def create_app() -> FastAPI:
    settings = Settings()
    db = MysqlClient(settings.database_url)
    cache = RedisClient(settings.redis_url)
    email = SmtpClient(settings.smtp_host)

    # Wire services
    user_repo = UserRepository(db)
    user_service = UserService(repository=user_repo, email_client=email)

    # Register with FastAPI DI
    app = FastAPI()
    app.dependency_overrides[get_user_service] = lambda: user_service
    return app
```
