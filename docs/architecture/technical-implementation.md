# 🔧 WriteFlow Technical Implementation Details

A Node.js/TypeScript implementation based on the core architecture of Claude Code.

## 🚀 Core Technology Stack

### Node.js 22.x + TypeScript 5.3+

```json
{
  "engines": {
    "node": ">=22.0.0"
  },
  "type": "module",
  "dependencies": {
    "@anthropic-ai/sdk": "^0.24.0",
    "undici": "^6.0.0",
    "yaml": "^2.3.0",
    "react": "^18.2.0",
    "chalk": "^5.3.0"
  }
}
```

## 📨 h2A Double-Buffered Asynchronous Message Queue Implementation

### Core Queue Class (Fully Replicates Claude Code)

```typescript
// src/core/queue/h2A-queue.ts
export interface Message {
  id: string
  type: MessageType
  priority: number
  payload: any
  timestamp: number
  source: string
  deadline?: number
}

export enum MessageType {
  UserInput = 'user_input',
  AgentResponse = 'agent_response', 
  ToolInvocation = 'tool_invocation',
  SystemNotification = 'system_notification',
  TaskAssignment = 'task_assignment'
}

export class H2AAsyncMessageQueue {
  private primaryBuffer: Message[] = []
  private secondaryBuffer: Message[] = []
  private readResolve: ((value: IteratorResult<Message>) => void) | null = null
  private isProcessing = false
  private throughputMetrics = {
    messagesPerSecond: 0,
    lastSecondCount: 0,
    currentSecond: Math.floor(Date.now() / 1000)
  }

  constructor(
    private maxBufferSize: number = 10000,
    private backpressureThreshold: number = 8000
  ) {}

  // Core async iterator (zero-latency design)
  async *[Symbol.asyncIterator](): AsyncIterator<Message> {
    while (true) {
      // Check the primary buffer
      if (this.primaryBuffer.length > 0) {
        const message = this.primaryBuffer.shift()!
        this.recordThroughput()
        yield message
        continue
      }

      // Wait for a new message (asynchronous blocking)
      const message = await new Promise<Message>((resolve) => {
        this.readResolve = (result) => {
          if (!result.done && result.value) {
            resolve(result.value)
          }
        }
      })
      
      this.recordThroughput()
      yield message
    }
  }

  // Zero-latency message enqueueing (Claude Code's core advantage)
  enqueue(message: Message): boolean {
    // Strategy 1: Zero-latency path - directly pass to the waiting reader
    if (this.readResolve && !this.isProcessing) {
      this.readResolve({ done: false, value: message })
      this.readResolve = null
      return true
    }

    // Strategy 2: Buffering path - check for backpressure
    if (this.primaryBuffer.length >= this.maxBufferSize) {
      return false // Queue is full, reject the message
    }

    // Insert by priority
    this.insertByPriority(message)
    
    // Backpressure handling
    if (this.primaryBuffer.length > this.backpressureThreshold) {
      this.triggerBackpressure()
    }

    return true
  }

  private insertByPriority(message: Message): void {
    let insertIndex = this.primaryBuffer.length
    
    // Find the correct insertion point (higher priority first)
    for (let i = this.primaryBuffer.length - 1; i >= 0; i--) {
      if (this.primaryBuffer[i].priority >= message.priority) {
        insertIndex = i + 1
        break
      }
    }
    
    this.primaryBuffer.splice(insertIndex, 0, message)
  }

  private recordThroughput(): void {
    const currentSecond = Math.floor(Date.now() / 1000)
    
    if (currentSecond !== this.throughputMetrics.currentSecond) {
      this.throughputMetrics.messagesPerSecond = this.throughputMetrics.lastSecondCount
      this.throughputMetrics.lastSecondCount = 1
      this.throughputMetrics.currentSecond = currentSecond
    } else {
      this.throughputMetrics.lastSecondCount++
    }
  }

  // Performance monitoring interface
  getMetrics() {
    return {
      queueSize: this.primaryBuffer.length,
      throughput: this.throughputMetrics.messagesPerSecond,
      backpressureActive: this.primaryBuffer.length > this.backpressureThreshold
    }
  }

  private triggerBackpressure(): void {
    // Backpressure strategy: switch to the secondary buffer for processing
    if (this.secondaryBuffer.length === 0) {
      this.secondaryBuffer = this.primaryBuffer.splice(0, this.backpressureThreshold / 2)
    }
  }
}
```

