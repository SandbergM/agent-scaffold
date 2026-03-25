---
name: fastapi-structure
description: FastAPI project structure and conventions. Auto-applied when the project uses FastAPI.
globs: ["app/**/*.py", "main.py"]
---

## Directory Structure

```
app/
├── models/                 # Pydantic models (DTOs, request/response schemas)
│   ├── __init__.py
│   ├── user_model.py       # UserModel, UserCreateModel, UserResponseModel
│   └── health_model.py     # HealthResponseModel
├── routers/                # API route definitions
│   ├── __init__.py
│   ├── v1_health_router.py
│   └── v1_user_router.py
├── services/               # Business logic
│   ├── __init__.py
│   ├── user_service.py     # UserService
│   └── mysql/              # Database-specific implementations
│       ├── __init__.py
│       ├── mysql_client.py # MysqlClient
│       └── user_repository.py # UserRepository
├── static/                 # Static assets
│   ├── js/
│   ├── styles/
│   ├── templates/
│   └── index.html
├── middleware/              # Custom middleware
│   ├── __init__.py
│   └── auth_middleware.py  # AuthMiddleware
├── config/                 # Configuration
│   ├── __init__.py
│   └── settings.py         # Settings (BaseSettings)
├── exceptions/             # Custom exceptions
│   ├── __init__.py
│   └── not_found_error.py  # NotFoundError
├── __init__.py
└── main.py                 # FastAPI app factory, router registration
```

## File Naming

Every file is named after the single class it contains, in `snake_case`.

| Class Name | File Name |
|-----------|-----------|
| `UserService` | `user_service.py` |
| `MysqlClient` | `mysql_client.py` |
| `UserCreateModel` | `user_create_model.py` |
| `AuthMiddleware` | `auth_middleware.py` |
| `NotFoundError` | `not_found_error.py` |
| `V1HealthRouter` | `v1_health_router.py` |

**Rule: One class per file. The file name IS the class name in snake_case.**
If you need a second class, create a second file. No exceptions.

## Naming Conventions

### Models (Pydantic)
- Suffix: `Model` (e.g., `UserModel`, `UserCreateModel`, `UserResponseModel`)
- Location: `app/models/`
- One model per file, or group related request/response models for the same
  entity in one file (e.g., `user_model.py` can contain `UserCreateModel`
  and `UserResponseModel` if they're tightly coupled)

### Routers
- Prefix with API version: `v1_`, `v2_`
- Suffix: `_router` (e.g., `v1_health_router.py`, `v1_user_router.py`)
- Location: `app/routers/`
- Each router file defines ONE `APIRouter` with a clear prefix

### Services
- Suffix: `Service` (e.g., `UserService`, `PricingService`)
- Location: `app/services/`
- Contains business logic, NO direct database calls
- Database calls go through repositories in the relevant subfolder

### Repositories / DB clients
- Location: `app/services/<db_type>/` (e.g., `app/services/mysql/`)
- Suffix: `Repository` for data access, `Client` for connections
- Keeps database implementation details isolated

## Router Pattern

```python
# app/routers/v1_health_router.py
from fastapi import APIRouter

from app.models.health_model import HealthResponseModel

router = APIRouter(prefix="/api/v1/health", tags=["health"])


@router.get("/", response_model=HealthResponseModel)
async def health_check() -> HealthResponseModel:
    return HealthResponseModel(status="ok")
```

## Service Pattern

```python
# app/services/user_service.py
from app.models.user_model import UserCreateModel, UserResponseModel
from app.services.mysql.user_repository import UserRepository


class UserService:
    """Handles user business logic."""

    def __init__(self, repository: UserRepository) -> None:
        self._repository = repository

    async def create_user(self, data: UserCreateModel) -> UserResponseModel:
        user = await self._repository.create(data)
        return UserResponseModel.model_validate(user)
```

## Model Pattern

```python
# app/models/user_model.py
from pydantic import BaseModel, EmailStr


class UserCreateModel(BaseModel):
    """Request body for creating a user."""

    name: str
    email: EmailStr


class UserResponseModel(BaseModel):
    """Response body for user endpoints."""

    id: int
    name: str
    email: str

    model_config = {"from_attributes": True}
```

## App Factory

```python
# app/main.py
from fastapi import FastAPI
from fastapi.staticfiles import StaticFiles

from app.routers import v1_health_router, v1_user_router


def create_app() -> FastAPI:
    app = FastAPI(title="MyProject", version="0.1.0")

    # Routers
    app.include_router(v1_health_router.router)
    app.include_router(v1_user_router.router)

    # Static files
    app.mount("/static", StaticFiles(directory="app/static"), name="static")

    return app
```

## Anti-patterns

- ❌ `models.py` with 15 classes → split into one file per class
- ❌ `routes.py` with all endpoints → split per domain with version prefix
- ❌ Database queries inside router functions → use service + repository
- ❌ Business logic inside models → models are data shapes only
- ❌ `utils.py` grab-bag → name the file after what it does
