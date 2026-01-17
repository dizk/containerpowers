# Project Knowledge

This file captures institutional knowledge, gotchas, and patterns discovered while working on this codebase.

## API Client Gotchas

### Header Ordering Requirement
The API client requires specific header ordering. The `Authorization` header must come before `Content-Type`, otherwise the request silently fails authentication. This is due to a quirk in how the upstream service parses headers.

```python
# Correct
headers = OrderedDict([
    ("Authorization", f"Bearer {token}"),
    ("Content-Type", "application/json"),
])

# Wrong - will fail silently
headers = {
    "Content-Type": "application/json",
    "Authorization": f"Bearer {token}",
}
```

## Testing Patterns

### Container Test Performance (3x speedup)
When running container tests, reuse the container instance across test cases instead of spinning up a new container per test. Use a module-scoped fixture with cleanup between tests rather than function-scoped fixtures.

```python
@pytest.fixture(scope="module")
def container():
    c = Container.start()
    yield c
    c.stop()

@pytest.fixture(autouse=True)
def reset_container_state(container):
    container.reset()  # Fast cleanup between tests
```

This reduced our container test suite from 12 minutes to 4 minutes.

## ORM Gotchas

### Silent Data Truncation
The ORM silently truncates string fields that exceed column length. This does NOT raise an error - the data is just cut off. Always validate string lengths before saving, or add database-level constraints.

```python
# Dangerous - silently truncates if name > 255 chars
user.name = very_long_name
user.save()

# Safe - explicit validation
if len(name) > User.NAME_MAX_LENGTH:
    raise ValueError(f"Name exceeds {User.NAME_MAX_LENGTH} characters")
user.name = name
user.save()
```

Consider adding a pre-save hook to validate field lengths across all models.
