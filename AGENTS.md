# AI Agent Email Skill (aaes) - Agent Guidelines

## Build / Lint / Test Commands

### Running Scripts
All scripts follow a JSON-over-stdin contract:
```bash
# Run a single script with JSON input
echo '{"requestId":"test","schemaVersion":"1.0","data":{"maxResults":5}}' | python scripts/mail_list.py

# Windows PowerShell
echo '{"requestId":"test","schemaVersion":"1.0","data":{"maxResults":5}}' | python scripts/mail_list.py

# Windows CMD
echo {"requestId":"test","schemaVersion":"1.0","data":{"maxResults":5}} | python scripts/mail_list.py
```

### Testing
No formal test framework exists. Test manually by:
1. Copy `aaes/config.default.toml` to `aaes/config.toml`
2. Set environment variables (see SKILL.md for auth setup)
3. Run scripts via stdin/JSON interface

### Linting
No linter configured. Follow the existing code style below.

---

## Code Style Guidelines

### Python Version
- **Target**: Python 3.14+
- **Features**: Use modern type hint syntax (`list[str]` not `List[str]`)

### Imports
Order imports in three groups (separated by blank lines):
1. Standard library (e.g., `import json`, `from typing import Any`)
2. Third-party (none currently used)
3. Local imports (e.g., `from scripts.common import ...`)

Use relative imports within `common/` package. Import from `scripts.common` in scripts.

### Formatting
- **Indentation**: Tabs (not spaces)
- **Line length**: ~120 characters (flexible)
- **Blank lines**: Separate logical sections, functions, and import groups
- **Trailing commas**: Use in multi-line collections

### Type Hints
- Always annotate function parameters and return types
- Use modern syntax: `list[str]`, `dict[str, Any]`, `tuple[int, str]`
- Use `Any` sparingly, prefer specific types
- Use `|` for unions: `str | None` not `Optional[str]`

### Naming Conventions
- **Functions/variables**: `snake_case` (e.g., `validate_imap_query`, `account_name`)
- **Classes**: `PascalCase` (e.g., `SkillError`, `_HTMLToTextParser`)
- **Constants**: `SCREAMING_SNAKE_CASE` (e.g., `SCHEMA_VERSION`, `IMAP_SEARCH_COMMANDS`)
- **Private helpers**: Prefix with underscore (e.g., `_extract_summary`, `_get_account_names`)

### Error Handling
Use the `SkillError` pattern throughout:
```python
from scripts.common import SkillError

# Raise with error code, message, and optional details
raise SkillError("VALIDATION_ERROR", "data.folder must be string")
raise SkillError("CONFIG_ERROR", "config file not found", {"configPath": str(path)})
```

**Error codes**: `VALIDATION_ERROR`, `CONFIG_ERROR`, `AUTH_ERROR`, `NETWORK_ERROR`, 
`MAIL_OPERATION_ERROR`, `MAILBOX_ERROR`, `INTERNAL_ERROR`

Wrap script entry points with `with_runtime()` handler:
```python
if __name__ == "__main__":
    raise SystemExit(with_runtime(handler))
```

### Docstrings
Use Google-style docstrings for public functions:
```python
def validate_imap_query(query: str) -> str:
    """Validate IMAP search query against injection attacks.
    
    Args:
        query: The IMAP search query string to validate
        
    Returns:
        The validated query string
        
    Raises:
        SkillError: If the query contains invalid or dangerous commands
    """
```

### JSON Protocol
All scripts communicate via JSON:
- **Request**: `{"requestId": "...", "schemaVersion": "1.0", "account": "...", "data": {...}}`
- **Success**: `{"ok": true, "requestId": "...", "schemaVersion": "1.0", "data": {...}}`
- **Error**: `{"ok": false, "requestId": "...", "schemaVersion": "1.0", "error": {"code": "...", "message": "..."}}`
- **Logs**: Write to stderr as JSON

### Resource Management
Always use try/finally for IMAP/SMTP connections:
```python
client = connect_imap(account_cfg)
try:
    # ... operations ...
finally:
    close_imap_safely(client)
```

### Security
- Validate all user inputs (use `validate_imap_query`, `validate_folder_name`)
- Never log sensitive data (passwords, tokens)
- Reject queries with `()";` characters to prevent IMAP injection
- SSL verification enabled by default (`ssl_verify = true`)

---

## Project Structure
```
aaes/
  SKILL.md                  # Skill documentation
  config.default.toml       # Default config template
  config.toml               # Runtime config (gitignored)
  scripts/
    common/                 # Shared utilities package
      __init__.py           # Exports all public symbols
      errors.py             # SkillError exception, constants
      protocol.py           # JSON stdin/stdout handling
      config.py             # Config loading/validation
      auth.py               # Authentication (password/OAuth2)
      validators.py         # Input validation
      imap_utils.py         # IMAP connection helpers
      smtp_utils.py         # SMTP connection helpers
      parsers.py            # Email parsing utilities
    folder_*.py             # Folder management scripts
    mail_*.py               # Email operation scripts
requirements.txt            # Dependencies (click>=8.0)
```

---

## Existing Rules
- No Cursor rules (`.cursor/rules/` or `.cursorrules` not present)
- No Copilot rules (`.github/copilot-instructions.md` not present)
