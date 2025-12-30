# 📚 WriteFlow Slidev Intelligent PPT System - Complete User Manual

> From idea to presentation, get it done with a single command! WriteFlow 2.11+ introduces a brand new intelligent PPT generation system.

## 🆕 v2.11+ New Feature Highlights

- 🧠 **Intelligent Generation**: AI-driven content creation based on the complete Slidev knowledge base.
- 🚀 **One-Step Process**: Input a topic to generate a presentation, no complex subcommands needed.
- 📋 **Intelligent Guidance**: Detailed operational steps and file naming suggestions.
- ⚡ **System Streamlining**: Reduced from 13 commands to 5 core commands to improve user experience.

## 🚀 Quick Start

### ⚡ One-Minute Experience (Recommended)

The simplest way - generate directly with the main command:

```bash
# Directly input a topic to intelligently generate a presentation
/slide "The principles of reactivity in Vue 3"

# A business report topic
/slide "Q4 Business Growth Report"

# An academic presentation topic
/slide "The latest advancements in machine learning"
```

**It's that simple!** 🎉 The system will automatically:

1. 🧠 Recognize that you have entered a topic, not a subcommand.
2. ⚙️ Intelligently generate professional PPT content.
3. 📋 Provide detailed guidance for saving and previewing.
4. 🚀 Support a one-click preview experience.

### 🎯 Traditional Method (Step-by-Step Operation)

If you prefer more control, you can perform the steps individually:

#### Step 1: Intelligently Generate Content

```bash
# Generate intelligent PPT content (will display detailed operational guidance)
/slide-intelligent "The application of deep learning in computer vision" --style=academic --duration=40 --audience=researchers
```

#### Step 2: Save and Preview

Follow the guidance provided after generation:

1. Copy the generated Markdown content.
2. Save it with the recommended file name (e.g., `The-application-of-deep-learning-in-computer-vision-slides.md`).
3. Execute the preview command:

   ```bash
   /slide-preview The-application-of-deep-learning-in-computer-vision-slides.md
   ```

## 📖 Complete Command List - Streamlined to 5 Core Commands

### 🎯 Main Command

| Command  | Function                                   | Example                  |
| -------- | ------------------------------------------ | ------------------------ |
| `/slide` | An intelligent main command that supports direct topic generation | `/slide "React Hooks"` |

### 🧠 Intelligent Generation Command

| Command              | Function                                 | Example                                          |
| -------------------- | ---------------------------------------- | ------------------------------------------------ |
| `/slide-intelligent` | Intelligently generate a PPT (displays operational guidance) | `/slide-intelligent "React Hooks" --style=technical` |

### 🚀 Preview Command

| Command            | Function                         | Example                  |
| ------------------ | -------------------------------- | ------------------------ |
| `/slide-preview`   | Preview a presentation           | `/slide-preview slides.md` |
| `/slide-preview --list` | View available presentations     | `/slide-preview -l`      |
| `/slide-preview --recent` | View recent presentation history | `/slide-preview -r`      |

### 🛠️ Creation and Conversion Commands

| Command          | Function                                    | Example                                       |
| ---------------- | ------------------------------------------- | --------------------------------------------- |
| `/slide-create`  | Create a presentation (supports dependency checks) | `/slide-create "My Topic" --theme=seriph`       |
| `/slide-convert` | Convert an article into a presentation (supports dependency checks) | `/slide-convert article.md --style=technical` |

## 💡 Parameter Descriptions

### Core Parameters

- `--style`: The style of the presentation
  - `academic` - Academic research style
  - `business` - Business report style
  - `technical` - Technical presentation style
  - `creative` - Creative showcase style
  - `professional` - Professional presentation style (default)

- `--duration`: The duration of the presentation (in minutes), which influences the planning of the number of slides.
- `--audience`: The target audience, which influences the depth of the content.
  - `researchers` - Researchers
  - `developers` - Developers
  - `investors` - Investors
  - `students` - Students
  - `mixed` - A mixed audience (default)