## 🤖 nO Agent Main Loop Engine

```typescript
// src/core/agent/nO-engine.ts
export class NOMainAgentEngine {
  private messageQueue: H2AAsyncMessageQueue
  private contextManager: WU2ContextManager
  private toolEngine: MH1ToolEngine
  private securityValidator: SixLayerValidator
  private currentState: AgentState = AgentState.Idle

  constructor() {
    this.messageQueue = new H2AAsyncMessageQueue()
    this.contextManager = new WU2ContextManager()
    this.toolEngine = new MH1ToolEngine()
    this.securityValidator = new SixLayerValidator()
  }

  // Main Agent loop (replicates Claude Code's nO engine)
  async *run(): AsyncGenerator<AgentResponse> {
    console.log("WriteFlow Agent starting...")
    
    try {
      // Start the message queue
      const messageIterator = this.messageQueue[Symbol.asyncIterator]()
      
      while (true) {
        try {
          // 1. Get the next message
          const { value: message, done } = await messageIterator.next()
          if (done) break

          // 2. Security validation
          const securityCheck = await this.securityValidator.validate({
            type: 'message',
            content: message.payload,
            source: message.source
          })
          
          if (!securityCheck.allowed) {
            yield { type: 'error', content: `Security check failed: ${securityCheck.reason}` }
            continue
          }

          // 3. Get the current context
          const context = await this.contextManager.getCurrentContext()

          // 4. Check the Plan mode state
          const planState = await this.checkPlanMode(message, context)

          // 5. Route to the appropriate handler based on the state
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

          // 6. Update the context
          await this.contextManager.updateContext(message, context)

        } catch (error) {
          yield* this.handleError(error)
        }
      }
    } catch (error) {
      console.error("Fatal error in Agent engine:", error)
    }
  }

  private async *handleDefaultMode(
    message: Message, 
    context: ArticleContext
  ): AsyncGenerator<AgentResponse> {
    // Parse user intent
    const intent = await this.parseUserIntent(message.payload)
    
    switch (intent.type) {
      case 'slash_command':
        yield* this.executeSlashCommand(intent.command, intent.args, context)
        break
      case 'article_request':
        yield* this.handleArticleGeneration(intent, context)
        break
      case 'edit_request':
        yield* this.handleArticleEditing(intent, context)
        break
      case 'research_request':
        yield* this.handleResearchTask(intent, context)
        break
      default:
        yield* this.handleGeneralQuery(message, context)
    }
  }

  private async *executeSlashCommand(
    command: string,
    args: string,
    context: ArticleContext
  ): AsyncGenerator<AgentResponse> {
    const cmd = this.findCommand(command)
    if (!cmd) {
      yield { 
        type: 'error', 
        content: `Unknown command: ${command}\nAvailable commands: ${this.getAvailableCommands().join(', ')}`
      }
      return
    }

    // Execute the command (replicates the three types from Claude Code)
    try {
      switch (cmd.type) {
        case 'local':
          const result = await cmd.execute(args, context)
          yield { type: 'success', content: result }
          break
          
        case 'local-jsx':
          const component = await cmd.execute(args, context)
          yield { type: 'component', jsx: component }
          break
          
        case 'prompt':
          const promptData = await cmd.execute(args, context)
          yield { type: 'prompt', ...promptData }
          break
      }
    } catch (error) {
      yield { type: 'error', content: `Command execution failed: ${error.message}` }
    }
  }
}

export enum AgentState {
  Idle = 'idle',
  Processing = 'processing',
  WaitingForInput = 'waiting_for_input',
  Error = 'error'
}

export interface AgentResponse {
  type: 'success' | 'error' | 'prompt' | 'component' | 'progress'
  content?: string
  jsx?: React.ReactElement
  allowedTools?: string[]
  maxTokens?: number
}
```

