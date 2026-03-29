# Ecosystem-Specific Commands

## Audit and outdated commands

**Node (npm)**:
```bash
npm audit --json 2>/dev/null | head -200
npm outdated --json 2>/dev/null | head -100
```

**Node (pnpm)**:
```bash
pnpm audit --json 2>/dev/null | head -200
pnpm outdated --json 2>/dev/null | head -100
```

**Node (yarn)**:
```bash
yarn audit --json 2>/dev/null | head -200
yarn outdated --json 2>/dev/null | head -100
```

**Python (pip)**:
```bash
pip audit --format=json 2>/dev/null | head -200
pip list --outdated --format=json 2>/dev/null | head -100
```

**Rust (cargo)**:
```bash
cargo audit --json 2>/dev/null | head -200
cargo outdated --format=json 2>/dev/null | head -100
```

**Go**:
```bash
go list -m -u -json all 2>/dev/null | head -200
govulncheck ./... 2>/dev/null | head -100
```

## Update commands

- **npm**: `npm install <package>@<version>`
- **pnpm**: `pnpm update <package>@<version>`
- **yarn**: `yarn upgrade <package>@<version>`
- **pip**: `pip install <package>==<version>`
- **cargo**: update version in `Cargo.toml`, then `cargo update -p <package>`
- **go**: `go get <package>@<version>`
