# 🛠️ WriteFlow Writing Toolset

An implementation of writing-specific tools based on the Claude Code MH1 tool engine.

## 🎯 Tool Design Philosophy

A complete replica of the Claude Code tool architecture, converting programming tools into writing tools:
- **Forced Read Mechanism**: An article must be read before it can be edited (replicating the Edit tool logic).
- **Six-Layer Security Verification**: Every tool call undergoes a complete security check.
- **Bulk Operation Support**: A single response supports the concurrent execution of multiple tools.
- **Forced Tool Substitution**: Disables traditional text commands, forcing the use of specialized tools.

## 📝 Core Article Operation Tools

### ReadArticle Tool (Replicates the Read tool)

```typescript
// src/tools/base/read-article.ts
export class ReadArticleTool implements WritingTool {
  name = "read_article"
  description = "Reads the content of an article, supporting multiple formats"
  securityLevel = "read-only"
  
  inputSchema = {
    type: "object",
    properties: {
      file_path: {
        type: "string",
        description: "The absolute path to the article file"
      },
      limit: {
        type: "number", 
        description: "The maximum number of lines to read, default is 2000"
      },
      offset: {
        type: "number",
        description: "The line number to start reading from"
      }
    },
    required: ["file_path"]
  }

  async execute(input: ReadArticleInput): Promise<ToolResult> {
    // Security validation
    await this.validateSecurity(input)
    
    // Path normalization (must be an absolute path)
    const absolutePath = path.resolve(input.file_path)
    
    try {
      // Read file content
      const content = await fs.readFile(absolutePath, 'utf-8')
      const lines = content.split('\n')
      
      // Handle pagination (replicating the pagination logic of the Read tool)
      const offset = input.offset || 0
      const limit = input.limit || 2000
      const pageLines = lines.slice(offset, offset + limit)
      const pageContent = pageLines.join('\n')
      
      // Detect article format
      const format = this.detectFileFormat(absolutePath, content)
      
      // Extract article metadata
      const metadata = this.extractArticleMetadata(content, format)
      
      // Automatic malicious content detection (replicating the tG5 mechanism)
      const securityWarning = await this.checkMaliciousContent(content)
      
      // Statistics
      const stats = this.calculateArticleStats(content)
      
      return {
        success: true,
        data: {
          content: pageContent,
          format,
          metadata,
          stats,
          pagination: {
            offset,
            limit,
            totalLines: lines.length,
            hasMore: offset + limit < lines.length
          },
          securityWarning
        }
      }
      
    } catch (error) {
      return {
        success: false,
        error: `Failed to read article: ${error.message}`
      }
    }
  }

  private detectFileFormat(filePath: string, content: string): ArticleFormat {
    const ext = path.extname(filePath).toLowerCase()
    
    switch (ext) {
      case '.md':
      case '.markdown':
        return 'markdown'
      case '.txt':
        return 'plain_text'
      case '.html':
      case '.htm':
        return 'html'
      case '.docx':
        return 'docx'
      default:
        // Detect by content features
        if (content.includes('# ') || content.includes('## ')) {
          return 'markdown'
        }
        return 'plain_text'
    }
  }

  private extractArticleMetadata(content: string, format: ArticleFormat): ArticleMetadata {
    const metadata: ArticleMetadata = {}
    
    if (format === 'markdown') {
      // Parse Markdown front matter
      const frontMatterMatch = content.match(/^---\n([\s\S]*?)\n---/)
      if (frontMatterMatch) {
        try {
          const yaml = require('yaml')
          metadata.frontMatter = yaml.parse(frontMatterMatch[1])
        } catch (error) {
          // Ignore parsing errors
        }
      }
      
      // Extract title
      const titleMatch = content.match(/^#\s+(.+)$/m)
      if (titleMatch) {
        metadata.title = titleMatch[1]
      }
    }
    
    return metadata
  }

  private calculateArticleStats(content: string): ArticleStats {
    const words = content.match(/[\u4e00-\u9fa5]|[a-zA-Z]+/g) || []
    const chineseChars = content.match(/[\u4e00-\u9fa5]/g) || []
    const englishWords = content.match(/[a-zA-Z]+/g) || []
    
    return {
      totalCharacters: content.length,
      chineseCharacters: chineseChars.length,
      englishWords: englishWords.length,
      totalWords: words.length,
      paragraphs: content.split(/\n\s*\n/).filter(p => p.trim()).length,
      estimatedReadingTime: Math.ceil(words.length / 200) // Estimated at 200 words/minute
    }
  }
}
```