## 🗜️ wU2 Context Compressor

```typescript
// src/core/context/wU2-compressor.ts
export class WU2ContextCompressor {
  private readonly COMPRESSION_THRESHOLD = 0.92 // 92% threshold
  private readonly PRESERVE_RATIO = 0.3 // Preserve 30% of core content
  
  async compress(context: ArticleContext): Promise<ArticleContext> {
    const currentTokens = this.calculateTokens(context)
    const maxTokens = this.getMaxContextTokens()
    
    // Check if compression is needed
    if (currentTokens < maxTokens * this.COMPRESSION_THRESHOLD) {
      return context
    }

    console.log(`Triggering context compression: ${currentTokens} tokens > ${Math.floor(maxTokens * this.COMPRESSION_THRESHOLD)} tokens`)

    // Perform compression
    const compressed = await this.performIntelligentCompression(context)
    
    // Log the compression result
    const compressedTokens = this.calculateTokens(compressed)
    const compressionRatio = 1 - (compressedTokens / currentTokens)
    
    console.log(`Compression complete: ${currentTokens} -> ${compressedTokens} tokens (${(compressionRatio * 100).toFixed(1)}% reduction)`)
    
    return compressed
  }

  private async performIntelligentCompression(context: ArticleContext): Promise<ArticleContext> {
    return {
      // Core context (never compressed)
      currentArticle: context.currentArticle,
      activeOutline: context.activeOutline,
      writingGoals: context.writingGoals,
      userPreferences: context.userPreferences,
      
      // Intelligently compressed content
      researchMaterial: await this.compressResearchMaterial(context.researchMaterial),
      dialogueHistory: await this.compressDialogueHistory(context.dialogueHistory),
      referenceArticles: await this.compressReferences(context.referenceArticles),
      toolUsageHistory: await this.compressToolHistory(context.toolUsageHistory),
      
      // Update metadata
      tokenCount: 0, // Will be recalculated
      compressionLevel: 0, // Will be recalculated
      lastUpdated: Date.now()
    }
  }

  private async compressResearchMaterial(materials: ResearchItem[]): Promise<ResearchItem[]> {
    if (materials.length === 0) return materials
    
    // Sort by importance score
    const scored = materials.map(item => ({
      item,
      score: this.calculateImportanceScore(item)
    })).sort((a, b) => b.score - a.score)
    
    // Keep the top 70% most important content
    const keepCount = Math.ceil(materials.length * 0.7)
    const kept = scored.slice(0, keepCount)
    
    // Compress the kept content
    return kept.map(({ item }) => ({
      ...item,
      content: this.summarizeText(item.content, 200), // Compress to 200 words
      summary: this.extractKeyPoints(item.content, 3) // Extract 3 key points
    }))
  }

  private calculateImportanceScore(item: ResearchItem): number {
    let score = 0
    
    // Timeliness (more recent content gets a higher score)
    const daysSinceCreated = (Date.now() - item.createdAt) / (1000 * 60 * 60 * 24)
    score += Math.max(0, 1 - daysSinceCreated / 30) * 0.3
    
    // Reference frequency
    score += Math.min(item.referenceCount / 10, 1) * 0.3
    
    // Content quality (length, structure, etc.)
    score += Math.min(item.content.length / 2000, 1) * 0.2
    
    // Relevance (match with the current article topic)
    score += item.relevanceScore * 0.2
    
    return score
  }
}
```

## ⚡ Slash Command System Implementation

### Command Parser (Replicates Claude Code's Parsing Logic)