- `--theme`: The Slidev theme
  - `seriph` - An elegant style (default)
  - `default` - The standard style
  - `apple-basic` - An Apple-like style
  - `carbon` - A carbon fiber style

### Preview Parameters

- `--port`: Specify the preview port (default is 3030).
- `--no-open`: Do not automatically open the browser.
- `--list` / `-l`: List available files.
- `--recent` / `-r`: Show recent records.
- `--help` / `-h`: Show help.

## ✨ Usage Examples

### 🏫 Academic Presentation

```bash
# An academic research presentation - using the main command
/slide "A study on the application of machine learning in medical diagnosis"

# Or use the intelligent generation command for detailed guidance
/slide-intelligent "A study on the application of machine learning in medical diagnosis" \
  --style=academic \
  --duration=40 \
  --audience=researchers \
  --theme=seriph
```

**Characteristics**: A rigorous academic format, a data-driven content structure, and support for professional charts and formulas.

### 💼 Business Report

```bash
# A business report presentation - using the main command
/slide "Q4 Business Growth Report"

# Or use the intelligent generation command for detailed guidance
/slide-intelligent "Q4 Business Growth Report" \
  --style=business \
  --duration=25 \
  --audience=executives \
  --theme=apple-basic
```

**Characteristics**: A visual design for business intelligence, highlighting of KPI data, and a decision-oriented content organization.

### 👨‍💻 Technical Presentation

```bash
# A technical presentation - using the main command
/slide "An in-depth analysis of the concurrency features in React 18"

# Or use the intelligent generation command for detailed guidance
/slide-intelligent "An in-depth analysis of the concurrency features in React 18" \
  --style=technical \
  --duration=50 \
  --audience=developers
```

**Characteristics**: Rich with code examples, practical-oriented content, and in-depth explanations of technical details.

### 🎨 Creative Showcase

```bash
# A creative showcase - using the main command
/slide "A methodology for building a design system"

# Or use the intelligent generation command for detailed guidance
/slide-intelligent "A methodology for building a design system" \
  --style=creative \
  --duration=60 \
  --theme=carbon
```

**Characteristics**: A visually innovative design, a highly interactive presentation, and guidance for creative thinking.

## 🎯 Detailed Operational Guidance

### Steps After Intelligent Generation

After using `/slide-intelligent`, the system will display:

```
🎉 Presentation generated successfully!

## 📋 Next Steps

### Step 1: Save the File 📁
Please save the Markdown content above as a file:
**Recommended file name**: `your-topic-slides.md`

### Step 2: Preview Immediately 🚀
After saving the file, copy and execute the following command:

```bash
/slide-preview your-topic-slides.md
```

### Alternative
If you encounter any issues, you can also use:
- `/slide-preview` (to automatically find the file)
- `npx @slidev/cli your-topic-slides.md --open`
```

### Indication of a Successful Preview

Seeing the following information means it has started successfully:

```
●■▲ Slidev v52.x.x
public slide show > http://localhost:3030/
presenter mode    > http://localhost:3030/presenter/
slides overview   > http://localhost:3030/overview/
```

## ❓ Common Questions

### Q: How do I choose the right presentation style?

**A**: Choose based on the occasion and the audience:
- **Academic Conference**: `--style=academic`
- **Company Report**: `--style=business`
- **Technical Presentation**: `--style=technical`
- **Creative Showcase**: `--style=creative`
- **General Presentation**: `--style=professional` (default)

### Q: What should I do if the preview fails to start?

**A**: The system will automatically check for dependencies and provide a solution:

1. Check if the Slidev CLI is installed: `npm install -g @slidev/cli`
2. Use the alternative command: `npx @slidev/cli your-file.md --open`
3. Check the error message for a specific solution.

### Q: How can I modify the generated PPT?