### WriteArticle Tool (Replicates the Write tool)

```typescript
// src/tools/base/write-article.ts
export class WriteArticleTool implements WritingTool {
  name = "write_article"
  description = "Writes article content to a file"
  securityLevel = "write"
  
  inputSchema = {
    type: "object",
    properties: {
      file_path: { type: "string", description: "The absolute path to the article file" },
      content: { type: "string", description: "The content of the article" },
      format: { type: "string", enum: ["markdown", "html", "txt"], description: "The file format" },
      metadata: { type: "object", description: "The article metadata" },
      backup: { type: "boolean", description: "Whether to create a backup" }
    },
    required: ["file_path", "content"]
  }

  async execute(input: WriteArticleInput): Promise<ToolResult> {
    // Security validation
    await this.validateSecurity(input)
    
    const absolutePath = path.resolve(input.file_path)
    
    try {
      // Check if overwriting an existing file (replicating the pre-read mechanism of the Write tool)
      const fileExists = await this.checkFileExists(absolutePath)
      if (fileExists && !input.force) {
        // Force reading the existing file first
        throw new Error(`File already exists: ${absolutePath}. Please use the read_article tool to read the file content first, or use the edit_article tool to edit it.`)
      }
      
      // Create a backup
      if (fileExists && input.backup !== false) {
        const backupPath = `${absolutePath}.backup.${Date.now()}`
        await fs.copyFile(absolutePath, backupPath)
      }
      
      // Ensure the directory exists
      const dir = path.dirname(absolutePath)
      await fs.mkdir(dir, { recursive: true })
      
      // Process article content
      let finalContent = input.content
      
      // Add metadata (if in Markdown format)
      if (input.format === 'markdown' && input.metadata) {
        const frontMatter = this.generateFrontMatter(input.metadata)
        finalContent = `---\n${frontMatter}\n---\n\n${input.content}`
      }
      
      // Write to file (atomic operation)
      const tempPath = `${absolutePath}.tmp.${process.pid}`
      await fs.writeFile(tempPath, finalContent, 'utf-8')
      await fs.rename(tempPath, absolutePath)
      
      // Calculate statistics
      const stats = this.calculateArticleStats(finalContent)
      
      return {
        success: true,
        data: {
          path: absolutePath,
          size: finalContent.length,
          format: input.format || this.detectFormat(absolutePath),
          stats,
          created_at: new Date().toISOString()
        }
      }
      
    } catch (error) {
      return {
        success: false,
        error: `Failed to write article: ${error.message}`
      }
    }
  }

  private generateFrontMatter(metadata: ArticleMetadata): string {
    const yaml = require('yaml')
    const frontMatter = {
      title: metadata.title,
      author: metadata.author,
      date: metadata.date || new Date().toISOString().split('T')[0],
      tags: metadata.tags || [],
      category: metadata.category,
      summary: metadata.summary,
      ...metadata.custom
    }
    
    return yaml.stringify(frontMatter)
  }
}
```

### EditArticle Tool (Replicates the forced read mechanism of the Edit tool)

