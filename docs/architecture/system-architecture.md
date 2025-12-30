# 🖋️ WriteFlow System Architecture Design

An AI writing assistant system based on the Claude Code architecture (implemented in Node.js/TypeScript).

## 🎯 System Overview

WriteFlow is a CLI writing assistant designed for technical writers, based entirely on the core architectural patterns of Claude Code:
- **h2A Double-Buffered Asynchronous Message Queue**
- **nO Agent Main Loop Engine**
- **Slash Command Interaction System**
- **MH1 Tool Execution Framework**

## 🏗️ Technology Stack Specification (Consistent with Claude Code)

```yaml
Core Technology Stack:
  Runtime: Node.js 22.x (latest LTS)
  Language: TypeScript 5.3+
  CLI Framework: Native Node.js CLI
  Build Tools: ESBuild + Vite
  Package Management: npm/pnpm

Internal Components:
  Interactive UI: React components (for certain commands)
  State Management: Native TypeScript state
  Configuration Management: YAML + Environment Variables

Tool Ecosystem:
  File Operations: fs/promises
  Network Requests: undici/fetch
  Text Processing: Native String API
  Command Execution: child_process
```

## 📁 Project Structure

```
writeflow/
├── src/
│   ├── cli/                         # CLI entry point and commands
│   │   ├── index.ts                 # Main entry file
│   │   ├── commands/                # Slash command implementations
│   │   │   ├── outline.ts           # /outline command
│   │   │   ├── rewrite.ts           # /rewrite command
│   │   │   ├── research.ts          # /research command
│   │   │   ├── publish.ts           # /publish command
│   │   │   ├── settings.tsx         # /settings command (React)
│   │   │   └── index.ts             # Command registration
│   │   ├── interactive/             # Interactive components
│   │   │   ├── CommandInput.tsx     # Command input component
│   │   │   ├── SettingsPanel.tsx    # Settings panel
│   │   │   └── ProgressView.tsx     # Progress display
│   │   └── parser/                  # Command parser
│   │       ├── SlashParser.ts       # Slash command parsing
│   │       └── ArgParser.ts         # Argument parsing
│   ├── core/                        # Core engine
│   │   ├── agent/                   # Agent system
│   │   │   ├── nO-engine.ts         # nO main loop engine
│   │   │   ├── main-agent.ts        # Main Agent
│   │   │   ├── sub-agent.ts         # Sub Agent
│   │   │   └── task-agent.ts        # Task Agent
│   │   ├── queue/                   # h2A message queue
│   │   │   ├── h2A-queue.ts         # Double-buffered queue
│   │   │   ├── message.ts           # Message definition
│   │   │   └── processor.ts         # Message processor
│   │   ├── context/                 # Context management
│   │   │   ├── wU2-compressor.ts    # Context compressor
│   │   │   ├── memory-manager.ts    # Memory management
│   │   │   └── session-state.ts     # Session state
│   │   └── security/                # Security framework
│   │       ├── validator.ts         # 6-layer validator
│   │       ├── sandbox.ts           # Sandbox environment
│   │       └── permissions.ts       # Permission control
│   ├── tools/                       # Tool implementations
│   │   ├── base/                    # Basic tools
│   │   │   ├── read-article.ts      # Read article
│   │   │   ├── write-article.ts     # Write article
│   │   │   ├── edit-article.ts      # Edit article
│   │   │   └── search-content.ts    # Search content
│   │   ├── writing/                 # Writing tools
│   │   │   ├── outline-generator.ts # Outline generator
│   │   │   ├── content-rewriter.ts  # Content rewriter
│   │   │   ├── style-adapter.ts     # Style adapter
│   │   │   └── grammar-checker.ts   # Grammar checker
│   │   ├── research/                # Research tools
│   │   │   ├── web-search.ts        # Web search
│   │   │   ├── web-fetch.ts         # Content fetching
│   │   │   ├── fact-checker.ts      # Fact checker
│   │   │   └── citation-manager.ts  # Citation manager
│   │   └── publish/                 # Publishing tools
│   │       ├── markdown-formatter.ts # Markdown formatter
│   │       ├── wechat-converter.ts   # WeChat format converter
│   │       ├── html-generator.ts     # HTML generator
│   │       └── platform-publisher.ts # Platform publisher
│   ├── types/                       # Type definitions
│   │   ├── agent.ts                 # Agent types
│   │   ├── command.ts               # Command types
│   │   ├── tool.ts                  # Tool types
│   │   ├── message.ts               # Message types
│   │   └── article.ts               # Article types
│   └── utils/                       # Utility functions
│       ├── config.ts                # Configuration management
│       ├── logger.ts                # Logging system
│       ├── crypto.ts                # Cryptography tools
│       └── validation.ts            # Validation tools
├── config/
│   ├── default.yaml                 # Default configuration
│   └── tools.yaml                   # Tool configuration
├── templates/                       # Writing templates
│   ├── article/                     # Article templates
│   ├── outline/                     # Outline templates
│   └── style/                       # Style templates
├── dist/                            # Compiled output
├── tests/                           # Test files
├── package.json
├── tsconfig.json
├── vite.config.ts                   # Build configuration
└── README.md
```

