---
name: clean-code
description: Apply clean code conventions. Use when writing or refactoring any code in this project.
globs: ["src/**/*.py", "src/**/*.ts", "src/**/*.js"]
---

When writing code in this project, always apply these patterns:

## Structure
- Early returns over nested if/else
- Guard clauses at function top
- One responsibility per function
- Extract helper when a block could have a name

## Naming
- Functions: verb_noun (calculate_price, fetch_user, validate_input)
- Classes: singular nouns (PriceCalculator, UserRepository)
- Files: snake_case of the class they contain (PriceCalculator → price_calculator.py)
- Booleans: is_, has_, can_ prefix
- Constants: UPPER_SNAKE_CASE

## Error Handling
- Fail fast with descriptive errors
- Use custom exception classes, not generic Exception
- Never silently swallow errors
- Log context: what failed, what was the input, what was expected

## Type Safety
- Type hints on all function signatures (Python)
- TypeScript strict mode (TS)
- Prefer Literal types and Enums over raw strings for finite sets

## Example Transform

Bad:
```python
def process(data, flag, mode, output, config, logger):
    if data:
        if flag:
            if mode == "fast":
                # 80 lines of logic
```

Good:
```python
@dataclass
class ProcessConfig:
    flag: bool
    mode: Literal["fast", "slow"]
    output: Path
    config: AppConfig

def process(data: InputData, cfg: ProcessConfig) -> Result:
    if not data:
        raise ValueError(f"Empty input data")

    if not cfg.flag:
        return Result.skip(reason="flag disabled")

    return _process_fast(data, cfg) if cfg.mode == "fast" else _process_slow(data, cfg)
```