```typescript
// src/cli/parser/SlashParser.ts
export interface ParsedCommand {
  name: string
  args: string
  type: 'mcp' | 'custom' | 'standard'
  isMCP: boolean
  isCustom: boolean
}

export class SlashCommandParser {
  // Replicates the parsing logic from Claude Code's chunks.100.mjs:2048
  parseCommand(input: string): ParsedCommand | null {
    // Detect slash command
    if (!input.startsWith("/")) {
      return null
    }

    // Parse command and arguments (fully replicates the original logic)
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
      args,
      type,
      isMCP,
      isCustom
    }
  }

  // Command validation (replicates the Zj2 function)
  validateCommand(commandName: string, availableCommands: SlashCommand[]): boolean {
    return availableCommands.some(cmd => 
      cmd.userFacingName() === commandName || 
      cmd.aliases?.includes(commandName)
    )
  }

  // Command lookup (replicates the cw1 function)
  findCommand(commandName: string, availableCommands: SlashCommand[]): SlashCommand {
    const command = availableCommands.find(cmd =>
      cmd.userFacingName() === commandName ||
      cmd.aliases?.includes(commandName)
    )

    if (!command) {
      const availableNames = availableCommands.map(cmd => {
        const name = cmd.userFacingName()
        return cmd.aliases ? `${name} (aliases: ${cmd.aliases.join(", ")})` : name
      }).join(", ")

      throw new ReferenceError(`Command ${commandName} not found. Available commands: ${availableNames}`)
    }

    return command
  }
}
```

### Writing Command Implementation

```typescript
// src/cli/commands/writing-commands.ts
export const WritingCommands: SlashCommand[] = [
  {
    type: "prompt",
    name: "outline",
    description: "Generate an article outline",
    aliases: ["outline", "ol"],
    
    async getPromptForCommand(args: string, context: AgentContext): Promise<string> {
      return `Please generate a detailed technical article outline for the topic "${args}". Requirements:
1. Include an engaging title
2. A logically clear chapter structure
3. The core argument for each chapter
4. Estimated word count allocation
5. Suggestions for related materials

Please generate a well-structured and easy-to-read outline.`
    },
    
    userFacingName: () => "outline",
    allowedTools: ["web_search", "read_article", "write_article"],
    progressMessage: "generating article outline"
  },

  {
    type: "prompt", 
    name: "rewrite",
    description: "Intelligently rewrite article content",
    aliases: ["rewrite", "rw"],
    
    async getPromptForCommand(args: string, context: AgentContext): Promise<string> {
      const [style, ...contentParts] = args.split(" ")
      const content = contentParts.join(" ")
      
      if (!content) {
        return `Please specify the content to rewrite. Format: /rewrite [style] [content]
Supported styles: formal, casual, technical, academic`
      }

      return `Please rewrite the following content in a ${style} style, preserving the original meaning but improving the expression:

Original text:
${content}

Rewrite requirements:
1. Maintain the core information and ideas
2. Adjust the language style to ${style}
3. Optimize sentence structure and flow
4. Ensure the logic is clear and easy to understand
5. Appropriately adjust the use of technical terms`
    },
    
    userFacingName: () => "rewrite",
    allowedTools: ["read_article", "edit_article", "style_adapter"],
    progressMessage: "rewriting content with specified style"
  },

  {
    type: "prompt",
    name: "research", 
    description: "In-depth topic research",
    aliases: ["research", "rs"],
    
    async getPromptForCommand(args: string, context: AgentContext): Promise<string> {
      return `Please conduct an in-depth research analysis on the topic "${args}", providing:

1. **Background Information**: Basic definition and history of the topic
2. **Current Status Analysis**: Current state of development and key characteristics
3. **Trend Prediction**: Future directions and potential changes
4. **Key Perspectives**: Comparison of important viewpoints from different angles
5. **Authoritative Sources**: Reliable sources of information and references
6. **Practical Suggestions**: Specific recommendations for writing

