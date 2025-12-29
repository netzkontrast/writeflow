# ⚡ WriteFlow Slash Command System

A writing-specific command system based on the Claude Code command parsing mechanism.

## 🎯 Command System Design Philosophy

A complete replica of the Claude Code slash command architecture, including:
- **Three command types**: `local`, `local-jsx`, `prompt`
- **Command parser**: Replicates the parsing logic of `chunks.100.mjs:2048`
- **MCP support**: Compatible with MCP protocol commands
- **Alias system**: Supports both English and Chinese aliases

### `/help` - Help Command
- **`/help`**: Shows a list of all available commands and their descriptions.
- **`/help [command]`**: Shows detailed information about a specific command.

### Chinese Alias Examples
- **`/大纲`**: Equivalent to `/outline`
- **`/改写`**: Equivalent to `/rewrite`
- **`/研究`**: Equivalent to `/research`
- **`/帮助`**: Equivalent to `/help`

## 📋 Core Writing Commands

### `/outline` - Outline Generation Command

```typescript
{
  type: "prompt",
  name: "outline", 
  aliases: ["大纲", "ol"],
  description: "AI-generated article outline",
  
  usage: "/outline <topic> [options]",
  examples: [
    "/outline AI agent technology development trends",
    "/outline Microservices architecture design --style=technical --length=3000"
  ],
  
  async getPromptForCommand(args: string, context: AgentContext): Promise<string> {
    const [topic, ...options] = args.split(" ")
    const style = this.extractOption(options, "style") || "technical"
    const length = this.extractOption(options, "length") || "2000"
    
    return `Please generate a detailed ${style} article outline for the topic "${topic}":

Target word count: ${length} words
Writing style: ${style}
Target audience: Technical personnel

Please generate an outline with the following structure:
1.  Catchy title suggestions (3 options)
2.  Article introduction (core problem and value)
3.  Main body chapters (3-5 main sections)
    -   Core arguments for each chapter
    -   Estimated word count allocation
    -   Key supporting materials
4.  Conclusion (summary and outlook)
5.  Writing suggestions and notes

Please ensure the outline is logical and easy to follow.`
  },
  
  allowedTools: ["web_search", "read_article", "write_article", "citation_manager"],
  progressMessage: "Generating article outline"
}
```

### `/rewrite` - Intelligent Rewrite Command

```typescript
{
  type: "prompt",
  name: "rewrite",
  aliases: ["改写", "rw", "重写"],
  description: "Intelligently rewrite article content",
  
  usage: "/rewrite <style> <content or file path>",
  examples: [
    "/rewrite casual ./articles/tech-article.md",
    "/rewrite academic This is a piece of technical content that needs rewriting...",
    "/rewrite formal --tone=professional --keep-structure"
  ],
  
  async getPromptForCommand(args: string, context: AgentContext): Promise<string> {
    const [style, ...contentParts] = args.split(" ")
    let content = contentParts.join(" ")
    
    // Check if it's a file path
    if (content.startsWith("./") || content.startsWith("/")) {
      const fileContent = await this.readFile(content)
      content = fileContent
    }
    
    if (!content) {
      throw new Error("Please provide content or a file path to rewrite.")
    }

    const styleMap = {
      "casual": "Easy to understand, suitable for a general audience",
      "formal": "Formal and rigorous, for business occasions",
      "technical": "Technically professional, for technical personnel",
      "academic": "Academic standard, conforming to thesis standards",
      "marketing": "Marketing-oriented, persuasive",
      "storytelling": "Narrative style, vivid and interesting"
    }

    const styleDesc = styleMap[style] || style

    return `Please rewrite the following content in a ${styleDesc} style:

Original content:
${content}

Rewrite requirements:
1.  Keep the core information and main points unchanged.
2.  Adjust the language style to: ${styleDesc}.
3.  Optimize sentence structure to improve readability.
4.  Ensure logical clarity and smooth expression.
5.  Appropriately adjust the use of professional terminology.
6.  Maintain the original information density and value.

Please provide the complete rewritten content.`
  },
  
  allowedTools: ["read_article", "edit_article", "style_adapter", "grammar_checker"],
  progressMessage: "Intelligently rewriting content"
}
```

### `/research` - Topic Research Command