## 🤖 Agent Architecture Design

### Layered Agent System (Fully Replicates Claude Code)

```typescript
// nO Main Agent Engine
class NOMainAgent {
  private h2aQueue: H2AMessageQueue
  private contextManager: WU2ContextManager
  private toolEngine: MH1ToolEngine
  private securityValidator: SixLayerValidator

  async *agentLoop(): AsyncGenerator<AgentResponse> {
    while (true) {
      try {
        // 1. Get message
        const message = await this.h2aQueue.nextMessage()
        
        // 2. Context management
        const context = await this.contextManager.getCurrentContext()
        
        // 3. Plan mode check
        const planState = await this.checkPlanMode(message, context)
        
        // 4. Route to the corresponding handler
        switch (planState) {
          case 'default':
            yield* this.handleDefaultMode(message, context)
            break
          case 'plan':
            yield* this.handlePlanMode(message, context)
            break
          case 'acceptEdits':
            yield* this.handleAcceptEditsMode(message, context)
            break
          case 'bypassPermissions':
            yield* this.handleBypassMode(message, context)
            break
        }
      } catch (error) {
        yield* this.handleError(error)
      }
    }
  }
}
```

## 📨 h2A Message Queue System

```typescript
interface Message {
  id: string
  type: MessageType
  priority: number
  payload: any
  timestamp: number
  source: string
}

class H2AAsyncMessageQueue {
  private primaryBuffer: Message[] = []
  private secondaryBuffer: Message[] = []
  private readResolve: ((value: IteratorResult<Message>) => void) | null = null
  private writeQueue: Message[] = []
  private throughputCounter = 0
  private lastSecond = 0

  // Core async iterator (replicates Claude Code)
  async *[Symbol.asyncIterator](): AsyncIterator<Message> {
    while (true) {
      if (this.primaryBuffer.length > 0) {
        const message = this.primaryBuffer.shift()!
        this.recordThroughput()
        yield message
      } else {
        // Wait for a new message
        await new Promise<void>(resolve => {
          this.readResolve = ({ value, done }) => {
            if (!done && value) {
              this.recordThroughput()
            }
            resolve()
          }
        })
      }
    }
  }

  // Zero-latency message enqueueing (core advantage)
  enqueue(message: Message): void {
    // Strategy 1: Zero-latency path - directly pass to the waiting reader
    if (this.readResolve) {
      this.readResolve({ done: false, value: message })
      this.readResolve = null
      return
    }
    
    // Strategy 2: Buffering path - store in the circular buffer
    this.primaryBuffer.push(message)
    this.processBackpressure()
  }

  private recordThroughput(): void {
    const now = Math.floor(Date.now() / 1000)
    if (now !== this.lastSecond) {
      this.lastSecond = now
      this.throughputCounter = 1
    } else {
      this.throughputCounter++
    }
  }
}
```

## ⚡ Slash Command System

### Command Parser (Replicates Claude Code Pattern)