Please ensure the information is accurate, the sources are reliable, and provide citation links.`
    },
    
    userFacingName: () => "research",
    allowedTools: ["web_search", "web_fetch", "fact_checker", "citation_manager"],
    progressMessage: "conducting deep topic research"
  },

  {
    type: "local",
    name: "publish",
    description: "Publish to various platforms",
    aliases: ["publish", "pub"],
    
    async call(args: string, context: AgentContext): Promise<string> {
      const [platform, articlePath, ...options] = args.split(" ")
      
      if (!platform || !articlePath) {
        return `Usage: /publish [platform] [article_path] [options]

Supported platforms:
- wechat: Convert to WeChat Official Account format
- zhihu: Adapt to Zhihu publishing format
- medium: Convert to Medium format
- html: Generate a static HTML page

Example: /publish wechat ./articles/my-article.md`
      }

      try {
        switch (platform.toLowerCase()) {
          case "wechat":
            return await this.publishToWeChat(articlePath, options)
          case "zhihu":
            return await this.publishToZhihu(articlePath, options)
          case "medium":
            return await this.publishToMedium(articlePath, options)
          case "html":
            return await this.generateHTML(articlePath, options)
          default:
            return `Unsupported platform: ${platform}`
        }
      } catch (error) {
        return `Publishing failed: ${error.message}`
      }
    },
    
    userFacingName: () => "publish"
  },

  {
    type: "local-jsx",
    name: "settings",
    description: "Open the settings interface",
    aliases: ["settings", "config"],
    
    async call(args: string, context: AgentContext): Promise<React.ReactElement> {
      const { createElement } = await import('react')
      
      return createElement(SettingsPanel, {
        config: context.getConfig(),
        onSave: async (newConfig) => {
          await context.updateConfig(newConfig)
          console.log("Configuration saved")
        },
        onDone: (result) => {
          console.log(result ? "Settings updated" : "Settings canceled")
        }
      })
    },
    
    userFacingName: () => "settings"
  }
]
```

## 🛠️ Writing Tool System

### MH1 Tool Engine (Writing-Specialized Version)

```typescript
// src/tools/base/MH1-tool-engine.ts
export class MH1WritingToolEngine {
  private tools: Map<string, WritingTool> = new Map()
  private securityValidator: SixLayerValidator
  private executionMetrics: ToolMetrics = new ToolMetrics()

  constructor() {
    this.securityValidator = new SixLayerValidator()
    this.registerCoreTools()
  }

  private registerCoreTools(): void {
    // Article operation tools
    this.registerTool(new ReadArticleTool())
    this.registerTool(new WriteArticleTool())
    this.registerTool(new EditArticleTool())
    
    // Writing tools
    this.registerTool(new OutlineGeneratorTool())
    this.registerTool(new ContentRewriterTool())
    this.registerTool(new StyleAdapterTool())
    this.registerTool(new GrammarCheckerTool())
    
    // Research tools
    this.registerTool(new WebSearchTool())
    this.registerTool(new WebFetchTool())
    this.registerTool(new FactCheckerTool())
    this.registerTool(new CitationManagerTool())
    
    // Publishing tools
    this.registerTool(new MarkdownFormatterTool())
    this.registerTool(new WeChatConverterTool())
    this.registerTool(new HTMLGeneratorTool())
  }

  async executeTool(toolName: string, input: ToolInput): Promise<ToolResult> {
    const tool = this.tools.get(toolName)
    if (!tool) {
      throw new Error(`Tool does not exist: ${toolName}`)
    }

    // 6-layer security validation
    const securityCheck = await this.securityValidator.validate({
      type: 'tool_execution',
      toolName,
      input,
      user: input.context?.userId
    })

    if (!securityCheck.allowed) {
      throw new Error(`Security check failed: ${securityCheck.reason}`)
    }

    // Execute the tool
    const startTime = Date.now()
    try {
      const result = await tool.execute(input)
      
      // Record execution metrics
      this.executionMetrics.recordExecution(toolName, Date.now() - startTime, true)
      
      return result
    } catch (error) {
      this.executionMetrics.recordExecution(toolName, Date.now() - startTime, false)
      throw error
    }
  }
}

// Example implementation of a core writing tool
export class OutlineGeneratorTool implements WritingTool {
  name = "outline_generator"
  description = "AI-generates an article outline"
  inputSchema = OutlineGeneratorInputSchema

  async execute(input: OutlineGeneratorInput): Promise<ToolResult> {
    const anthropic = new Anthropic({
      apiKey: process.env.ANTHROPIC_API_KEY
    })

    const response = await anthropic.messages.create({
      model: "claude-3-opus-20240229",
      max_tokens: 4000,
      messages: [{
        role: "user", 
        content: `Please generate a detailed article outline for the topic "${input.topic}":

