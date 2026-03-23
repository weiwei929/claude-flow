# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Workspace Structure

This workspace contains two projects:

- **`claude-flow/`** — Enterprise AI agent orchestration platform (TypeScript/Node.js, v2.0.0-alpha)
- **`ClaudeKimi/`** — Claude Code configuration for Kimi API (Moonshot) integration via batch script

---

## claude-flow

### Build & Dev Commands

```bash
npm run build          # Full build (clean + ESM + CJS + binary)
npm run build:esm      # ESM-only compilation
npm run build:cjs      # CommonJS-only compilation
npm run dev            # Development mode with tsx (live reload)
npm run typecheck      # TypeScript type checking without emitting
```

### Test Commands

```bash
npm test                      # Run all tests
npm run test:unit             # Unit tests only
npm run test:integration      # Integration tests
npm run test:e2e              # End-to-end tests
npm run test:coverage         # Generate coverage report
npm run test:watch            # Watch mode
```

### Lint & Format

```bash
npm run lint           # ESLint (disabled in alpha builds, may be a no-op)
npm run format         # Prettier formatting
```

### Runtime Requirements

- Node.js >=20.0.0, npm >=9.0.0
- ES Modules (ESM) throughout; `"type": "module"` in package.json
- TypeScript strict mode enabled

---

## Architecture: claude-flow

### Entry Points

- CLI entry: `src/cli/` — Commander-based CLI exposing `swarm`, `hive-mind`, `sparc`, and other subcommands
- MCP server: `src/mcp/` — 87 Model Context Protocol tools organized by category
- Main orchestration: `src/core/` — Core engine wiring all subsystems together

### Key Subsystems

| Directory | Responsibility |
|---|---|
| `src/hive-mind/` | Queen-led multi-agent coordination; spawns and manages specialized worker agents |
| `src/swarm/` | Swarm intelligence for lightweight parallel task execution |
| `src/agents/` | Individual agent implementations (architect, coder, tester, researcher, etc.) |
| `src/memory/` | Distributed memory with SQLite backend (`.swarm/memory.db`, 12 tables) and in-memory cache |
| `src/coordination/` | Inter-agent task dependency resolution and scheduling |
| `src/mcp/` | MCP tool implementations exposed to Claude Code |
| `src/communication/` | Agent messaging bus |
| `src/monitoring/` | Metrics and health check endpoints |
| `src/terminal/` | node-pty terminal emulation for agent shell access |
| `src/enterprise/` | Feature-gated enterprise capabilities |

### Two Coordination Modes

1. **`swarm`** — Ephemeral parallel agent pools for quick, bounded tasks
2. **`hive-mind`** — Persistent session with a Queen coordinator and specialized workers; survives across sessions via SQLite memory

### MCP Tool Categories (87 tools total)

Swarm orchestration, neural/cognitive models, memory management, performance monitoring, workflow automation, GitHub integration, dynamic agent management, and system/security utilities.

### Data Persistence

- `.swarm/memory.db` — SQLite database; stores agent state, task queues, session context, and encrypted sensitive data (AES-256)
- `.hive-mind/` — Hive-mind session configuration and queen state
- `.claude/` — Claude Code hook and settings configuration

### Build Output

Dual ESM/CJS output targeting Node.js 20+:
- `dist/esm/` — ES modules
- `dist/cjs/` — CommonJS
- `dist/` — Compiled binaries (Linux/macOS/Windows x64) via `pkg`

### Testing

Jest with `ts-jest`. Tests live under `src/tests/` mirroring the source structure. CI runs the full matrix including performance and swarm coordination tests.

---

## ClaudeKimi

Provides a `claude-kimi.bat` launcher that overrides Claude Code's API base URL to `https://api.moonshot.cn/anthropic/` for Kimi model access. No build step required.