```typescript
{
  type: "prompt", 
  name: "research",
  aliases: ["研究", "调研", "rs"],
  description: "In-depth topic research and information gathering",
  
  usage: "/research <topic> [options]",
  examples: [
    "/research AI Agent architecture design",
    "/research Blockchain technology development --depth=in-depth --sources=10",
    "/research Quantum computing applications --lang=en --time=last-year"
  ],
  
  async getPromptForCommand(args: string, context: AgentContext): Promise<string> {
    const [topic, ...options] = args.split(" ")
    const depth = this.extractOption(options, "depth") || "standard"
    const maxSources = this.extractOption(options, "sources") || "8"
    const timeRange = this.extractOption(options, "time") || "unlimited"
    const language = this.extractOption(options, "lang") || "en-zh"
    
    return `Please conduct in-depth research on the topic "${topic}" and provide a comprehensive analysis report:

Research parameters:
- Research depth: ${depth}
- Maximum sources: ${maxSources}
- Time range: ${timeRange}
- Language preference: ${language}

Please provide the following content:

## 1. Topic Overview
- Basic definition and core concepts
- Development history and important milestones
- Current importance and impact

## 2. Current Situation Analysis
- Current state of technological development
- Main players and vendors
- Market size and growth trends
- Existing problems and challenges

## 3. Latest Developments
- Recent important breakthroughs and progress
- New technologies and methods
- Industry dynamics and policy changes

## 4. Comparison of Different Viewpoints
- Main arguments of supporters
- Main concerns of skeptics
- Research directions in academia
- Application practices in industry

## 5. Authoritative Information Sources
- Academic papers and research reports
- Materials published by authoritative institutions
- Opinion articles by renowned experts
- Reliable statistical data sources

## 6. Writing Suggestions
- Suitable article angles and entry points
- Core issues of interest to readers
- Technical details that can be discussed in depth
- Practical cases and application scenarios

Please ensure the information is accurate, the sources are reliable, and provide specific citation links.`
  },
  
  allowedTools: [
    "web_search", "web_fetch", "fact_checker", 
    "citation_manager", "read_article", "write_article"
  ],
  progressMessage: "Conducting in-depth topic research"
}
```

### `/publish` - Publish Command

```typescript
{
  type: "local",
  name: "publish",
  aliases: ["发布", "pub", "deploy"],
  description: "Publish articles to various platforms",
  
  usage: "/publish <platform> <article-path> [options]",
  examples: [
    "/publish wechat ./articles/ai-trends.md",
    "/publish zhihu ./articles/blockchain.md --tags=tech,blockchain",
    "/publish medium ./articles/startup.md --draft",
    "/publish html ./articles/tutorial.md --theme=tech"
  ],
  
  async call(args: string, context: AgentContext): Promise<string> {
    const [platform, articlePath, ...options] = args.split(" ")
    
    if (!platform || !articlePath) {
      return `Usage: /publish <platform> <article-path> [options]

🌐 Supported publishing platforms:

📱 WeChat Official Account (wechat):
   - Automatically formats to WeChat style
   - Image optimization and style adjustments
   - Code block beautification
   - Example: /publish wechat article.md

🔗 Zhihu:
   - Adapts to Zhihu editor format
   - Automatically adds references
   - Tag suggestions
   - Example: /publish zhihu article.md --tags=AI,tech

📝 Medium:
   - Converts to Medium format
   - English content optimization
   - Automatically adds tags
   - Example: /publish medium article.md --draft

🌍 HTML Static Page:
   - Generates a standalone HTML file
   - Optional theme styles
   - Suitable for website publishing
   - Example: /publish html article.md --theme=tech

📄 PDF Document:
   - High-quality PDF generation
   - Suitable for printing and sharing
   - Example: /publish pdf article.md --layout=a4`
    }

    try {
      // Check if the article file exists
      const articleExists = await this.checkFileExists(articlePath)
      if (!articleExists) {
        return `❌ Article file not found: ${articlePath}`
      }

      // Read the article content
      const article = await this.readArticleFile(articlePath)
      
      // Publish based on the platform
      switch (platform.toLowerCase()) {
        case "wechat":
          return await this.publishToWeChat(article, options, context)
          
        case "zhihu":
          return await this.publishToZhihu(article, options, context)
          
        case "medium":
          return await this.publishToMedium(article, options, context)
          
        case "html":
          return await this.generateHTML(article, options, context)
          
        case "pdf":
          return await this.generatePDF(article, options, context)
          
        default:
          return `❌ Unsupported platform: ${platform}\nPlease use: wechat, zhihu, medium, html, pdf`
      }
      
    } catch (error) {
      return `❌ Publishing failed: ${error.message}`
    }
  },
  
  userFacingName: () => "publish"
}
```