Target audience: ${input.audience || "Technical readers"}
Article length: ${input.targetLength || 2000} words
Writing style: ${input.style || "Technical"}
Special requirements: ${input.requirements || "None"}

Please generate an outline with the following structure:
1. An engaging title
2. An introduction section
3. 3-5 main body chapters
4. A conclusion section
5. The core argument and estimated word count for each chapter`
      }]
    })

    const outline = this.parseOutlineFromResponse(response.content[0].text)
    
    return {
      success: true,
      data: {
        outline,
        estimatedLength: this.calculateEstimatedLength(outline),
        suggestions: this.generateWritingSuggestions(input.topic),
        metadata: {
          model: "claude-3-opus-20240229",
          tokensUsed: response.usage.output_tokens,
          generatedAt: Date.now()
        }
      }
    }
  }

  private parseOutlineFromResponse(text: string): OutlineStructure {
    // Parse the AI-generated outline structure
    const lines = text.split('\n').filter(line => line.trim())
    const outline: OutlineItem[] = []
    
    let currentSection: OutlineItem | null = null
    
    for (const line of lines) {
      const trimmed = line.trim()
      
      // Detect heading level
      if (trimmed.startsWith('# ')) {
        outline.push({
          level: 1,
          title: trimmed.slice(2),
          content: "",
          subsections: []
        })
      } else if (trimmed.startsWith('## ')) {
        const section: OutlineItem = {
          level: 2,
          title: trimmed.slice(3),
          content: "",
          subsections: []
        }
        if (currentSection) {
          currentSection.subsections.push(section)
        } else {
          outline.push(section)
        }
      }
      // Continue parsing other levels...
    }
    
    return {
      title: this.extractTitle(text),
      sections: outline,
      estimatedLength: this.calculateEstimatedLength(outline),
      structure: this.analyzeStructure(outline)
    }
  }
}
```

## 🔄 CLI Interactive Interface