```typescript
// src/tools/base/edit-article.ts
export class EditArticleTool implements WritingTool {
  name = "edit_article"
  description = "Precisely edits article content"
  securityLevel = "write"
  
  private fileStateTracker: FileStateTracker = new FileStateTracker()
  
  inputSchema = {
    type: "object",
    properties: {
      file_path: { type: "string", description: "The absolute path to the article file" },
      old_string: { type: "string", description: "The text to be replaced (must be an exact match)" },
      new_string: { type: "string", description: "The new text content" },
      replace_all: { type: "boolean", description: "Whether to replace all occurrences" }
    },
    required: ["file_path", "old_string", "new_string"]
  }

  async execute(input: EditArticleInput): Promise<ToolResult> {
    const absolutePath = path.resolve(input.file_path)
    
    // Forced read validation (replicating the core mechanism of Claude Code)
    const fileState = this.fileStateTracker.getFileState(absolutePath)
    if (!fileState) {
      throw new Error(`File ${absolutePath} has not been read. Please use the read_article tool to read the file content first.`)
    }
    
    // Verify that the file content has not been modified externally
    const currentContent = await fs.readFile(absolutePath, 'utf-8')
    if (this.calculateHash(currentContent) !== fileState.hash) {
      throw new Error(`File ${absolutePath} has been modified externally. Please use the read_article tool to read the latest content again.`)
    }
    
    try {
      // Perform string replacement (fully replicating the Edit tool logic)
      let newContent: string
      
      if (input.replace_all) {
        // Replace all
        newContent = currentContent.replaceAll(input.old_string, input.new_string)
        const replaceCount = (currentContent.match(new RegExp(escapeRegex(input.old_string), 'g')) || []).length
        
        if (replaceCount === 0) {
          throw new Error(`Text to be replaced not found: "${input.old_string}"`)
        }
        
      } else {
        // Single replacement - ensure uniqueness
        const matches = currentContent.split(input.old_string)
        if (matches.length === 1) {
          throw new Error(`Text to be replaced not found: "${input.old_string}"`)
        }
        if (matches.length > 2) {
          throw new Error(`Text is not unique, found ${matches.length - 1} matches. Please provide more context or use the replace_all parameter.`)
        }
        
        newContent = currentContent.replace(input.old_string, input.new_string)
      }
      
      // Atomic write
      const tempPath = `${absolutePath}.tmp.${process.pid}`
      await fs.writeFile(tempPath, newContent, 'utf-8')
      await fs.rename(tempPath, absolutePath)
      
      // Update file state tracking
      this.fileStateTracker.updateFileState(absolutePath, newContent)
      
      // Calculate change statistics
      const changeStats = this.calculateChangeStats(currentContent, newContent)
      
      return {
        success: true,
        data: {
          path: absolutePath,
          changes: {
            old_length: currentContent.length,
            new_length: newContent.length,
            diff: changeStats.diff,
            lines_changed: changeStats.linesChanged
          },
          statistics: this.calculateArticleStats(newContent)
        }
      }
      
    } catch (error) {
      return {
        success: false,
        error: `Failed to edit article: ${error.message}`
      }
    }
  }

  // File state tracker (replicating the readFileState mechanism)
  private class FileStateTracker {
    private fileStates: Map<string, FileState> = new Map()
    
    trackFileRead(filePath: string, content: string): FileState {
      const state: FileState = {
        path: filePath,
        hash: this.calculateHash(content),
        content,
        lastRead: Date.now(),
        lineCount: content.split('\n').length
      }
      
      this.fileStates.set(filePath, state)
      return state
    }
    
    getFileState(filePath: string): FileState | undefined {
      return this.fileStates.get(filePath)
    }
    
    updateFileState(filePath: string, newContent: string): void {
      const state = this.fileStates.get(filePath)
      if (state) {
        state.content = newContent
        state.hash = this.calculateHash(newContent)
        state.lastModified = Date.now()
        state.lineCount = newContent.split('\n').length
      }
    }
  }
}
```

## ✍️ Advanced Writing Tools

### OutlineGenerator Tool

```typescript
// src/tools/writing/outline-generator.ts
export class OutlineGeneratorTool implements WritingTool {
  name = "generate_outline"
  description = "AI-generates an article outline"
  securityLevel = "ai-powered"
  
  async execute(input: OutlineGeneratorInput): Promise<ToolResult> {
    const anthropic = new Anthropic({ apiKey: process.env.ANTHROPIC_API_KEY })
    
    // Build the prompt
    const prompt = this.buildOutlinePrompt(input)
    
    const response = await anthropic.messages.create({
      model: input.model || "claude-3-opus-20240229",
      max_tokens: 4000,
      temperature: 0.7,
      messages: [{
        role: "user",
        content: prompt
      }]
    })
    
    // Parse the generated outline
    const outline = this.parseOutlineFromAI(response.content[0].text)
    
    // Generate file suggestions
    const fileSuggestions = this.generateFileSuggestions(input.topic, outline)
    
    return {
      success: true,
      data: {
        outline,
        fileSuggestions,
        metadata: {
          model: input.model || "claude-3-opus-20240229",
          tokensUsed: response.usage.output_tokens,
          generatedAt: Date.now(),
          topic: input.topic
        }
      }
    }
  }

  private buildOutlinePrompt(input: OutlineGeneratorInput): string {
    return `Please generate a detailed article outline for the topic "${input.topic}".