### `/model` - Model Settings Command (Claude Code Replica)

```typescript
{
  type: "local-jsx",
  name: "model",
  aliases: ["模型", "ai"],
  description: "Set AI model and parameters",
  
  usage: "/model [model-name] [parameters]",
  examples: [
    "/model",                                    // Open model selection interface
    "/model claude-3-opus-20240229",            // Switch to Opus
    "/model claude-3-sonnet-20240229 --temp=0.5" // Set model and temperature
  ],
  
  async call(args: string, context: AgentContext): Promise<React.ReactElement> {
    const { createElement } = await import('react')
    const [modelName, ...params] = args.split(" ")
    
    if (!args.trim()) {
      // Open model selection interface
      return createElement(ModelSelectorPanel, {
        currentModel: context.getConfig().ai.model,
        availableModels: [
          {
            id: "claude-3-opus-20240229",
            name: "Claude 3 Opus",
            description: "Most powerful model, suitable for complex writing tasks",
            maxTokens: 4000,
            costLevel: "High"
          },
          {
            id: "claude-3-sonnet-20240229", 
            name: "Claude 3 Sonnet",
            description: "Balanced performance and cost, for daily writing",
            maxTokens: 4000,
            costLevel: "Medium"
          },
          {
            id: "claude-3-haiku-20240307",
            name: "Claude 3 Haiku", 
            description: "Fast response, for simple tasks",
            maxTokens: 4000,
            costLevel: "Low"
          }
        ],
        onSelect: async (model) => {
          await context.updateConfig({
            ai: { ...context.getConfig().ai, model: model.id }
          })
          console.log(`✓ Switched to model: ${model.name}`)
        },
        onDone: () => {
          console.log("Model settings complete.")
        }
      })
    } else {
      // Set model directly
      const config = context.getConfig()
      config.ai.model = modelName
      
      // Parse other parameters
      const temperature = this.extractParam(params, "temp") 
      const maxTokens = this.extractParam(params, "tokens")
      
      if (temperature) config.ai.temperature = parseFloat(temperature)
      if (maxTokens) config.ai.max_tokens = parseInt(maxTokens)
      
      await context.updateConfig(config)
      
      return createElement('div', null, [
        createElement('p', null, `✓ Model set to: ${modelName}`),
        temperature && createElement('p', null, `✓ Temperature: ${temperature}`),
        maxTokens && createElement('p', null, `✓ Max tokens: ${maxTokens}`)
      ].filter(Boolean))
    }
  },
  
  userFacingName: () => "model"
}
```

### `/style` - Writing Style Command

```typescript
{
  type: "prompt",
  name: "style",
  aliases: ["风格", "语调"],
  description: "Adjust article writing style",
  
  usage: "/style <target-style> [content]",
  examples: [
    "/style casual",                        // View description of casual style
    "/style formal This content needs a more formal expression",   // Rewrite directly
    "/style technical ./articles/draft.md"      // Rewrite the entire file
  ],
  
  async getPromptForCommand(args: string, context: AgentContext): Promise<string> {
    const [targetStyle, ...contentParts] = args.split(" ")
    
    const styleGuides = {
      "casual": {
        description: "Easy to understand, suitable for a general audience",
        features: ["Simple vocabulary", "Relatable analogies", "Avoid jargon", "Conversational tone"],
        tone: "Friendly and approachable"
      },
      "formal": {
        description: "Formal and rigorous, for business and official occasions",
        features: ["Standard vocabulary", "Complete sentences", "Logical rigor", "Precise wording"],
        tone: "Objective and professional"
      },
      "technical": {
        description: "Technically professional, for technical personnel",
        features: ["Accurate terminology", "Clear logic", "Sufficient detail", "Rich examples"],
        tone: "Professional and authoritative"
      },
      "academic": {
        description: "Academic standard, conforming to thesis standards",
        features: ["Proper citations", "Rigorous argumentation", "Precise wording", "Complete structure"],
        tone: "Objective and neutral"
      }
    }
    
    const guide = styleGuides[targetStyle]
    if (!guide) {
      return `Please select a valid writing style:

