# mm-print

Enhanced printing library with rich formatting support.

## Overview

`mm-print` provides a set of enhanced printing functions with beautiful formatting, advanced JSON serialization, and automatic support for popular Python libraries like Pydantic and dataclasses.

## Quick Start

```python
from mm_print import print_json, print_table, pretty_print_toml

# Pretty print JSON with automatic type handling
data = {"name": "John", "age": 30, "active": True}
print_json(data)

# Create beautiful tables
print_table(
    title="Users",
    columns=["Name", "Age", "City"],
    rows=[
        ["Alice", 25, "New York"],
        ["Bob", 30, "London"],
    ]
)

# Syntax highlighted TOML
toml_config = """
[database]
host = "localhost"
port = 5432
"""
pretty_print_toml(toml_config)
```

## API Reference

### `print_plain(*messages)`

Print messages to stdout without any formatting.

```python
from mm_print import print_plain

print_plain("Hello, world!")
print_plain("Multiple", "arguments", 42)
```

### `print_json(data, default_serializer=None)`

Print objects as beautifully formatted JSON with syntax highlighting.

```python
from mm_print import print_json
from datetime import datetime
from decimal import Decimal

# Basic usage
print_json({"key": "value", "number": 42})

# With complex types (automatically handled)
data = {
    "timestamp": datetime.now(),
    "price": Decimal("19.99"),
    "tags": {"python", "json"},  # set -> list conversion
}
print_json(data)

# Custom serializer for unknown types
def custom_serializer(obj):
    if hasattr(obj, "to_dict"):
        return obj.to_dict()
    return str(obj)

print_json(data, default_serializer=custom_serializer)
```

### `print_table(title, columns, rows)`

Create and print formatted tables with rich styling.

```python
from mm_print import print_table

print_table(
    title="Sales Report",
    columns=["Product", "Quantity", "Revenue"],
    rows=[
        ["Widget A", 150, "$1,500.00"],
        ["Widget B", 89, "$890.00"],
        ["Widget C", 200, "$2,000.00"],
    ]
)
```

### `pretty_print_toml(data, line_numbers=False, theme="monokai")`

Print TOML with syntax highlighting and optional line numbers.

```python
from mm_print import pretty_print_toml

toml_content = """
[server]
host = "0.0.0.0"
port = 8080

[database]
url = "postgresql://localhost/mydb"
max_connections = 20
"""

# Basic usage
pretty_print_toml(toml_content)

# With line numbers and custom theme
pretty_print_toml(toml_content, line_numbers=True, theme="github")
```

### `fatal(message, code=1)`

Print error message to stderr and exit with specified code.

```python
from mm_print import fatal

# Exit with code 1
fatal("Configuration file not found!")

# Exit with custom code
fatal("Database connection failed", code=2)
```

### `to_json(data, default=None, **kwargs)`

Serialize objects to JSON string with extended type support.

```python
from mm_print import to_json
from uuid import uuid4
from pathlib import Path

data = {
    "id": uuid4(),
    "path": Path("/home/user/file.txt"),
    "metadata": {"created": datetime.now()}
}

json_string = to_json(data)
print(json_string)

# Pass additional options to json.dumps
json_string = to_json(data, indent=2, ensure_ascii=False)
```

## Extended JSON Serialization

`mm-print` automatically handles many Python types that aren't natively JSON serializable:

### Built-in Types
- **UUID** → string representation
- **Decimal** → string representation
- **Path** → string representation
- **datetime/date** → ISO format string
- **set/frozenset** → list
- **bytes** → UTF-8 decoded string
- **complex** → `{"real": x, "imag": y}`

### Python Structures
- **dataclasses** → dictionary via `asdict()`
- **Enum** → enum value
- **Exception** → string representation

### Example

```python
from mm_print import print_json
from datetime import datetime
from decimal import Decimal
from uuid import uuid4
from pathlib import Path
from dataclasses import dataclass
from enum import Enum

class Status(Enum):
    ACTIVE = "active"
    INACTIVE = "inactive"

@dataclass
class User:
    id: str
    name: str
    created_at: datetime
    status: Status

user = User(
    id=str(uuid4()),
    name="John Doe",
    created_at=datetime.now(),
    status=Status.ACTIVE
)

# All types automatically serialized
print_json({
    "user": user,
    "price": Decimal("99.99"),
    "config_path": Path("/etc/config.yml"),
    "tags": {"python", "json"},
})
```

## Automatic Library Support

If you have these libraries installed, their objects are automatically supported:

### Pydantic Models
```python
from pydantic import BaseModel
from mm_print import print_json

class User(BaseModel):
    name: str
    email: str
    age: int

user = User(name="Alice", email="alice@example.com", age=30)
print_json(user)  # Automatically uses user.model_dump()
```

### mm-result Types
```python
from mm_result import Result, Ok, Err
from mm_print import print_json

def divide(a: int, b: int) -> Result[float, str]:
    if b == 0:
        return Err("Division by zero")
    return Ok(a / b)

result = divide(10, 2)
print_json(result)  # Automatically uses result.to_dict()
```

## Examples

### Configuration Display
```python
from mm_print import print_json, print_table, pretty_print_toml

# Display parsed config as JSON
config = {
    "app_name": "MyApp",
    "version": "1.0.0",
    "features": {"auth", "api", "web"},
    "created": datetime.now()
}
print_json(config)

# Show database connections as table
connections = [
    ["Primary", "postgresql://localhost:5432/main", "active"],
    ["Cache", "redis://localhost:6379/0", "active"],
    ["Analytics", "clickhouse://localhost:8123/stats", "inactive"],
]
print_table("Database Connections", ["Name", "URL", "Status"], connections)
```

### Error Handling
```python
from mm_print import fatal, print_json

try:
    result = risky_operation()
    print_json({"status": "success", "result": result})
except FileNotFoundError:
    fatal("Required configuration file not found")
except Exception as e:
    print_json({"status": "error", "error": str(e)})
    fatal("Operation failed", code=1)
```
