# mm-print

Enhanced printing library with rich formatting support.

## Overview

`mm-print` provides a set of enhanced printing functions with beautiful formatting and table display. JSON serialization is handled by the `mm-std` library for extended type support.

## Quick Start

```python
from mm_print import print_json, print_table, pretty_print_toml

# Pretty print JSON
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
print_plain(42)
```

### `print_json(data, type_handlers=None)`

Print objects as beautifully formatted JSON with syntax highlighting.

```python
from mm_print import print_json

# Basic usage
print_json({"key": "value", "number": 42})

# With custom type handlers
class CustomObject:
    def __init__(self, value):
        self.value = value

def serialize_custom(obj):
    return {"custom_value": obj.value}

data = {"obj": CustomObject("test")}
print_json(data, type_handlers={CustomObject: serialize_custom})
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

## Examples

### Configuration Display
```python
from mm_print import print_json, print_table, pretty_print_toml

# Display config as JSON
config = {
    "app_name": "MyApp",
    "version": "1.0.0",
    "features": ["auth", "api", "web"]
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