📝 Available styles:
${Object.entries(styleGuides).map(([name, info]) => 
  `• ${name}: ${info.description}`
).join('\n')}

Usage examples:
/style casual This technical content needs a more casual expression.
/style formal ./articles/draft.md`
    }
    
    let content = contentParts.join(" ")
    
    // Check if it's a file path
    if (content.startsWith("./") || content.startsWith("/")) {
      // This will trigger the read_article tool
      content = `[File content will be read by the read_article tool: ${content}]`
    }
    
    if (!content || content.includes("[File content will be read by")) {
      return `Please use the read_article tool to read the file, then rewrite it in a ${targetStyle} style.

Style characteristics - ${guide.description}:
${guide.features.map(f => `• ${f}`).join('\n')}
Tone: ${guide.tone}`
    }

    return `Please rewrite the following content in a ${targetStyle} style:

🎯 Target style: ${guide.description}
📋 Style characteristics: ${guide.features.join(', ')}
🗣️ Tone requirement: ${guide.tone}

Original content:
${content}

Rewrite requirements:
1.  Strictly follow the characteristics of the ${targetStyle} style.
2.  Maintain the core information and viewpoints of the original text.
3.  Adjust vocabulary and sentence structure.
4.  Ensure the rewritten content meets the needs of the target audience.
5.  Maintain logical clarity and smooth expression.

Please provide the complete rewritten result.`
  },
  
  allowedTools: ["read_article", "edit_article", "style_adapter", "grammar_checker"],
  progressMessage: "Adjusting writing style"
}
```

### `/help` - Help Command

```typescript
{
  type: "local",
  name: "help",
  aliases: ["帮助", "h", "?"],
  description: "Display command help information",
  
  async call(args: string, context: AgentContext): Promise<string> {
    if (args.trim()) {
      // Display detailed help for a specific command
      return this.getCommandHelp(args.trim())
    }
    
    return `WriteFlow AI Writing Assistant - Command Reference

📝 Writing Commands:
  /outline <topic>           Generate article outline
  /rewrite <style> <content>    Intelligently rewrite content
  /research <topic>          In-depth topic research
  /style <style> [content]      Adjust writing style

📤 Publishing Commands:
  /publish <platform> <file>    Publish to a platform
  /format <format> <file>     Format conversion

⚙️ System Commands:
  /model [model-name]           Set AI model
  /settings                 Open settings interface
  /status                   View system status
  /clear                    Clear session history

🔍 Tool Commands:
  /read <file-path>          Read file content
  /edit <file-path>          Edit file
  /search <keyword>          Search for content

💡 Usage Tips:
  - Commands support English and Chinese aliases (e.g., /大纲 is equivalent to /outline)
  - Use /help <command> to view detailed instructions
  - Most commands support --parameter=value option format

Example session:
> /outline AI agent technology
> /research Natural language processing
> /rewrite casual ./articles/technical-article.md
> /publish wechat ./articles/final-article.md`
  },
  
  userFacingName: () => "help"
}
```

## 🔧 Command Execution Engine

### Command Router (rN5 function replica)

```typescript
// src/cli/commands/command-executor.ts
export class SlashCommandExecutor {
  private commands: Map<string, SlashCommand> = new Map()
  
  constructor() {
    this.registerWritingCommands()
  }

  // Replicates the logic of Claude Code's rN5 function
  async executeCommand(
    commandName: string,
    args: string, 
    context: AgentContext,
    callbacks: CommandCallbacks
  ): Promise<CommandResult> {
    try {
      // Find the command (cw1 function replica)
      const command = this.findCommand(commandName)
      
      // Execute based on command type (replicating three type handlers)
      switch (command.type) {
        case "local-jsx":
          return this.executeJSXCommand(command, args, context, callbacks)
          
        case "local":
          return this.executeLocalCommand(command, args, context)
          
        case "prompt":
          return this.executePromptCommand(command, args, context)
          
        default:
          throw new Error(`Unknown command type: ${command.type}`)
      }
      
    } catch (error) {
      return {
        success: false,
        error: error.message,
        messages: [
          { role: 'assistant', content: `❌ Command execution failed: ${error.message}` }
        ]
      }
    }
  }

  // local-jsx type command execution (logic replica)
  private async executeJSXCommand(
    command: SlashCommand,
    args: string,
    context: AgentContext,
    callbacks: CommandCallbacks
  ): Promise<CommandResult> {
    return new Promise((resolve) => {
      command.call((output, skipMessage) => {
        if (skipMessage?.skipMessage) {
          resolve({ 
            success: true, 
            messages: [], 
            shouldQuery: false, 
            skipHistory: true 
          })
          return
        }
        
        resolve({
          success: true,
          messages: [
            {
              role: 'assistant',
              content: `<command-name>/${command.userFacingName()}</command-name>
