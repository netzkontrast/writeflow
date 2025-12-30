# WriteFlow AI Writing Assistant

A professional AI writing assistant based on the Claude Code architecture, providing a complete writing solution for technical writers.

## ✨ Core Features

- **Complete Replica of Claude Code Architecture**: h2A message queue, nO Agent engine, six-layer security verification
- **AI-Driven Writing**: Intelligent outline generation, content rewriting, style adjustment, grammar checking
- **Multi-Platform Publishing**: Supports format conversion for platforms like WeChat Official Accounts, Zhihu, Medium, etc.
- **In-Depth Research**: Web search, fact-checking, citation management
- **High-Performance Design (Design Goals)**: >10,000 msg/sec message processing, <100ms response latency

## 🚀 Quick Start

### Installation

```bash
# Method 1: Install from NPM (Recommended)
npm install -g writeflow

# Method 2: Install from Source
git clone https://github.com/wordflowlab/writeflow.git
cd writeflow
npm install
npm run build
npm install -g .
```

**System Requirements**: Node.js >= 18.0.0

### Configure API Keys

Set environment variables according to the AI provider you use (optionally use `API_PROVIDER`/`AI_MODEL` to specify the default provider and model):

- Anthropic Claude: Requires setting `ANTHROPIC_API_KEY` (optional `API_BASE_URL` to override the default address)
- OpenAI: Requires setting `OPENAI_API_KEY`
- DeepSeek: Requires setting `DEEPSEEK_API_KEY` (optional `API_BASE_URL` to override the default address)
- Kimi (Moonshot): Requires setting `KIMI_API_KEY` or `MOONSHOT_API_KEY`
- BigDream (Claude Proxy): Requires setting `BIGDREAM_API_KEY`

Example:

```bash
# Select default provider and model
export API_PROVIDER=deepseek
export AI_MODEL=deepseek-chat

# Set API key (Example: DeepSeek)
export DEEPSEEK_API_KEY="your-deepseek-api-key"
# For custom gateway
export API_BASE_URL="https://api.deepseek.com"

# Examples for other providers
export ANTHROPIC_API_KEY="your-anthropic-key"
export OPENAI_API_KEY="your-openai-key"
export KIMI_API_KEY="your-kimi-key"  # or MOONSHOT_API_KEY
export BIGDREAM_API_KEY="your-bigdream-key"
```

For detailed configuration instructions and advanced usage for more providers, see `docs/ai-providers-setup.md`.

**Note**: Please replace the example API keys with your own.

### Basic Usage

```bash
# Start interactive mode (Recommended)
writeflow

# Directly execute a single slash command
writeflow exec "/outline AI technology development trends"
```

📚 **For detailed usage instructions, please see the [Quick Start Guide](docs/quick-start.md)**

## 📋 Command Reference

### Slash Command System

WriteFlow uses a slash command system, fully replicating the Claude Code command experience:

```bash
# Use slash commands in interactive mode
writeflow> /outline <topic> [options]
writeflow> /rewrite <style> <content or file path> [options]
writeflow> /research <topic> [options]
writeflow> /publish <platform> <file> [options]
writeflow> /help              # View all commands

# Supports Chinese and English aliases
writeflow> /大纲 AI技术发展   # Equivalent to /outline
writeflow> /改写 通俗 ./article.md # /rewrite casual ./article.md
writeflow> /研究 量子计算 # /research quantum computing
writeflow> /帮助              # Equivalent to /help
```

### Command Options

```bash
# Generate article outline
/outline <topic> --style=technical|formal|casual|academic --length=2000

# Intelligently rewrite content
/rewrite <style> <content or file path>

# In-depth topic research
/research <topic> --depth=standard|in-depth --sources=8 --time=last-year --lang=zh|en

# Publish to a platform
/publish <platform> <file path> --tags=AI,technology --lang=zh|en

# Format conversion
/format <target format> <file path> --preserve-style=true --output=./output-path.md
```

### CLI System Commands

```bash
# Start interactive mode (default)
writeflow

# Directly execute slash commands
writeflow exec "/outline AI technology development"
writeflow exec "/help"

# Configuration management
writeflow config --set model=claude-3-opus-20240229
writeflow config --get model
writeflow config --list

# System status
writeflow status
```