```typescript
class SlashCommandParser {
  parseCommand(input: string): ParsedCommand | null {
    // Detect slash command (replicates chunks.100.mjs:2048)
    if (!input.startsWith("/")) {
      return null
    }
    
    // Parse command and arguments (replicates parsing logic)
    const parts = input.slice(1).split(" ")
    let commandName = parts[0]
    let isMCP = false
    
    // MCP command detection
    if (parts.length > 1 && parts[1] === "(MCP)") {
      commandName = commandName + " (MCP)"
      isMCP = true
    }
    
    if (!commandName) {
      throw new Error("Commands are in the form `/command [args]`")
    }
    
    // Command classification
    const isCustom = commandName.includes(":")
    const type = isMCP ? "mcp" : isCustom ? "custom" : "standard"
    const args = input.slice(commandName.length + 2)
    
    return {
      name: commandName,
      type,
      args,
      isMCP,
      isCustom
    }
  }
}
```

### Writing-Specific Slash Commands

```typescript
// Writing command definitions
export const WritingCommands: SlashCommand[] = [
  {
    name: "outline",
    type: "prompt",
    description: "Generate an article outline",
    aliases: ["outline"],
    async execute(args: string, context: AgentContext): Promise<CommandResult> {
      const prompt = `Please generate a detailed article outline for the topic "${args}", including:
1. Article title suggestions
2. Core idea refinement
3. Chapter structure design
4. Key argument preparation
5. Estimated word count allocation`
      
      return {
        type: "prompt",
        prompt,
        allowedTools: ["web_search", "read_article", "write_article"],
        maxTokens: 4000
      }
    }
  },
  
  {
    name: "rewrite", 
    type: "prompt",
    description: "Intelligently rewrite article content",
    aliases: ["rewrite", "revise"],
    async execute(args: string, context: AgentContext): Promise<CommandResult> {
      const [style, ...content] = args.split(" ")
      const prompt = `Please rewrite the following content in a ${style} style:\n\n${content.join(" ")}`
      
      return {
        type: "prompt", 
        prompt,
        allowedTools: ["read_article", "edit_article", "style_adapter"],
        maxTokens: 6000
      }
    }
  },
  
  {
    name: "research",
    type: "prompt", 
    description: "In-depth topic research",
    aliases: ["research"],
    async execute(args: string, context: AgentContext): Promise<CommandResult> {
      const prompt = `Please conduct in-depth research on the topic "${args}", including:
1. Background information collection
2. Latest development trends
3. Authoritative sources
4. Comparison of different viewpoints
5. Key statistical data`
      
      return {
        type: "prompt",
        prompt,
        allowedTools: ["web_search", "web_fetch", "fact_checker", "citation_manager"],
        maxTokens: 8000
      }
    }
  },
  
  {
    name: "publish",
    type: "local",
    description: "Publish to various platforms",
    aliases: ["publish"],
    async execute(args: string, context: AgentContext): Promise<string> {
      const [platform, articlePath] = args.split(" ")
      
      switch (platform) {
        case "wechat":
          return await publishToWeChat(articlePath)
        case "zhihu":
          return await publishToZhihu(articlePath)
        case "medium":
          return await publishToMedium(articlePath)
        default:
          return `Supported platforms: wechat, zhihu, medium`
      }
    }
  },
  
  {
    name: "settings",
    type: "local-jsx",
    description: "Open the settings interface",
    aliases: ["settings", "config"],
    async execute(args: string, context: AgentContext): Promise<React.ReactElement> {
      return React.createElement(SettingsPanel, {
        onSave: (newConfig) => {
          context.updateConfig(newConfig)
        },
        currentConfig: context.getConfig()
      })
    }
  }
]
```

## 🗜️ wU2 Context Compression System

```typescript
class WU2ContextCompressor {
  private compressionThreshold = 0.92 // 92% threshold
  
  async compress(context: ArticleContext): Promise<ArticleContext> {
    const currentTokens = this.calculateTokens(context)
    const maxTokens = this.getMaxTokens()
    
    if (currentTokens < maxTokens * this.compressionThreshold) {
      return context // No compression needed
    }
    
    const compressed = await this.performCompression(context)
    
    // Log compression statistics
    const compressedTokens = this.calculateTokens(compressed)
    console.log(`Context compressed: ${currentTokens} -> ${compressedTokens} tokens (${((1 - compressedTokens/currentTokens) * 100).toFixed(1)}%)`)
    
    return compressed
  }
  
  private async performCompression(context: ArticleContext): Promise<ArticleContext> {
    return {
      // Core context (never compressed)
      currentArticle: context.currentArticle,
      activeOutline: context.activeOutline,
      writingGoals: context.writingGoals,
      userPreferences: context.userPreferences,
      
      // Compressed content
      researchMaterial: this.compressResearchMaterial(context.researchMaterial),
      dialogueHistory: this.compressDialogueHistory(context.dialogueHistory),
      referenceArticles: this.compressReferences(context.referenceArticles),
      
      // Metadata
      tokenCount: this.calculateTokens(context),
      compressionLevel: this.calculateCompressionLevel(context),
      lastUpdated: Date.now()
    }
  }
}
```