Article parameters:
- Target audience: ${input.audience || "Technical readers"}
- Article type: ${input.articleType || "Technical article"}
- Target length: ${input.targetLength || 2000} words
- Writing style: ${input.style || "Technical"}
- Special requirements: ${input.requirements || "None"}

Please generate an outline with the following structure:

## 1. Title Suggestions
Provide 3 title options from different angles that are attractive and accurate.

## 2. Article Structure
### Introduction (10-15%)
- Problem introduction or background
- Value of the article and benefits to the reader
- Estimated word count: XXX words

### Body (70-80%)
#### Chapter 1: [Chapter Title]
- Core argument:
- Key content:
- Supporting materials:
- Estimated word count: XXX words

#### Chapter 2: [Chapter Title]
- Core argument:
- Key content:
- Supporting materials:
- Estimated word count: XXX words

[Continue with other chapters...]

### Conclusion (10-15%)
- Summary of key points
- Deeper thoughts or outlook
- Call to action (if applicable)
- Estimated word count: XXX words

## 3. Writing Suggestions
- Suggestions for key information sources
- Potential difficulties and solutions
- Design for reader interaction points
- SEO optimization suggestions

## 4. Related Materials
- Necessary background materials
- Authoritative reference sources
- Statistical data requirements

Please ensure the outline is logical, clear, and easy to execute.`
  }

  private parseOutlineFromAI(text: string): OutlineStructure {
    // Intelligently parse the AI-generated outline structure
    const sections: OutlineSection[] = []
    const lines = text.split('\n')
    
    let currentSection: OutlineSection | null = null
    let currentSubsection: OutlineSubsection | null = null
    
    for (const line of lines) {
      const trimmed = line.trim()
      
      if (trimmed.match(/^##\s+\d+\.\s+(.+)/)) {
        // Main chapter
        const title = trimmed.match(/^##\s+\d+\.\s+(.+)/)?.[1] || ''
        currentSection = {
          level: 1,
          title,
          subsections: [],
          estimatedWords: 0
        }
        sections.push(currentSection)
        
      } else if (trimmed.match(/^###\s+(.+)/)) {
        // Sub-chapter
        const title = trimmed.match(/^###\s+(.+)/)?.[1] || ''
        currentSubsection = {
          level: 2,
          title,
          content: [],
          estimatedWords: 0
        }
        if (currentSection) {
          currentSection.subsections.push(currentSubsection)
        }
        
      } else if (trimmed.match(/^####\s+(.+)/)) {
        // Sub-sub-chapter
        const title = trimmed.match(/^####\s+(.+)/)?.[1] || ''
        if (currentSubsection) {
          currentSubsection.content.push({
            type: 'subsection',
            title,
            description: ''
          })
        }
        
      } else if (trimmed.startsWith('- ') && currentSubsection) {
        // List of points
        const content = trimmed.slice(2)
        if (content.includes('：') || content.includes(':')) {
          const [label, description] = content.split(/[:：]/)
          currentSubsection.content.push({
            type: 'point',
            label: label.trim(),
            description: description.trim()
          })
        } else {
          currentSubsection.content.push({
            type: 'bullet',
            text: content
          })
        }
        
      } else if (trimmed.match(/Estimated word count: (\d+) words/)) {
        // Extract word count estimate
        const wordCount = parseInt(trimmed.match(/Estimated word count: (\d+) words/)?.[1] || '0')
        if (currentSubsection) {
          currentSubsection.estimatedWords = wordCount
        } else if (currentSection) {
          currentSection.estimatedWords = wordCount
        }
      }
    }
    
    return {
      title: this.extractMainTitle(text),
      sections,
      totalEstimatedWords: sections.reduce((sum, section) => 
        sum + section.estimatedWords + 
        section.subsections.reduce((subSum, sub) => subSum + sub.estimatedWords, 0), 0
      ),
      structure: this.analyzeOutlineStructure(sections)
    }
  }
}
```

