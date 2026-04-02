# AI Agent Email Skill (aaes)

## 1. Introduction

See [SKILL.md](aaes/SKILL.md)

## 2. Structure

```
aaes/                       # Skill about
  SKILL.md                  # Skill documentation
  config.default.toml       # Default config file (copy to config.toml).
  scripts/
    common/                 # Shared utilities (modular package).
    folder_*.py             # Folder management scripts.
    mail_*.py               # Email operation scripts.
requirements.txt            # Python dependencies (click>=8.0)
README.md                   # Project readme
```

## 3. Contribution & License

Issues and PRs are welcome!

MIT License