## 🛠️ Writing Tool System

### MH1 Tool Engine Adaptation

```typescript
// Base tool interface (replicates Claude Code pattern)
interface WritingTool {
  name: string
  description: string
  inputSchema: ToolInputSchema
  securityLevel: SecurityLevel
  execute(input: ToolInput): Promise<ToolResult>
}

// Core writing toolset
export const CoreWritingTools: WritingTool[] = [
  {
    name: "read_article",
    description: "Reads article content, supports multimodal files",
    inputSchema: ReadArticleInputSchema,
    securityLevel: "read-only",
    async execute(input: ReadArticleInput): Promise<ToolResult> {
      // Security validation
      await this.validateSecurity(input)
      
      // Read article
      const content = await fs.readFile(input.path, 'utf-8')
      
      // Automatic malicious content detection (replicates tG5 mechanism)
      const securityWarning = await this.checkMaliciousContent(content)
      
      return {
        success: true,
        data: {
          content,
          wordCount: this.countWords(content),
          metadata: this.extractMetadata(content),
          securityWarning
        }
      }
    }
  },
  
  {
    name: "generate_outline", 
    description: "AI generates an article outline",
    inputSchema: OutlineInputSchema,
    securityLevel: "ai-powered",
    async execute(input: OutlineInput): Promise<ToolResult> {
      const client = new AnthropicClient()
      
      const response = await client.messages.create({
        model: "claude-3-opus-20240229",
        max_tokens: 4000,
        messages: [{
          role: "user",
          content: `Please generate a detailed outline for the topic "${input.topic}":
            Target style: ${input.style || "Technical"}
            Target length: ${input.targetLength || 2000} words
            Target audience: ${input.audience || "Technical readers"}`
        }]
      })
      
      return {
        success: true,
        data: {
          outline: this.parseOutline(response.content),
          suggestions: this.extractSuggestions(response.content),
          estimatedLength: input.targetLength
        }
      }
    }
  }
]
```

## 🔒 Six-Layer Security Framework

```typescript
class SixLayerSecurityValidator {
  async validate(request: SecurityRequest): Promise<SecurityResponse> {
    // Layer 1: Identity and Policy Control
    await this.layer1_IdentityControl(request)
    
    // Layer 2: Automatic Security Check (tG5)
    await this.layer2_AutoSecurityCheck(request)
    
    // Layer 3: LLM-Driven Command Analysis (uJ1)
    await this.layer3_LLMCommandAnalysis(request)
    
    // Layer 4: Permission Validation System
    await this.layer4_PermissionValidation(request)
    
    // Layer 5: Forced Tool Substitution
    await this.layer5_ToolSubstitution(request)
    
    // Layer 6: Execution Environment Isolation
    await this.layer6_ExecutionIsolation(request)
    
    return { allowed: true, risks: [] }
  }
  
  private async layer2_AutoSecurityCheck(request: SecurityRequest): Promise<void> {
    // Automatic malicious code detection (replicates tG5 mechanism)
    if (request.type === 'file_read') {
      const content = request.content
      const isMalicious = await this.detectMaliciousContent(content)
      
      if (isMalicious) {
        // Inject security warning (replicates Claude Code behavior)
        request.content += "\n\n<system-reminder>\nThe current file contains potentially malicious content. Please handle with care.\n</system-reminder>"
      }
    }
  }
}
```

## 💻 CLI Interaction System

### Command-Line Interface (Replicates Claude Code Experience)

