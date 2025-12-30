# 📖 WriteFlow User Guides

Welcome to WriteFlow! Here you will find all the user guides and tutorials to help you quickly master the powerful features of WriteFlow.

## 🚀 Getting Started

### [Quick Start Guide](./quick-start.md)
Get started with WriteFlow in 5 minutes, and learn about its basic features and usage.

### [AI Provider Setup](./ai-providers-setup.md)
Configure different AI model providers, including Claude, DeepSeek, Qwen, GLM, and more.

### [Deployment Guide](./deployment-guide.md)
A detailed installation and deployment guide, including environmental requirements, configuration steps, and troubleshooting.

## 📝 Core Features

### [Slash Command Guide](./slash-commands.md)
A detailed introduction to all of WriteFlow's slash commands, including features for writing, research, publishing, and more.

### [Slidev PPT User Manual](./slidev-ppt-guide.md) 🆕
**New Feature!** Use AI to create professional technical presentations, with support for intelligent conversion from Markdown to PPT.

## 🎯 Feature Categories

### Writing Features
- **Outline Generation** (`/outline`) - AI generates an article outline.
- **Content Rewriting** (`/rewrite`) - Intelligently rewrites the style of an article.
- **Grammar Check** (`/grammar`) - Checks for grammatical errors.
- **Style Adjustment** (`/style`) - Adjusts the writing style.

### PPT Creation Features
- **Create a Presentation** (`/slide create`) - Generate a complete PPT based on a topic.
- **Article Conversion** (`/slide convert`) - Convert a Markdown article into a presentation.
- **Generate an Outline** (`/slide outline`) - Generate a presentation outline.
- **Optimize a Presentation** (`/slide optimize`) - Optimize an existing presentation.

### Research Features
- **Web Search** (`/research`) - In-depth topic research.
- **Fact-Checking** (`/fact-check`) - Verify the accuracy of information.
- **Citation Management** (`/cite`) - Manage bibliographic references.

### Publishing Features
- **Format Conversion** (`/publish`) - Convert to various platform formats.
- **WeChat Formatting** (`/wechat`) - Format for WeChat Official Accounts.
- **HTML Generation** (`/html`) - Generate in HTML format.

## 📚 In-Depth Learning

### System Architecture
Learn about WriteFlow's technical architecture and design philosophy:
- [System Architecture Design](../architecture/system-architecture.md)
- [Technical Implementation Details](../architecture/technical-implementation.md)
- [Writing Toolset](../architecture/writing-tools.md)

### Feature Documents
Get an in-depth understanding of the detailed design of each feature:
- [Slidev PPT Feature Requirements Document](../features/slidev-ppt-feature.md)
- More feature documents are coming soon...

## 🔧 Configuration and Customization

### Agent Configuration
Learn how to configure and customize Agents:
- Agent configuration file location: `.writeflow/agents/`
- See the [Agent Configuration Instructions](../../.writeflow/agents/README.md)

### Template System
Customize templates to meet your individual needs:
- Template directory: `src/templates/`
- Supports Handlebars syntax.

## 💡 Usage Tips

### Tips for Improving Efficiency

1. **Use Aliases**: Most commands have short aliases, such as `/ol` for `/outline`.
2. **Bulk Processing**: Supports batch conversion of multiple files.
3. **Command Combination**: You can combine multiple commands to complete complex tasks.
4. **Template Reuse**: Create custom templates to improve efficiency.

### Common Use Cases

| Scenario               | Recommended Command Combination             | Description                                       |
| ---------------------- | ------------------------------------------- | ------------------------------------------------- |
| Technical Blog Writing | `/outline` → `/research` → `/rewrite`       | First, create an outline, then research, and finally, polish. |
| Presentation Preparation | `/slide outline` → `/slide create` → `/slide optimize` | From an outline to a complete presentation.       |
| Article to Presentation | `/slide convert` → `/slide optimize`        | Quickly convert an article into a PPT.            |
| Multi-Platform Publishing | `/publish` → `/wechat`                      | Write once, publish everywhere, adapted for different platforms. |

## 🆘 Getting Help

### Common Problems
- See the "Common Problems" section in each guide.
- Visit [GitHub Issues](https://github.com/writeflow/writeflow/issues).

### Community Support
- [GitHub Discussions](https://github.com/writeflow/writeflow/discussions)
- [Official Documentation Site](https://writeflow.app/docs)

### Feedback and Suggestions
We welcome your feedback and suggestions:
- Submit an Issue: [GitHub Issues](https://github.com/writeflow/writeflow/issues/new)
- Contribute Code: [Contributing Guide](../../CONTRIBUTING.md)

## 📈 Changelog

### v2.9.3 (2025-01-03)
- 🎉 Added the Slidev PPT creation feature.
- 📚 Improved user documentation and guides.
- 🔧 Optimized the Agent loading mechanism.
- 🐛 Fixed known issues.

### v2.9.2
- Enhanced the onboarding process.
- Fixed issues with the configuration system.

### v2.9.1
- Initial version release.

---

## Quick Navigation

### 📖 User Guides
- [Quick Start](./quick-start.md)
- [AI Provider Setup](./ai-providers-setup.md)
- [Deployment Guide](./deployment-guide.md)
- [Slash Commands](./slash-commands.md)
- [Slidev PPT Guide](./slidev-ppt-guide.md) 🔥

### 🏗️ Architecture Documents
- [System Architecture](../architecture/system-architecture.md)
- [Technical Implementation](../architecture/technical-implementation.md)
- [Writing Tools](../architecture/writing-tools.md)

### 🎯 Feature Documents
- [Slidev Feature](../features/slidev-ppt-feature.md)

### 🔍 Other Resources
- [Project README](../../README.md)
- [Contributing Guide](../../CONTRIBUTING.md)
- [Changelog](../../CHANGELOG.md)

---

*Last updated: 2025-01-03*
*WriteFlow Team*