### ContentRewriter Tool

```typescript
// src/tools/writing/content-rewriter.ts 
export class ContentRewriterTool implements WritingTool {
  name = "rewrite_content"
  description = "Intelligently rewrites and optimizes article content"
  securityLevel = "ai-powered"
  
  async execute(input: ContentRewriterInput): Promise<ToolResult> {
    const anthropic = new Anthropic({ apiKey: process.env.ANTHROPIC_API_KEY })
    
    // Analyze original content features
    const originalStats = this.analyzeContent(input.originalContent)
    
    // Build rewrite prompt
    const prompt = this.buildRewritePrompt(input, originalStats)
    
    const response = await anthropic.messages.create({
      model: "claude-3-opus-20240229",
      max_tokens: Math.max(4000, input.originalContent.length * 1.2),
      temperature: input.creativity || 0.7,
      messages: [{
        role: "user",
        content: prompt
      }]
    })
    
    const rewrittenContent = response.content[0].text
    
    // Analyze rewrite result
    const rewrittenStats = this.analyzeContent(rewrittenContent)
    const qualityMetrics = this.assessRewriteQuality(
      input.originalContent, 
      rewrittenContent, 
      input.targetStyle
    )
    
    return {
      success: true,
      data: {
        originalContent: input.originalContent,
        rewrittenContent,
        changes: this.generateChangeReport(input.originalContent, rewrittenContent),
        statistics: {
          original: originalStats,
          rewritten: rewrittenStats
        },
        quality: qualityMetrics,
        suggestions: this.generateImprovementSuggestions(rewrittenContent)
      }
    }
  }

  private buildRewritePrompt(input: ContentRewriterInput, stats: ContentStats): string {
    const styleGuides = {
      "Casual": "Use simple vocabulary, avoid jargon, use everyday analogies, and have a friendly and natural tone.",
      "Formal": "Use formal and precise language, complete sentence structures, clear logical flow, and avoid colloquialisms.",
      "Technical": "Use accurate technical terms, maintain strict logic, and provide sufficient technical details and examples.",
      "Academic": "Follow academic writing standards, use precise terminology, provide rigorous arguments, and cite sources properly.",
      "Marketing": "Be persuasive, highlight value and benefits, use powerful language, and include a call to action.",
      "Storytelling": "Use a narrative style with vivid plots, rich scene descriptions, and an immersive feel."
    }

    const targetGuide = styleGuides[input.targetStyle] || input.targetStyle

    return `Please rewrite the following content in a "${input.targetStyle}" style:

🎯 Target Style Description: ${targetGuide}

📊 Original Content Analysis:
- Word count: ${stats.wordCount} words
- Paragraphs: ${stats.paragraphCount}
- Technical terms: ${stats.technicalTerms}
- Readability level: ${stats.readabilityLevel}

📝 Original Content:
${input.originalContent}

🔄 Rewrite Requirements:
1. **Style Conversion**: Strictly adhere to the characteristics of the "${input.targetStyle}" style.
2. **Information Preservation**: Retain all core information and ideas, without omitting important content.
3. **Logical Optimization**: Optimize paragraph structure and logical flow to improve readability.
4. **Length Control**: ${input.targetLength ? `Keep it around ${input.targetLength} words` : 'Maintain a similar length to the original'}.
5. **Quality Improvement**: Enhance expression, eliminate redundancy, and improve impact.

${input.preserveStructure ? '📋 **Structure Preservation**: Maintain the original chapter structure and heading levels.' : ''}
${input.audienceLevel ? `👥 **Audience Level**: Target readers at a ${input.audienceLevel} level.` : ''}