## 🏗️ Architecture Design

### Core Components

```text
┌─────────────────────────────────────────────────┐
│                 WriteFlow CLI                   │
├─────────────────────────────────────────────────┤
│  Slash Command System │  Interactive UI  │  Configuration Mgt │
├─────────────────────────────────────────────────┤
│           Tool System (Tool Manager)            │
│  Base Tools │  Writing Tools │  Research Tools │  Publish Tools  │
├─────────────────────────────────────────────────┤
│                 nO Agent Engine                 │
│  Message Proc │  Task Scheduling │  State Management     │
├─────────────────────────────────────────────────┤
│  h2A Message Queue │ wU2 Context Mgt │ 6-Layer Security   │
└─────────────────────────────────────────────────┘
```

### Performance Metrics

- **Message Queue Throughput**: >10,000 msg/sec
- **Response Latency**: <100ms
- **Memory Usage**: <256MB
- **Startup Time**: <3s

## 🛠️ Development Guide

### Project Structure

```text
src/
├── cli/                     # CLI Interface
│   ├── commands/            # Slash commands
│   ├── executor/            # Command executor
│   ├── parser/              # Command parser
│   ├── interactive/         # Interactive UI
│   ├── index.ts             # CLI entry point
│   └── writeflow-cli.ts     # Main CLI class
├── core/                    # Core engine
│   ├── agent/               # nO Agent system
│   ├── context/             # wU2 Context management
│   ├── queue/               # h2A Message queue
│   └── security/            # Security framework
├── services/                # External services & AI calls
│   ├── ai/                  # AI service wrapper
│   └── models/              # Model and provider definitions
├── tools/                   # Tool system
│   ├── base/                # Basic article operations
│   ├── writing/             # Writing tools
│   ├── research/            # Research tools
│   └── publish/             # Publishing tools
├── ui/                      # Terminal UI components (Ink)
└── types/                   # TypeScript type definitions
```

### Local Development

```bash
# Development mode
npm run dev

# Run tests
npm test
npm run test:watch

# Type checking
npm run typecheck

# Code linting
npm run lint
```

### Adding a Custom Tool

```typescript
import { WritingTool, ToolInput, ToolResult } from '@/types/tool.js'

export class CustomTool implements WritingTool {
  name = 'custom_tool'
  description = 'Custom tool description'
  securityLevel = 'safe'
  
  async execute(input: ToolInput): Promise<ToolResult> {
    // Implement custom logic
    return {
      success: true,
      content: 'Processing result'
    }
  }
}
```

### Adding a Custom Command

```typescript
{
  type: 'prompt',
  name: 'custom_command',
  description: 'Custom command',
  aliases: ['custom', 'cc'],
  async getPromptForCommand(args: string): Promise<string> {
    return `Custom prompt: ${args}`
  },
  userFacingName: () => 'custom_command'
}
```

## 🧪 Testing

```bash
# Run all tests
npm test

# Test a specific component
npm run test:queue    # Message queue tests
npm run test:agent    # Agent engine tests
npm run test:tools    # Tool system tests

# End-to-end tests
npm run test:e2e

# Performance benchmarks
npm run benchmark
```

## 📊 Monitoring and Debugging

```bash
# Enable detailed debugging
DEBUG=writeflow:* writeflow exec "/outline AI technology"

# Debug specific components
DEBUG=writeflow:h2a,writeflow:nO writeflow exec "/research machine learning"

# Performance analysis
writeflow status
writeflow config --get performance
```

## 🤝 Contribution Guide

1. Fork the project
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Create a Pull Request

### Development Standards

- Follow TypeScript strict mode
- All new features must include tests
- Maintain consistency with the Claude Code architecture
- Prioritize performance optimization

## 📄 License

This project is open-sourced under the MIT License. See the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgements

- [Anthropic](https://www.anthropic.com/) - Claude AI technical support
- [Claude Code](https://claude.ai/code) - Architectural design reference

---

**WriteFlow** - Making AI writing more professional 🚀