<command-message>${command.userFacingName()}</command-message>
<command-args>${args}</command-args>`
            },
            output ? {
              role: 'assistant',
              content: `<local-command-stdout>${output}</local-command-stdout>`
            } : {
              role: 'assistant', 
              content: `<local-command-stdout>Command executed successfully</local-command-stdout>`
            }
          ],
          shouldQuery: false
        })
      }, context, args).then((jsx) => {
        callbacks.onJSXResult?.({ jsx, shouldHidePromptInput: true })
      })
    })
  }

  // local type command execution
  private async executeLocalCommand(
    command: SlashCommand,
    args: string, 
    context: AgentContext
  ): Promise<CommandResult> {
    const commandMessage = {
      role: 'assistant' as const,
      content: `<command-name>/${command.userFacingName()}</command-name>
<command-message>${command.userFacingName()}</command-message>
<command-args>${args}</command-args>`
    }

    try {
      const result = await command.call(args, context)
      return {
        success: true,
        messages: [
          commandMessage,
          {
            role: 'assistant',
            content: `<local-command-stdout>${result}</local-command-stdout>`
          }
        ],
        shouldQuery: false
      }
    } catch (error) {
      return {
        success: false,
        messages: [
          commandMessage,
          {
            role: 'assistant', 
            content: `<local-command-stderr>${String(error)}</local-command-stderr>`
          }
        ],
        shouldQuery: false
      }
    }
  }

  // prompt type command execution
  private async executePromptCommand(
    command: SlashCommand,
    args: string,
    context: AgentContext
  ): Promise<CommandResult> {
    const promptData = await command.getPromptForCommand(args, context)
    const allowedTools = command.allowedTools || []
    
    const commandMessages = [
      `<command-message>${command.userFacingName()} ${command.progressMessage || 'is processing'}…</command-message>`,
      `<command-name>/${command.userFacingName()}</command-name>`,
      args ? `<command-args>${args}</command-args>` : null
    ].filter(Boolean).join('\n')
    
    const maxThinkingTokens = await this.calculateThinkingTokens(promptData)
    
    return {
      success: true,
      messages: [
        { role: 'assistant', content: commandMessages },
        { role: 'user', content: promptData, isMeta: true }
      ],
      shouldQuery: true,
      allowedTools,
      maxThinkingTokens: maxThinkingTokens > 0 ? maxThinkingTokens : undefined
    }
  }
}
```

## 📱 React Interactive Components

### Settings Panel Component

```typescript
// src/cli/interactive/SettingsPanel.tsx
import React, { useState } from 'react'

interface SettingsPanelProps {
  config: WriteFlowConfig
  onSave: (config: WriteFlowConfig) => Promise<void>
  onDone: (success: boolean) => void
}

export const SettingsPanel: React.FC<SettingsPanelProps> = ({ 
  config, 
  onSave, 
  onDone 
}) => {
  const [formConfig, setFormConfig] = useState(config)
  const [saving, setSaving] = useState(false)

  const handleSave = async () => {
    setSaving(true)
    try {
      await onSave(formConfig)
      onDone(true)
    } catch (error) {
      console.error("Failed to save configuration:", error)
      onDone(false)
    } finally {
      setSaving(false)
    }
  }

  return React.createElement('div', { className: 'settings-panel' }, [
    React.createElement('h2', null, '⚙️ WriteFlow Settings'),
    
    // Writing settings
    React.createElement('section', null, [
      React.createElement('h3', null, '📝 Writing Settings'),
      React.createElement('label', null, [
        'Default style: ',
        React.createElement('select', {
          value: formConfig.writing.default_style,
          onChange: (e) => setFormConfig({
            ...formConfig,
            writing: { ...formConfig.writing, default_style: e.target.value }
          })
        }, [
          React.createElement('option', { value: 'technical' }, 'Technical'),
          React.createElement('option', { value: 'casual' }, 'Casual'),
          React.createElement('option', { value: 'formal' }, 'Formal'),
          React.createElement('option', { value: 'academic' }, 'Academic')
        ])
      ])
    ]),
    
    // AI model settings
    React.createElement('section', null, [
      React.createElement('h3', null, '🤖 AI Model'),
      React.createElement('label', null, [
        'Model: ',
        React.createElement('select', {
          value: formConfig.ai.model,
          onChange: (e) => setFormConfig({
            ...formConfig,
            ai: { ...formConfig.ai, model: e.target.value }
          })
        }, [
          React.createElement('option', { value: 'claude-3-opus-20240229' }, 'Claude 3 Opus'),
          React.createElement('option', { value: 'claude-3-sonnet-20240229' }, 'Claude 3 Sonnet'),
          React.createElement('option', { value: 'claude-3-haiku-20240307' }, 'Claude 3 Haiku')
        ])
      ])
    ]),
    
    // Buttons
    React.createElement('div', { className: 'button-group' }, [
      React.createElement('button', { 
        onClick: handleSave, 
        disabled: saving 
      }, saving ? 'Saving...' : '💾 Save'),
      React.createElement('button', { 
        onClick: () => onDone(false) 
      }, '❌ Cancel')
    ])
  ])
}
```

