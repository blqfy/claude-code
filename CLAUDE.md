# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Context

This is a **publicly exposed Claude Code source snapshot** from March 2026, mirrored for educational and defensive security research. The original code is property of Anthropic.

This repository contains only the `src/` directory from the original project.

## High-Level Architecture

### Core Loop
`main.tsx` → CLI entry using Commander.js → React/Ink renderer. The main loop (`QueryEngine.ts`) handles LLM streaming, tool-call orchestration, and retry logic.

### Tool System (`src/tools/`)
Each tool is a self-contained module with its own input schema, permission model, and execution logic. Tools are registered in `tools.ts` and executed through `Tool.ts`.

### Command System (`src/commands/`)
Slash commands (e.g., `/commit`, `/review`, `/compact`) are registered in `commands.ts`. Each command is a separate subdirectory with `index.ts` and implementation files.

### Bridge System (`src/bridge/`)
Bidirectional communication between IDE extensions (VS Code, JetBrains) and the CLI. Handles session management, permissions, and messaging.

### Service Layer (`src/services/`)
- `api/` — Anthropic API client, file uploads, bootstrap
- `mcp/` — Model Context Protocol server connections
- `analytics/` — GrowthBook feature flags

### Key Files
| File | Purpose |
|------|---------|
| `QueryEngine.ts` | Core LLM query loop, streaming, tool orchestration |
| `Tool.ts` | Base tool types and interfaces |
| `commands.ts` | Slash command registry |
| `context.ts` | System/user context collection |
| `bridgeMain.ts` | Bridge main loop for IDE integration |

### Feature Flags
Dead-code elimination via `bun:bundle`:
```typescript
import { feature } from 'bun:bundle'
const voiceCommand = feature('VOICE_MODE') ? require('./commands/voice/index.js').default : null
```
Notable flags: `PROACTIVE`, `KAIROS`, `BRIDGE_MODE`, `DAEMON`, `VOICE_MODE`, `AGENT_TRAGENTS`, `MONITOR_TOOL`

### Design Patterns

**Parallel Prefetch**: MDM settings, keychain reads, and API preconnect run in parallel before heavy module evaluation.

**Lazy Loading**: Heavy modules (OpenTelemetry, gRPC, analytics) use dynamic `import()` until needed.

**Agent Swarms**: Sub-agents via `AgentTool`, coordinated through `coordinator/`. `TeamCreateTool` enables parallel team work.

**Skill System**: Reusable workflows in `skills/` executed via `SkillTool`.

## No Build/Test Commands

This is a source snapshot only — no `package.json`, build scripts, or test suite is present.
