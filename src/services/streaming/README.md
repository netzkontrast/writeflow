# WriteFlow Streaming Adapter System

Provides unified multi-vendor AI model streaming response handling for WriteFlow.

## 🎯 Core Features

- **Multi-Vendor Support**: OpenAI, Anthropic Claude, DeepSeek, Google Gemini, Zhipu AI (GLM), Kimi/Moonshot, Qwen/Tongyi Qianwen
- **Protocol Agnostic**: A unified interface hides the SSE protocol differences of various vendors.
- **Automatic Detection**: Automatically selects an adapter based on the model name or response format.
- **High Performance**: Supports both incremental parsing and buffered parsing strategies.
- **Enterprise-Grade Features**: Error handling, retry mechanisms, configuration management, status monitoring.
- **Fully Typed**: Complete TypeScript type definitions.

## 🚀 Quick Start

### Basic Usage

```typescript
import { getStreamingService } from './index.js'

// Get the streaming service
const streamingService = getStreamingService()

// Listen for streaming data chunks
streamingService.on('chunk', (response) => {
  console.log(response.content)        // Text content
  console.log(response.reasoning)      // Reasoning content (e.g., from DeepSeek)
  console.log(response.usage)          // Token usage statistics
  console.log(response.done)           // Whether the stream is finished
})

// Listen for the complete event
streamingService.on('complete', (response) => {
  console.log('✅ Stream complete!')
  console.log(`📊 Usage stats: ${response.usage?.inputTokens}→${response.usage?.outputTokens} tokens`)
  console.log(`💰 Cost: $${response.cost?.toFixed(6)}`)
})

// Start a streaming request
await streamingService.startStream({
  prompt: "Please explain what TypeScript is in a concise way.",
  model: 'claude-3-sonnet',
  maxTokens: 200,
  temperature: 0.7
})
```

### Compatibility Usage

```typescript
import { getWriteFlowAIService } from '../ai/WriteFlowAIService.js'

const aiService = getWriteFlowAIService()

// Existing code, just add stream: true
const response = await aiService.processRequest({
  prompt: "Write an introduction to AI",
  stream: true,  // Enable streaming
  model: 'deepseek-chat',
  maxTokens: 150
})

console.log(response.content)
```

### Convenience Function

```typescript
import { askAIStreamComplete } from './index.js'

// A streaming request that waits for the complete response
const response = await askAIStreamComplete("What is a microservices architecture?", {
  model: 'deepseek-reasoner',
  maxTokens: 150,
  temperature: 0.3
})

console.log(response.content)
console.log(`Token usage: ${response.usage.inputTokens}→${response.usage.outputTokens}`)
```

## 📚 Supported Models

### OpenAI Models
```typescript
const openai = getStreamingService()
await openai.startStream({ 
  prompt: "task",
  model: 'gpt-4o' 
})
```

### Anthropic Claude Models
```typescript
const claude = getStreamingService()
await claude.startStream({ 
  prompt: "task",
  model: 'claude-3-sonnet' 
})
```

### DeepSeek Models (with reasoning content support)
```typescript
const deepseek = getStreamingService()
deepseek.on('chunk', (response) => {
  if (response.reasoning) {
    console.log('💭 Reasoning:', response.reasoning)
  }
  console.log('📝 Answer:', response.content)
})

await deepseek.startStream({ 
  prompt: "task",
  model: 'deepseek-reasoner' 
})
```

### Google Gemini Models
```typescript
const gemini = getStreamingService()
await gemini.startStream({ 
  prompt: "task",
  model: 'gemini-pro' 
})
```

### Zhipu AI (GLM) Models
```typescript
const zhipu = getStreamingService()
await zhipu.startStream({ 
  prompt: "task",
  model: 'glm-4.5' 
})
```

### Kimi/Moonshot Models (long context)
```typescript
const kimi = getStreamingService()
await kimi.startStream({ 
  prompt: "task",
  model: 'moonshot-v1-128k'  // Supports 128k context
})
```

### Qwen/Tongyi Qianwen Models
```typescript
const qwen = getStreamingService()
await qwen.startStream({ 
  prompt: "task",
  model: 'qwen-turbo' 
})
```

## 🔧 Advanced Configuration

### Error Handling and Retries