## 🎨 Command-Line Interface Rendering

### CLI Output Formatting (Claude Code Style Replica)

```typescript
// src/cli/renderer/cli-renderer.ts
export class CLIRenderer {
  private chalk = require('chalk')
  private ora = require('ora')

  // Render command execution result
  renderCommandResult(result: CommandResult): void {
    if (result.success) {
      if (result.jsx) {
        this.renderReactComponent(result.jsx)
      } else if (result.messages) {
        this.renderMessages(result.messages)
      }
    } else {
      this.renderError(result.error)
    }
  }

  // Render message list
  private renderMessages(messages: Message[]): void {
    for (const message of messages) {
      if (message.content.includes('<command-name>')) {
        // Parse command execution information
        const commandInfo = this.parseCommandInfo(message.content)
        this.renderCommandInfo(commandInfo)
      } else if (message.content.includes('<local-command-stdout>')) {
        // Render command output
        const output = this.extractCommandOutput(message.content)
        console.log(this.chalk.green("✓"), output)
      } else if (message.content.includes('<local-command-stderr>')) {
        // Render command error
        const error = this.extractCommandError(message.content)
        console.log(this.chalk.red("✗"), error)
      } else {
        // Normal message
        console.log(message.content)
      }
    }
  }

  // Render interactive progress
  renderProgress(message: string): () => void {
    const spinner = this.ora({
      text: this.chalk.blue(message),
      spinner: 'dots'
    }).start()
    
    return () => spinner.stop()
  }

  // Render article outline
  renderOutline(outline: OutlineStructure): void {
    console.log(this.chalk.cyan.bold(`📋 ${outline.title}`))
    console.log()
    
    for (const section of outline.sections) {
      const indent = '  '.repeat(section.level - 1)
      const marker = section.level === 1 ? '■' : section.level === 2 ? '▪' : '·'
      
      console.log(`${indent}${this.chalk.blue(marker)} ${this.chalk.bold(section.title)}`)
      if (section.summary) {
        console.log(`${indent}  ${this.chalk.gray(section.summary)}`)
      }
      if (section.estimatedWords) {
        console.log(`${indent}  ${this.chalk.yellow(`Estimated words: ${section.estimatedWords}`)}`)
      }
    }
    
    console.log()
    console.log(`Total estimated words: ${this.chalk.yellow.bold(outline.estimatedLength)}`)
  }

  // Render publish result
  renderPublishResult(platform: string, result: PublishResult): void {
    console.log(this.chalk.green.bold(`✅ Published to ${platform}`))
    
    if (result.url) {
      console.log(`🔗 Link: ${this.chalk.underline(result.url)}`)
    }
    
    if (result.previewPath) {
      console.log(`👀 Preview: ${result.previewPath}`)
    }
    
    if (result.stats) {
      console.log(`📊 Stats: ${result.stats.words} words, ${result.stats.characters} characters`)
    }
  }
}
```

---

*This implementation is entirely based on the real architecture of Claude Code: a Node.js CLI + TypeScript + Slash Command System*