Please provide the complete rewritten result, ensuring it meets the target style requirements.`
  }

  private assessRewriteQuality(
    original: string, 
    rewritten: string, 
    targetStyle: string
  ): QualityMetrics {
    return {
      styleConsistency: this.calculateStyleConsistency(rewritten, targetStyle),
      readabilityImprovement: this.calculateReadabilityImprovement(original, rewritten),
      informationRetention: this.calculateInformationRetention(original, rewritten),
      languageQuality: this.assessLanguageQuality(rewritten),
      overallScore: 0 // Will be calculated based on the above metrics
    }
  }
}
```

## 🔍 Research Tool System

### WebSearch Tool (Replicates WebSearch)

```typescript
// src/tools/research/web-search.ts
export class WebSearchTool implements WritingTool {
  name = "web_search"
  description = "Searches the web for information on a given topic"
  securityLevel = "network"
  
  private searchEngines = {
    google: new GoogleSearchEngine(),
    bing: new BingSearchEngine(),
    baidu: new BaiduSearchEngine()
  }
  
  async execute(input: WebSearchInput): Promise<ToolResult> {
    const engine = this.searchEngines[input.engine || 'google']
    if (!engine) {
      throw new Error(`Unsupported search engine: ${input.engine}`)
    }
    
    try {
      const results = await engine.search({
        query: input.query,
        limit: input.limit || 10,
        language: input.language || 'en',
        region: input.region,
        timeRange: input.timeRange
      })
      
      // Filter and sort results
      const filteredResults = this.filterSearchResults(results, input.filters)
      const rankedResults = this.rankByRelevance(filteredResults, input.query)
      
      // Extract key information
      const insights = this.extractSearchInsights(rankedResults)
      
      return {
        success: true,
        data: {
          query: input.query,
          engine: input.engine || 'google',
          results: rankedResults,
          insights,
          metadata: {
            totalResults: results.total,
            searchTime: results.duration,
            language: input.language || 'en'
          }
        }
      }
      
    } catch (error) {
      return {
        success: false,
        error: `Search failed: ${error.message}`
      }
    }
  }

  private extractSearchInsights(results: SearchResult[]): SearchInsights {
    const domains = new Map<string, number>()
    const publishTimes: Date[] = []
    const topics = new Set<string>()
    
    for (const result of results) {
      // Tally domain distribution
      const domain = new URL(result.url).hostname
      domains.set(domain, (domains.get(domain) || 0) + 1)
      
      // Collect publication times
      if (result.publishTime) {
        publishTimes.push(new Date(result.publishTime))
      }
      
      // Extract topic keywords
      const resultTopics = this.extractTopics(result.title + ' ' + result.description)
      resultTopics.forEach(topic => topics.add(topic))
    }
    
    return {
      topDomains: Array.from(domains.entries())
        .sort(([,a], [,b]) => b - a)
        .slice(0, 5)
        .map(([domain, count]) => ({ domain, count })),
      timeDistribution: this.analyzeTimeDistribution(publishTimes),
      relatedTopics: Array.from(topics).slice(0, 10),
      sourceQuality: this.assessSourceQuality(results)
    }
  }
}
```

### FactChecker Tool

```typescript
// src/tools/research/fact-checker.ts
export class FactCheckerTool implements WritingTool {
  name = "fact_checker" 
  description = "Fact-checks and verifies information"
  securityLevel = "ai-powered"
  
  async execute(input: FactCheckerInput): Promise<ToolResult> {
    const statements = this.extractStatements(input.content)
    const factChecks: FactCheckResult[] = []
    
    // Concurrently check multiple statements
    const checkPromises = statements.map(async (statement, index) => {
      const result = await this.checkSingleStatement(statement, input.sources)
      return { index, result }
    })
    
    const results = await Promise.allSettled(checkPromises)
    
    for (const promiseResult of results) {
      if (promiseResult.status === 'fulfilled') {
        const { index, result } = promiseResult.value
        factChecks[index] = result
      } else {
        factChecks.push({
          statement: statements[factChecks.length] || 'Unknown statement',
          confidence: 0,
          status: 'error',
          error: promiseResult.reason.message
        })
      }
    }
    
    // Generate an overall report
    const overallAssessment = this.generateOverallAssessment(factChecks)
    
    return {
      success: true,
      data: {
        originalContent: input.content,
        factChecks,
        overallAssessment,
        recommendations: this.generateRecommendations(factChecks),
        checkedAt: new Date().toISOString()
      }
    }
  }

  private async checkSingleStatement(
    statement: string, 
    sources?: string[]
  ): Promise<FactCheckResult> {
    const anthropic = new Anthropic({ apiKey: process.env.ANTHROPIC_API_KEY })
    
    const prompt = `Please fact-check the following statement:

Statement: ${statement}

Please provide:
1. Factual assessment (Correct/Partially Correct/Incorrect/Unverifiable)
2. Confidence level (a value between 0 and 1)
3. Evidence to support or refute the statement
4. Authoritative sources
5. Correction suggestions (if necessary)

${sources ? `\nReference sources:\n${sources.join('\n')}` : ''}

Please return the result in JSON format.`

    const response = await anthropic.messages.create({
      model: "claude-3-opus-20240229",
      max_tokens: 2000,
      messages: [{ role: "user", content: prompt }]
    })
    
    try {
      const result = JSON.parse(response.content[0].text)
      return {
        statement,
        confidence: result.confidence,
        status: result.status,
        evidence: result.evidence,
        sources: result.sources,
        correction: result.correction
      }
    } catch (error) {
      return {
        statement,
        confidence: 0.5,
        status: 'unknown',
        error: 'Failed to parse AI response'
      }
    }
  }
}
```

## 📤 Publishing Tool System

### WeChatConverter Tool

```typescript
// src/tools/publish/wechat-converter.ts
export class WeChatConverterTool implements WritingTool {
  name = "convert_wechat"
  description = "Converts to WeChat Official Account format"
  securityLevel = "format-conversion"
  
  async execute(input: WeChatConverterInput): Promise<ToolResult> {
    try {
      // Parse Markdown content
      const parsed = this.parseMarkdown(input.markdown)
      
      // Apply WeChat styling
      const styled = this.applyWeChatStyling(parsed, input.theme || 'default')
      
      // Generate HTML
      const html = this.generateWeChatHTML(styled)
      
      // Optimize images
      const optimizedImages = await this.optimizeImagesForWeChat(styled.images)
      
      return {
        success: true,
        data: {
          html,
          css: this.generateWeChatCSS(input.theme),
          images: optimizedImages,
          preview: input.generatePreview ? await this.generatePreview(html) : null,
          metadata: {
            theme: input.theme || 'default',
            wordCount: this.countWords(input.markdown),
            convertedAt: new Date().toISOString()
          }
        }
      }
      
    } catch (error) {
      return {
        success: false,
        error: `WeChat format conversion failed: ${error.message}`
      }
    }
  }

  private applyWeChatStyling(content: ParsedContent, theme: string): StyledContent {
    const themes = {
      'default': {
        primaryColor: '#1AAD19',
        fontFamily: '-apple-system, BlinkMacSystemFont, "Helvetica Neue", sans-serif',
        fontSize: '16px',
        lineHeight: '1.6'
      },
      'tech': {
        primaryColor: '#2196F3', 
        fontFamily: 'Monaco, "Cascadia Code", monospace',
        fontSize: '15px',
        lineHeight: '1.7'
      },
      'minimal': {
        primaryColor: '#333',
        fontFamily: 'system-ui, sans-serif', 
        fontSize: '16px',
        lineHeight: '1.8'
      }
    }
    
    const themeConfig = themes[theme] || themes.default
    
    return {
      ...content,
      style: themeConfig,
      headings: content.headings.map(h => ({
        ...h,
        style: this.getHeadingStyle(h.level, themeConfig)
      })),
      paragraphs: content.paragraphs.map(p => ({
        ...p,
        style: this.getParagraphStyle(themeConfig)
      })),
      codeBlocks: content.codeBlocks.map(cb => ({
        ...cb,
        style: this.getCodeBlockStyle(themeConfig)
      }))
    }
  }
}
```

---

*All tools are fully based on the Claude Code MH1 tool engine architecture to ensure architectural consistency.*