```typescript
import { getStreamingService } from './index.js'

const streamingService = getStreamingService({
  maxRetries: 5,           // Maximum number of retries
  retryDelay: 2000,        // Retry delay (exponential backoff)
  timeout: 120000,         // Timeout (2 minutes)
  bufferSize: 16384,       // Buffer size
  enableReconnect: true    // Enable automatic reconnection
})

streamingService.on('error', (error) => {
  console.error('Streaming error:', error.message)
  
  // Check if it will be retried automatically
  const status = streamingService.getStreamingStatus()
  if (status.retryCount < 5) {
    console.log(`Will attempt retry #${status.retryCount + 1}`)
  }
})
```

### Status Monitoring

```typescript
// Monitor streaming status
const status = streamingService.getStreamingStatus()
console.log('Is streaming:', status.isStreaming)
console.log('Retry count:', status.retryCount)
console.log('Configuration:', status.config)

// Dynamically update configuration
streamingService.updateConfig({
  timeout: 180000,  // Increase timeout to 3 minutes
  maxRetries: 10    // Increase max retries
})
```

### Performance Optimization

```typescript
// Use a larger buffer for large text scenarios
const service = getStreamingService({
  bufferSize: 32768,        // 32KB buffer
  parseStrategy: 'buffered' // Buffered parsing strategy
})

// Use incremental parsing for real-time scenarios
const realtimeService = getStreamingService({
  bufferSize: 4096,           // 4KB buffer
  parseStrategy: 'incremental' // Incremental parsing strategy
})
```

## 📈 Protocol Formats

### OpenAI Format
```
data: {"choices":[{"delta":{"content":"Hello"}}]}
data: {"choices":[{"delta":{"content":" World"}}]}
data: [DONE]
```

### Anthropic Claude Format
```
event: content_block_delta
data: {"type":"content_block_delta","delta":{"type":"text_delta","text":"Hello"}}

event: message_stop
data: {"type":"message_stop"}
```

### DeepSeek Format (extends OpenAI)
```
data: {"choices":[{"delta":{"reasoning_content":"Let me think..."}}]}
data: {"choices":[{"delta":{"content":"Hello World"}}]}
data: [DONE]
```

### Gemini Format
```json
{"candidates":[{"content":{"parts":[{"text":"Hello World"}]},"finishReason":"STOP"}]}
```

## 🧪 Testing and Examples

```bash
# Run functional tests
npx tsx src/services/streaming/test.ts

# Run usage examples
npx tsx src/services/streaming/examples.ts
```

## 📁 File Structure

```
src/services/streaming/
├── StreamAdapter.ts          # Base adapter interface
├── OpenAIStreamAdapter.ts    # OpenAI protocol adapter
├── ClaudeStreamAdapter.ts    # Anthropic protocol adapter
├── DeepSeekStreamAdapter.ts  # DeepSeek protocol adapter
├── GeminiStreamAdapter.ts    # Gemini protocol adapter
├── ZhipuStreamAdapter.ts     # Zhipu AI protocol adapter
├── UniversalOpenAIAdapter.ts # Universal OpenAI compatible adapter
├── StreamAdapterFactory.ts   # Factory pattern and auto-detection
├── StreamingService.ts       # Unified streaming service
├── StreamingAIService.ts     # Compatibility service wrapper
├── examples.ts               # Usage examples
├── test.ts                   # Functional tests
└── index.ts                  # Module exports
```

## ✅ Feature Matrix

| Vendor        | Basic Streaming | Reasoning Content | Usage Stats | Tool Calls | Vision Input | Long Context |
|---------------|-----------------|-------------------|-------------|------------|--------------|--------------|
| OpenAI        | ✅              | ❌                | ❌          | 🔄 Planned | 🔄 Planned   | ❌           |
| Anthropic     | ✅              | ❌                | ✅          | 🔄 Planned | 🔄 Planned   | ✅           |
| DeepSeek      | ✅              | ✅                | ✅          | 🔄 Planned | ❌           | ❌           |
| Gemini        | ✅              | ❌                | ❌          | 🔄 Planned | 🔄 Planned   | ❌           |
| Zhipu AI (GLM)| ✅              | ❌                | ❌          | ✅         | ✅           | ✅           |
| Kimi/Moonshot | ✅              | ❌                | ❌          | ✅         | ❌           | ✅           |
| Qwen/Tongyi   | ✅              | ❌                | ❌          | ✅         | ❌           | ✅           |

## 🔄 Extending for New Vendors

To add support for a new vendor:

1. Inherit from the `StreamAdapter` base class.
2. Implement the `parseStream()` and `isStreamEnd()` methods.
3. Add detection logic in `StreamAdapterFactory`.
4. Update `modelCapabilities.ts` to add model configuration.

```typescript
export class NewProviderStreamAdapter extends StreamAdapter {
  parseStream(data: string): StreamChunk[] {
    // Implement protocol parsing logic
  }
  
  isStreamEnd(data: string): boolean {
    // Implement end-of-stream detection logic
  }
}
```

## 📄 License

This project uses the same license as WriteFlow.
