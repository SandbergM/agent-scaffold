---
name: error-handling
description: Consistent error handling patterns. Apply when writing any code that can fail.
globs: ["src/**/*.py", "src/**/*.ts", "src/**/*.js"]
---

## Principles

1. **Fail fast** — validate inputs at the boundary, not deep inside.
2. **Be specific** — custom exception types, not generic ones.
3. **Add context** — every error message should answer: what failed, what
   was the input, what was expected.
4. **Don't swallow** — no bare `except:` or empty `catch {}`.
5. **Recover or propagate** — handle it meaningfully or let it bubble up.

## Python Patterns

```python
# Custom exceptions — one per failure mode
class NotFoundError(Exception):
    def __init__(self, entity: str, identifier: str):
        super().__init__(f"{entity} not found: {identifier}")

class ValidationError(Exception):
    def __init__(self, field: str, value: Any, reason: str):
        super().__init__(f"Invalid {field}={value!r}: {reason}")

# Guard clause at function top
def get_user(user_id: str) -> User:
    if not user_id:
        raise ValidationError("user_id", user_id, "cannot be empty")

    user = db.find(user_id)
    if not user:
        raise NotFoundError("User", user_id)

    return user

# External call with retry context
def fetch_price(url: str) -> Decimal:
    try:
        response = client.get(url, timeout=5)
        response.raise_for_status()
        return parse_price(response.text)
    except httpx.TimeoutException:
        raise ExternalServiceError(f"Timeout fetching {url}")
    except httpx.HTTPStatusError as e:
        raise ExternalServiceError(f"HTTP {e.response.status_code} from {url}")
```

## TypeScript Patterns

```typescript
// Result type instead of throwing
type Result<T, E = Error> =
  | { ok: true; value: T }
  | { ok: false; error: E };

// Typed errors
class AppError extends Error {
  constructor(
    message: string,
    public readonly code: string,
    public readonly context?: Record<string, unknown>
  ) {
    super(message);
  }
}
```

## Anti-patterns to Flag

- `except Exception:` / `catch (e) {}` with no handling
- Error messages without context: "Something went wrong"
- Logging an error then re-raising the same error (double-logging)
- Using return codes when exceptions are the language convention
- Catching errors just to wrap them in the same type
