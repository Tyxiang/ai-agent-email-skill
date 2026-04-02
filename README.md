# AI Agent Email Skill (aaes)

## 1. Function

See [SKILL.md](aaes/SKILL.md)

## 2. Structure

```
aaes/                       # Skill about
  SKILL.md                  # Skill documentation
  config.default.toml     # Default config file (copy to config.toml).
  config.toml             # Local runtime config (gitignored).
  scripts/
    common/                 # Shared utilities (modular package).
    folder_*.py             # Folder management scripts.
    mail_*.py               # Email operation scripts.
requirements.txt            # Python dependencies (click>=8.0)
README.md                   # Project readme
```

## 3. Package

- 用 PyInstaller 打包。
- 打包时加 `--icon "None"` 参数。

## 4. Publish

## 5. Contribution & License

Issues and PRs are welcome!

MIT License