```typescript
// src/cli/index.ts
export class WriteFlowCLI {
  private agent: NOMainAgentEngine
  private commandParser: SlashCommandParser
  private inputHistory: string[] = []
  private isInteractive = false

  constructor() {
    this.agent = new NOMainAgentEngine()
    this.commandParser = new SlashCommandParser()
  }

  async start(): Promise<void> {
    // Display startup information
    console.log(chalk.cyan("WriteFlow AI Writing Assistant v1.0.0"))
    console.log(chalk.gray("Based on Claude Code Architecture | Node.js 22.x + TypeScript"))
    console.log("")
    console.log("Available commands:")
    console.log("  /outline <topic>     - Generate an article outline")
    console.log("  /rewrite <style>     - Intelligently rewrite content")
    console.log("  /research <topic>    - In-depth topic research")
    console.log("  /publish <platform>     - Publish to a platform")
    console.log("  /settings          - Open settings")
    console.log("  /help              - Show help")
    console.log("")

    // Start the Agent engine
    const agentStream = this.agent.run()
    
    // Start the interactive command line session
    await this.startInteractiveSession(agentStream)
  }

  private async startInteractiveSession(agentStream: AsyncGenerator<AgentResponse>): Promise<void> {
    const readline = require('readline')
    const rl = readline.createInterface({
      input: process.stdin,
      output: process.stdout,
      prompt: chalk.green('writeflow> ')
    })

    this.isInteractive = true
    rl.prompt()

    // Handle user input
    rl.on('line', async (line: string) => {
      const trimmed = line.trim()
      if (!trimmed) {
        rl.prompt()
        return
      }

      try {
        // Record input history
        this.inputHistory.push(trimmed)

        // Send a message to the Agent
        await this.agent.messageQueue.enqueue({
          id: this.generateMessageId(),
          type: MessageType.UserInput,
          priority: 10,
          payload: trimmed,
          timestamp: Date.now(),
          source: 'cli'
        })

        // Handle Agent responses
        await this.handleAgentResponses(agentStream, rl)

      } catch (error) {
        console.error(chalk.red("Error:"), error.message)
      }

      rl.prompt()
    })

    // Graceful shutdown
    rl.on('SIGINT', () => {
      console.log(chalk.yellow("\nShutting down WriteFlow..."))
      rl.close()
      process.exit(0)
    })
  }

  private async handleAgentResponses(
    agentStream: AsyncGenerator<AgentResponse>, 
    rl: any
  ): Promise<void> {
    try {
      const response = await agentStream.next()
      if (response.done) return

      const { value } = response

      switch (value.type) {
        case 'success':
          console.log(chalk.green("✓"), value.content)
          break
          
        case 'error':
          console.log(chalk.red("✗"), value.content)
          break
          
        case 'progress':
          // Show progress information
          process.stdout.write(chalk.yellow("⟳ ") + value.content + "\r")
          break
          
        case 'prompt':
          // AI is thinking, show a loading animation
          const spinner = ora(chalk.blue("AI is thinking...")).start()
          // Stop after a delay
          setTimeout(() => spinner.stop(), 100)
          break
          
        case 'component':
          // Render a React component (for settings UI, etc.)
          await this.renderInteractiveComponent(value.jsx, rl)
          break
      }
    } catch (error) {
      console.error(chalk.red("Error handling Agent response:"), error.message)
    }
  }

  private async renderInteractiveComponent(jsx: React.ReactElement, rl: any): Promise<void> {
    // Simplified React component rendering (for settings and other interactive UIs)
    console.log(chalk.cyan("📋 Opening interactive interface..."))
    
    // Can integrate ink.js here to render React components to the command line
    // Or use a simplified text-based interface instead
    const inquirer = await import('enquirer')
    // ... specific implementation
  }
}
```

## 📝 Configuration Management

### CLAUDE.md Compatible Configuration Format

```yaml
# CLAUDE.md - WriteFlow Configuration File (replicates Claude Code format)

Output in English

# Writing settings
writing:
  default_style: "Technical article"
  target_length: 2000
  auto_outline: true
  grammar_check: true
  fact_check: true

# AI model configuration
ai:
  provider: "anthropic"
  model: "claude-3-opus-20240229"
  temperature: 0.7
  max_tokens: 4000

# Publishing platforms
platforms:
  wechat:
    auto_format: true
    image_style: "tech" 
    template: "default"
  zhihu:
    add_references: true
    format_style: "zhihu"
  medium:
    add_tags: true
    format_style: "medium"

# Research settings
research:
  max_sources: 10
  fact_check_threshold: 0.8
  auto_citation: true
  preferred_languages: ["en", "zh"]

# Performance settings
performance:
  message_queue_size: 10000
  context_compression_threshold: 0.92
  tool_timeout: 120000
  max_concurrent_tools: 5

# Security settings
security:
  content_filter: true
  malicious_detection: true
  sandbox_mode: false
  audit_logging: true
```

---

*This implementation is fully based on the actual technology stack of Claude Code: Node.js 22.x + TypeScript, retaining its core architectural advantages.*