**A**: There are several ways:
- Directly edit the generated `.md` file (Slidev will update the preview in real-time).
- Use the `/slide-intelligent` command to regenerate the optimized content.
- Refer to the [official Slidev documentation](https://sli.dev) to learn about advanced features.

### Q: Where is the file saved?

**A**:
- Using `/slide "topic"`: The system will generate the content and provide guidance for saving.
- Using `/slide-intelligent`: You need to manually save it to the current directory.
- To see recent files: `/slide-preview --recent`

### Q: How can I see all available presentations?

**A**: Use `/slide-preview --list` or `/slide-preview -l`.

## 🔧 Advanced Features

### Bulk Operations

```bash
# View all Markdown files in the current directory
/slide-preview --list

# Preview the most recently used presentation
/slide-preview --recent

# Preview on a specific port (to avoid conflicts)
/slide-preview slides.md --port=3031
```

### File Management

```bash
# Automatically find and preview (Priority: slides.md > presentation.md > deck.md)
/slide-preview

# Do not automatically open the browser
/slide-preview slides.md --no-open
```

### Keyboard Shortcuts

Available shortcuts during preview:

- **Arrow keys/Spacebar**: Navigate through slides
- **f**: Fullscreen mode
- **o**: Presentation outline
- **e**: Edit mode
- **g**: Go to a specific page
- **Ctrl+C**: Stop the service

## 🏆 Best Practices

### 1. Tips for Describing the Topic

- **Be Specific and Clear**: "Vue 3 Composition API" is better than "Vue.js".
- **Include Keywords**: Mention important concepts and technology stacks.
- **Provide Context**: "For beginners..." or "Enterprise-level...".

### 2. Suggestions for Parameter Configuration

- **`--duration`**: Plan for 1-2 minutes per page.
- **`--style`**: Choose a style appropriate for the occasion.
- **`--audience`**: Accurately describe the characteristics of the audience.
- **`--theme`**: `seriph` is suitable for formal occasions, while `default` is good for technical presentations.

### 3. Tips for Content Optimization

- **Structured Description**: "Include background, methods, results, and conclusion".
- **Clarify the Focus**: "Focus on the algorithm principles and performance comparison".
- **Practicality**: "Need code examples and practical case studies".

### 4. Recommended Workflow

A recommended workflow for creating a PPT:

1. **Planning Phase**: Determine the topic, audience, and duration.
2. **Generation Phase**: Use `/slide "topic"` or `/slide-intelligent` to generate the content.
3. **Saving Phase**: Save as a `.md` file according to the system's guidance.
4. **Previewing Phase**: Use `/slide-preview` to see the result.
5. **Optimization Phase**: Edit the file or regenerate as needed.
6. **Presentation Phase**: Use fullscreen mode and keyboard shortcuts.

## 📈 Version Update History

### v2.11.0 (Latest) - System Streamlining and Optimization
- ✅ **Intelligent Main Command**: `/slide` supports direct topic input, automatically recognizing and generating the presentation.
- ✅ **Major System Streamlining**: Reduced from 13 commands to 5 core commands to improve user experience.
- ✅ **Optimized Dependency Checks**: The `create` and `convert` commands support automatic dependency checks and installation guidance.
- ✅ **Enhanced User Interaction**: All core commands support a three-option user interaction mode.
- ✅ **Comprehensive Documentation Update**: Reorganized the user guide to highlight the simplified usage.

### v2.10.1
- ✅ Integrated the complete Slidev knowledge base.
- ✅ Implemented the intelligent generation command `/slide-intelligent`.
- ✅ Supports a variety of presentation styles and theme selections.
- ✅ Created detailed documentation and user guides.

## 🤝 Getting Help

- **Command Help**: Use the `--help` parameter to view help for a specific command.
- **Error Troubleshooting**: The system will provide detailed error messages and solutions.
- **Feature Suggestions**: Propose improvements via GitHub Issues.
- **Technical Support**: Refer to the project documentation or contact the development team.

---

**The WriteFlow Slidev Intelligent PPT System turns every idea into a brilliant presentation!** 🚀
