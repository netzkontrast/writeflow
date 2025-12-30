# AI Provider Configuration Guide

WriteFlow is a powerful AI writing assistant that supports four major AI providers. This guide will detail how to configure and use these providers.

## 🚀 Supported AI Providers

| Provider    | Model        | Context Length | Special Features      | Billing Currency |
| ----------- | ------------ | -------------- | --------------------- | ---------------- |
| **Anthropic** | Claude 4.1   | 200K           | Superior reasoning capabilities | USD              |
| **Deepseek**  | v3.1         | 128K           | Specialized reasoning model | -                |
| **Qwen**    | Qwen3        | 30K            | Optimized for Chinese | CNY              |
| **Zhipu GLM** | 4.5          | 128K           | Multimodal support    | CNY              |

## 📦 Installing WriteFlow

### Global Installation (Recommended)

```bash
npm install -g writeflow
```

### Installation from Source

```bash
git clone https://github.com/wordflowlab/writeflow.git
cd writeflow
npm install
npm run build
npm install -g .
```

**System Requirements**: Node.js >= 18.0.0

## ⚙️ Configuring Each AI Provider

### 1. Anthropic Claude

#### Getting an API Key

1. Visit the [Anthropic Console](https://console.anthropic.com/).
2. Register/log in to your account.
3. Go to the **API Keys** page.
4. Click **Create Key** to create a new key.
5. Copy the generated API key (it starts with `sk-`).

#### Environment Variable Configuration

```bash
# Set Anthropic as the default provider
export API_PROVIDER=anthropic
export ANTHROPIC_API_KEY=sk-your-anthropic-api-key-here

# Optional: custom API endpoint (proxy/forwarding)
export API_BASE_URL=https://your-proxy-url.com/v1
```

#### Supported Models

```bash
# Claude 4.1 series (latest)
claude-opus-4-1-20250805          # Default model
claude-opus-4-1-20250805-thinking

# Claude 4 series
claude-opus-4-20250514
claude-sonnet-4-20250514

# Claude 3.5 series
claude-3-5-sonnet-20241022
claude-3-5-haiku-20241022
```

#### Usage Example

```bash
writeflow
writeflow> Hello, please give me an overview of the history of AI development.
writeflow> /outline Trends in deep learning technology
```

---

### 2. Deepseek v3.1

#### Getting an API Key

1. Visit the [Deepseek Open Platform](https://platform.deepseek.com/).
2. Register/log in to your account.
3. Go to the **API Keys** management page.
4. Create a new API key.
5. Copy the generated key.

#### Environment Variable Configuration

```bash
# Set Deepseek as the provider
export API_PROVIDER=deepseek
export DEEPSEEK_API_KEY=sk-your-deepseek-api-key-here
export API_BASE_URL=https://api.deepseek.com

# Optional: specify a model
export AI_MODEL=deepseek-chat
```

#### Supported Models

```bash
# Deepseek v3.1 series
deepseek-chat      # General-purpose chat model (default)
deepseek-reasoner  # Specialized reasoning model

# Compatible aliases
deepseek-v3-chat
deepseek-v3-reasoner
```

#### Usage Example

```bash
writeflow
writeflow> Please analyze the technical principles of quantum computing.
writeflow> /research The latest advancements in deep reinforcement learning
```

---

### 3. Qwen3

#### Getting an API Key

1. Visit the [Alibaba Cloud Console](https://dashscope.console.aliyun.com/).
2. Register/log in to your Alibaba Cloud account.
3. Activate **DashScope Model Service**.
4. Go to the **API Key Management** page.
5. Create and copy an API key.

#### Environment Variable Configuration

```bash
# Set Qwen3 as the provider
export API_PROVIDER=qwen3
export QWEN_API_KEY=sk-your-qwen-api-key-here
export API_BASE_URL=https://dashscope.aliyuncs.com/compatible-mode/v1

# Optional: specify a model
export AI_MODEL=qwen-max
```

#### Supported Models

```bash
# Qwen3 commercial version
qwen-max           # Most powerful version (default)
qwen-plus          # High-cost-performance version
qwen-turbo         # Fast-response version

# Qwen2.5 open-source version
qwen2.5-72b-instruct
qwen2.5-32b-instruct
qwen2.5-14b-instruct
qwen2.5-7b-instruct
```

#### Pricing Information (CNY)

| Model      | Input Price          | Output Price         |
| ---------- | -------------------- | -------------------- |
| qwen-max   | ¥0.02/1k tokens      | ¥0.06/1k tokens      |
| qwen-plus  | ¥0.004/1k tokens     | ¥0.012/1k tokens     |
| qwen-turbo | ¥0.0015/1k tokens    | ¥0.002/1k tokens     |

#### Usage Example

```bash
writeflow
writeflow> Help me write an outline for an article about the development of ancient Chinese technology.
writeflow> /rewrite academic ./my-article.md
```

---

### 4. Zhipu GLM-4.5

#### Getting an API Key

1. Visit the [Zhipu Open Platform](https://open.bigmodel.cn/).
2. Register/log in to your account.
3. Complete the real-name verification.
4. Go to the **API Keys** management page.
5. Create and copy an API key.

#### Environment Variable Configuration

```bash
# Set GLM-4.5 as the provider
export API_PROVIDER=glm4.5
export GLM_API_KEY=your-glm-api-key-here
export API_BASE_URL=https://open.bigmodel.cn/api/paas/v4

# Optional: specify a model
export AI_MODEL=glm-4.5
```

#### Supported Models

```bash
# GLM-4 series
glm-4              # Standard version
glm-4.5            # Upgraded version (default)
glm-4-air          # Lightweight version
glm-4-flash        # Ultra-fast version
glm-4v             # Multimodal version
```

#### Model Features

| Model       | Context | Special Features | Response Speed |
| ----------- | ------- | ---------------- | -------------- |
| glm-4.5     | 128K    | Superior reasoning | Standard       |
| glm-4-air   | 128K    | Fast response    | Fast           |
| glm-4-flash | 128K    | Millisecond response | Ultra-fast     |
| glm-4v      | 2K      | Multimodal       | Standard       |

#### Pricing Information (CNY)

| Model       | Input Price          | Output Price         |
| ----------- | -------------------- | -------------------- |
| glm-4       | ¥0.1/1k tokens       | ¥0.1/1k tokens       |
| glm-4.5     | ¥0.05/1k tokens      | ¥0.05/1k tokens      |
| glm-4-air   | ¥0.001/1k tokens     | ¥0.001/1k tokens     |
| glm-4-flash | ¥0.0001/1k tokens    | ¥0.0001/1k tokens    |

#### Usage Example

```bash
writeflow
writeflow> Please analyze the development of artificial intelligence from a philosophical perspective.
writeflow> /outline A cross-disciplinary study of cognitive science and artificial intelligence
```

## 🎯 User Guide

### Starting WriteFlow

```bash
# Start interactive mode
writeflow

# Check the current configuration
writeflow status
```

### Switching Providers

You can switch between different AI providers by modifying the environment variables:

```bash
# Switch to Deepseek
export API_PROVIDER=deepseek
export DEEPSEEK_API_KEY=your-key

# Switch to Qwen3
export API_PROVIDER=qwen3
export QWEN_API_KEY=your-key

# Switch to GLM-4.5
export API_PROVIDER=glm4.5
export GLM_API_KEY=your-key

# Restart WriteFlow for the changes to take effect
writeflow
```

### Basic Command Examples

```bash
# Free-form conversation
writeflow> Hello, I'd like to understand the basic concepts of machine learning.

# Generate an article outline
writeflow> /outline The application of artificial intelligence in the medical field

# Rewrite the style of the content
writeflow> /rewrite casual "Deep learning is a machine learning method"

# Topic research
writeflow> /research The latest breakthroughs in quantum computing

# View help
writeflow> /help
```

### Advanced Configuration Options

#### Model Parameter Adjustment

```bash
# Temporarily adjust parameters in a conversation
writeflow> Please adjust the temperature parameter to 0.9, then create a science fiction story for me.

# Set via environment variables
export AI_MODEL=qwen-max
export TEMPERATURE=0.7
export MAX_TOKENS=4000
```

#### Proxy/Forwarding Configuration

```bash
# Use a proxy service
export API_BASE_URL=https://your-proxy.com/v1

# Example of a cloud-based forwarder (supports multiple APIs)
export API_BASE_URL=https://yunwu.apifox.cn/api-264600675
export API_PROVIDER=anthropic  # or another provider
```

## 🔧 Troubleshooting

### Common Errors and Solutions

#### 1. API Key Verification Failure

**Error Message**: `401 Authentication Fails` or `Invalid API Key`

**Solution**:
- Check if the API key was copied correctly.
- Confirm that the key has not expired.
- Verify that the environment variable name is correct.

```bash
# Check environment variables
echo $ANTHROPIC_API_KEY
echo $API_PROVIDER
```

#### 2. API Connection Timeout

**Error Message**: `Connection timeout` or `Network error`

**Solution**:
- Check your network connection.
- Try using a proxy service.
- Confirm that the `API_BASE_URL` is set correctly.

```bash
# Test the connection
curl -H "Authorization: Bearer $ANTHROPIC_API_KEY" \
     https://api.anthropic.com/v1/messages
```

#### 3. Model Not Supported

**Error Message**: `Model not found` or `Unsupported model`

**Solution**:
- Check the spelling of the model name.
- Confirm that the provider supports that model.
- Check the model list in this document.

#### 4. Quota Exceeded

**Error Message**: `Rate limit exceeded` or `Quota exceeded`

**Solution**:
- Wait for the quota to reset.
- Upgrade your account plan.
- Distribute your requests over time.

## 💡 Best Practices

### Provider Selection Recommendations

#### Selection Based on Use Case

- **Academic Research**: Anthropic Claude (strong reasoning capabilities).
- **Chinese Content**: Qwen3 (optimized for Chinese).
- **Cost-Sensitive**: Zhipu GLM-4-air (low price).
- **Complex Reasoning**: Deepseek v3.1 (specialized for reasoning).
- **Multimodal**: Zhipu GLM-4v (supports images).

#### Cost Optimization Strategies

1. **Tiered Usage**: Use cheaper models for simple tasks and high-end models for complex ones.
2. **Batch Processing**: Handle multiple questions in a single request.
3. **Cache Results**: Avoid repeating the same requests.
4. **Parameter Tuning**: Appropriately reduce `max_tokens` to control costs.

### Performance Comparison

| Scenario        | Recommended Provider | Reason                                   |
| --------------- | -------------------- | ---------------------------------------- |
| English Writing | Anthropic            | Highest language quality                 |
| Chinese Writing | Qwen                 | Good understanding of Chinese context    |
| Code Generation | Deepseek             | Strong code reasoning capabilities       |
| Fast Response   | GLM-4-flash          | Millisecond-level response               |
| Long Document Processing | Claude 4.1           | 200K extra-long context                  |

### Environment Configuration Template

Create a `.env` file to manage your configuration:

```bash
# .env file example
API_PROVIDER=anthropic
ANTHROPIC_API_KEY=sk-your-key-here
API_BASE_URL=https://api.anthropic.com

# Optional configuration
AI_MODEL=claude-opus-4-1-20250805
TEMPERATURE=0.7
MAX_TOKENS=4000
SYSTEM_PROMPT=You are the WriteFlow AI writing assistant.
```

Then, load it before starting:

```bash
source .env
writeflow
```

## 🚀 Get Started

Now that you know how to configure each AI provider, choose one that suits your needs:

1. **Quick Start**: Use Anthropic Claude (most comprehensive features).
2. **Chinese Users**: Qwen3 is recommended.
3. **Cost Control**: Choose Zhipu GLM-4-air.
4. **Reasoning Tasks**: Try Deepseek v3.1.

After configuration, run `writeflow` to start your AI writing journey!

---

**WriteFlow** - Making AI writing more professional 🚀

If you have any questions, please visit [GitHub Issues](https://github.com/wordflowlab/writeflow/issues) for help.
