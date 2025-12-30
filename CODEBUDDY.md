# CODEBUDDY.md

This document provides guidance for future CodeBuddy Code instances working with the WriteFlow AI Writing Assistant codebase.

## Project Overview

WriteFlow is a professional AI writing assistant based on the Claude Code architecture, which implements a specification-driven writing workflow. The core philosophy is to solve the problem of traditional "ambient writing" through a complete workflow of `specify → plan → task → write`.

## Development Commands

### Basic Development
```bash
# Build the project
npm run build

# Development mode (hot-reloading)
npm run dev

# Start the built application
npm run start

# Clean build files
npm run clean
```

### Code Quality
```bash
# TypeScript type checking
npm run typecheck

# ESLint code checking
npm run lint
npm run lint:fix
```

### Testing System
```bash
# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Component-specific tests
npm run test:queue    # h2A message queue tests
npm run test:agent    # nO Agent engine tests
npm run test:tools    # Tool system tests
npm run test:e2e      # End-to-end tests

# Performance benchmarks
npm run benchmark
```

### Running a Single Test
```bash
# Run a specific test file
npm test -- --testPathPattern="queue"
npm test -- --testNamePattern="should process messages"

# Run tests in debug mode
npm test -- --verbose --no-cache
```

## Core Architecture

### Three-Layer Architecture Design

WriteFlow adopts a three-layer architecture that replicates Claude Code:

1. **CLI Layer** (`src/cli/`)
   - Command parsing and execution (`commands/`, `executor/`)
   - Interactive UI (`WriteFlowREPL.tsx`)
   - Main application class (`writeflow-app.ts`)

2. **Core Engine Layer** (`src/core/`)
   - **h2A Message Queue** (`queue/h2A-queue.ts`): A high-performance, double-buffered asynchronous message queue, with a target of >10,000 msg/sec.
   - **nO Agent Engine** (`agent/nO-engine.ts`): Intelligent task scheduling and state management.
   - **wU2 Context Compression** (`context/wU2-compressor.ts`): Intelligent context compression and management.
   - **Six-Layer Security Validation** (`security/six-layer-validator.ts`): A multi-layered security protection mechanism.

3. **Tool Ecosystem Layer** (`src/tools/`)
   - Basic tools: file operations, search, system commands.
   - Writing tools: outline generation, content rewriting, grammar checking.
   - Research tools: web search, fact-checking, citation management.
   - Publishing tools: platform format conversion.

### Key Design Patterns

#### 1. Specification-Driven Workflow
A 4-command system that implements specification-driven writing:
- `/specify <topic>`: Transforms a vague requirement into a clear writing specification.
- `/plan`: Generates a detailed content plan based on the specification.
- `/task`: Decomposes the plan into executable writing tasks.
- `/write <task>`: Performs precise writing based on a clear task.

#### 2. Plan Mode System
A complete replica of Claude Code's Plan mode:
- **PlanModeManager** (`src/modes/PlanModeManager.ts`): Core state management.
- **ExitPlanMode Tool**: A tool call for the AI to request an exit from Plan mode.
- **UI Integration**: A Plan mode warning and confirmation interface.
- **Permission Control**: Only read-only operations are allowed in Plan mode.

#### 3. Event-Driven Architecture
`WriteFlowApp` inherits from `EventEmitter`, with key events:
- `plan-mode-changed`: The state of Plan mode has changed.
- `exit-plan-mode`: A request to exit Plan mode.
- `ai-thinking`: The AI's thinking process.
- `todo:changed`: The TodoList has been updated.

#### 4. Tool System Architecture
A dual tool system design:
- **`WritingTool` Interface**: The traditional tool interface (`src/types/tool.ts`).
- **`WriteFlowTool` Interface**: The enhanced tool interface (`src/Tool.ts`).
- **Tool Orchestrator**: Unified tool management and permission control.

## Important Implementation Details

### AI Service Integration
- **`WriteFlowAIService`** (`src/services/ai/WriteFlowAIService.ts`): A unified AI service interface.
- Supports multiple providers: Anthropic, DeepSeek, Qwen, GLM.
- Supports streaming output and tool calls.

### Memory and Context Management
- **`MemoryManager`**: A three-layer memory architecture (short-term, mid-term, long-term).
- **`ContextManager`**: Intelligent context compression and retrieval.
- **`TodoManager`**: Task state persistence.

### UI System (React + Ink)
- **`WriteFlowREPL`**: The main interactive interface.
- **`PlanModeAlert`/`PlanModeConfirmation`**: Plan mode UI components.
- **`TodoPanel`**: The task panel.
- **`Message`**: Unified message rendering.

### Performance Optimization
- A double-buffered message queue to avoid blocking.
- Intelligent context compression to reduce token usage.
- Asynchronous tool calls and concurrency support.
- Streaming output to improve user experience.

## Development Conventions

### TypeScript Configuration
- Strict mode is enabled.
- ESNext module system.
- Path alias: `@/*` maps to `src/*`.
- Different `tsconfig` files for build and development.

### Testing Strategy
- Jest testing framework.
- Tests are categorized by component (queue, agent, tools).
- End-to-end tests to validate the complete workflow.
- Performance benchmarks to ensure metrics are met.

### Code Organization Principles
- Functional modularity: each core function has its own directory.
- Interface-first: define clear type interfaces.
- Event-driven: use events to decouple inter-component communication.
- Error handling: unified error handling and logging.

## Debugging and Monitoring

### Debug Mode
```bash
# Enable detailed debug logs
DEBUG=writeflow:* npm run dev

# Debug specific components
DEBUG=writeflow:h2a,writeflow:nO npm run dev

# Performance analysis
npm run start -- status
```

### Key Log Points
- h2A queue: message throughput and latency.
- nO Agent: task scheduling and state transitions.
- Plan mode: mode switching and permission checks.
- Tool calls: execution status and error handling.

## Extension Development

### Adding a New Tool
1. Implement the `WritingTool` interface.
2. Create it in the corresponding category directory in `src/tools/`.
3. Register it in the `ToolManager`.
4. Add a corresponding test.

### Adding a New Command
1. Create a command definition in `src/cli/commands/core/`.
2. Implement the `getPromptForCommand` method.
3. Register it in the command index.
4. Update the help documentation.

### Plan Mode Extension
Plan mode is a core feature, and any modifications require:
1. Updating the `PlanModeManager` state management.
2. Synchronizing the UI component state.
3. Ensuring permission control is correct.
4. Testing the complete workflow.

## Important Notes

- **Do not modify existing test cases**: If a test is indeed problematic, it needs to be confirmed.
- **Maintain consistency with the Claude Code architecture**: Core design patterns should not be changed arbitrarily.
- **Performance first**: All modifications must consider their impact on the >10,000 msg/sec target.
- **Specification-driven**: New features should conform to the `specify`→`plan`→`task`→`write` workflow.
- **Keep in mind the CLAUDE.md requirements**.

## Current Development Status

**Phase 1 has been completed**: The core 4-command system has been implemented and integrated with Plan mode.
- ✅ The specification-driven writing workflow has been established.
- ✅ Plan mode is fully integrated.
- ✅ The tool system and UI components have been improved.
- ✅ Compilation and basic tests have passed.

**Next Phase Goal**: Workflow optimization and user experience improvement.