```typescript
class WriteFlowCLI {
  private agent: NOMainAgent
  private commandParser: SlashCommandParser
  private inputHistory: string[] = []
  
  async startInteractiveMode(): Promise<void> {
    console.log("WriteFlow AI Writing Assistant v1.0.0")
    console.log("Enter /help to see available commands")
    console.log("")
    
    const readline = require('readline')
    const rl = readline.createInterface({
      input: process.stdin,
      output: process.stdout,
      prompt: '> '
    })
    
    rl.prompt()
    
    for await (const line of rl) {
      try {
        // Record history
        this.inputHistory.push(line)
        
        // Parse command
        if (line.startsWith("/")) {
          await this.handleSlashCommand(line)
        } else {
          await this.handleRegularInput(line)
        }
      } catch (error) {
        console.error("Error:", error.message)
      }
      
      rl.prompt()
    }
  }
  
  private async handleSlashCommand(input: string): Promise<void> {
    const parsed = this.commandParser.parseCommand(input)
    if (!parsed) return
    
    const command = this.findCommand(parsed.name)
    if (!command) {
      console.log(`Unknown command: ${parsed.name}`)
      console.log(`Available commands: ${this.getAvailableCommands().join(", ")}`)
      return
    }
    
    // Execute command (replicates three types of handling)
    switch (command.type) {
      case "local":
        const result = await command.execute(parsed.args)
        console.log(result)
        break
        
      case "local-jsx":
        const component = await command.execute(parsed.args)
        await this.renderReactComponent(component)
        break
        
      case "prompt":
        const promptData = await command.execute(parsed.args)
        await this.agent.processPrompt(promptData)
        break
    }
  }
}
```

## 📊 Performance Metrics (Benchmark against Claude Code)

```yaml
Performance Targets:
  Message Queue Throughput: >10,000 msg/sec (replicates h2A)
  Agent Response Latency: <100ms
  Command Parsing Time: <10ms
  Tool Execution Timeout: 120 seconds (default)
  Peak Memory Usage: <256MB
  
Writing Feature Performance:
  Outline Generation Time: <3 seconds
  Article Rewriting Time: <10 seconds
  Topic Research Time: <30 seconds
  Format Conversion Time: <2 seconds
```

## 🔧 Configuration System

### CLAUDE.md Configuration File (Replicates Pattern)

```yaml
# writeflow/CLAUDE.md
# WriteFlow User Configuration File

Output in English

# Writing preference settings
writing:
  default_style: "Technical article"
  target_length: 2000
  auto_outline: true
  fact_check: true

# AI model configuration
ai:
  model: "claude-3-opus-20240229"
  temperature: 0.7
  max_tokens: 4000

# Publishing platform configuration
publish:
  wechat:
    auto_format: true
    image_style: "tech"
  zhihu:
    add_references: true
    format: "markdown"

# Security settings
security:
  content_filter: true
  fact_check_threshold: 0.8
  citation_required: true
```

## 📦 Package.json (Node.js 22.x)

```json
{
  "name": "writeflow",
  "version": "1.0.0",
  "description": "AI Writing Assistant - Based on Claude Code Architecture",
  "main": "dist/cli/index.js",
  "type": "module",
  "bin": {
    "writeflow": "./dist/cli/index.js"
  },
  "engines": {
    "node": ">=22.0.0"
  },
  "scripts": {
    "build": "tsc && vite build",
    "dev": "tsx src/cli/index.ts",
    "test": "jest",
    "test:watch": "jest --watch",
    "lint": "eslint src/**/*.ts",
    "start": "node dist/cli/index.js"
  },
  "dependencies": {
    "@anthropic-ai/sdk": "^0.24.0",
    "undici": "^6.0.0",
    "yaml": "^2.3.0",
    "chalk": "^5.3.0",
    "ora": "^8.0.0",
    "enquirer": "^2.4.0"
  },
  "devDependencies": {
    "@types/node": "^22.0.0",
    "@types/react": "^18.0.0",
    "typescript": "^5.3.0",
    "tsx": "^4.0.0", 
    "vite": "^5.0.0",
    "jest": "^29.0.0",
    "eslint": "^9.0.0"
  }
}
```

This redesign is based entirely on the actual technology stack of Claude Code: Node.js + TypeScript CLI, not a Go or React 18 application.
