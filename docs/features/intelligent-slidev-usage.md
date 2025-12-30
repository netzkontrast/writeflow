# Slidev Intelligent PPT Generation System - User Guide

## Overview

WriteFlow has now integrated the complete Slidev knowledge base to provide an intelligent PPT generation system. With the `/slide-intelligent` command, you can generate professional, beautiful, and fully functional Slidev presentations based on any topic and requirement.

## Core Features

### 🧠 Intelligent Content Understanding
- Automatically analyzes user intent and content type.
- Intelligently plans the structure and logic of the presentation.
- Optimizes content depth based on audience characteristics.

### 🎨 Professional Visual Design
- Modern design style and color schemes.
- Hierarchical information architecture.
- Professional typography with a mix of Chinese and English.

### ⚡ Application of Advanced Features
- Makes full use of all Slidev features.
- Animation effects like `v-click` and `v-motion`.
- Built-in components and layout system.
- Code highlighting and chart integration.

## Quick Start

### Basic Usage
```bash
/slide-intelligent "The Application of Deep Learning in Computer Vision"
```

### Advanced Usage
```bash
/slide-intelligent "2024 Product Launch" --style=business --duration=45 --audience=investors --theme=seriph
```

## Command Details

### Main Command Syntax
```
/slide-intelligent <description or topic> [options]
```

### Available Options

| Option     | Description         | Default Value | Example                               |
| ---------- | ------------------- | ------------- | ------------------------------------- |
| `--style`    | The style of the presentation | `professional`  | `academic`, `business`, `technical`, `creative` |
| `--theme`    | The Slidev theme      | `seriph`        | `default`, `apple-basic`, `bricks`, `carbon` |
| `--duration` | The duration of the presentation (minutes) | `20`          | `15`, `30`, `45`, `60`                    |
| `--audience` | The target audience   | `mixed`         | `researchers`, `developers`, `investors`, `students` |
| `--language` | The language        | `english`       | `english`, `chinese`                  |

### Command Aliases
- `/slide-ai`
- `/slide-smart`
- `/intelligent-ppt`

## Usage Examples

### Academic Presentation
```bash
/slide-intelligent "A Comparative Study of Machine Learning Algorithms" --style=academic --duration=30 --audience=researchers
```
**Generated Effect**: A rigorous academic presentation containing algorithm principles, experimental data, and comparative analysis.

### Business Report
```bash
/slide-intelligent "Q4 Business Growth Report" --style=business --duration=25 --audience=executives
```
**Generated Effect**: A data-driven, visually prominent business report format.

### Technical Sharing
```bash
/slide-intelligent "Best Practices for React Hooks" --style=technical --duration=40 --audience=developers
```
**Generated Effect**: A practical, code-example-rich technical presentation.

### Creative Showcase
```bash
/slide-intelligent "Design Thinking Workshop" --style=creative --theme=apple-basic --duration=60
```
**Generated Effect**: A highly interactive and visually innovative creative showcase.

## Characteristics of the Generated Results

### 📋 Content Structure
- **Cover Page**: An eye-catching title and core value proposition.
- **Table of Contents Page**: A clear presentation outline.
- **Content Pages**: 3-7 core chapters with a clear logical flow.
- **Data Pages**: Presentation of charts and key metrics.
- **Summary Page**: A recap of key points and a call to action.

### 🎯 Technical Implementation
- **Layout Diversity**: `cover`, `center`, `two-cols`, `image-right`, etc.
- **Smooth Animations**: `v-click` sequences to control the pace of information display.
- **Rich Components**: Full use of built-in components to optimize effects.
- **Professional Styling**: A perfect combination of UnoCSS + custom CSS.

### 📊 Quality Assurance
- All user content is fully preserved.
- Slidev syntax is 100% correct.
- Content depth is appropriate for the target audience.
- Supports keyboard navigation and responsive design.

## Best Practices

### Tips for Describing the Topic
1. **Be Specific and Clear**: "Vue 3 Composition API" is better than "Vue.js".
2. **Include Keywords**: Mention important concepts and technology stacks.
3. **Provide Context**: "For beginners..." or "Enterprise-level...".

### Suggestions for Option Configuration
1. **`--duration`**: Plan for 1-2 minutes per page.
2. **`--style`**: Choose a style appropriate for the occasion.
3. **`--audience`**: Accurately describe the characteristics of the audience.
4. **`--theme`**: `seriph` is suitable for formal occasions, while `default` is good for technical sharing.

### Tips for Content Optimization
1. **Structured Description**: "Include background, methods, results, and conclusion".
2. **Clarify the Focus**: "Focus on the algorithm principles and performance comparison".
3. **Practicality**: "Need code examples and practical case studies".

## Comparison with Other Commands

| Command            | Use Case                | Generation Quality | Customization Level | Technical Features |
| ------------------ | ----------------------- | ------------------ | ------------------- | ------------------ |
| `/slide-intelligent` | Any topic, high requirements | ⭐⭐⭐⭐⭐           | ⭐⭐⭐⭐⭐            | ⭐⭐⭐⭐⭐           |
| `/slide-quick`     | Quick prototyping, known topic | ⭐⭐⭐             | ⭐⭐                  | ⭐⭐⭐             |
| `/slide-create`    | Basic creation          | ⭐⭐⭐             | ⭐⭐⭐                | ⭐⭐⭐             |

## Troubleshooting

### Common Problems

**Q: What should I do if I get an "Agent loading failed" prompt?**
A: The system will automatically use a fallback generation logic, so the functionality will not be affected.

**Q: The generated content does not meet my expectations?**
A: Try to describe your requirements in more detail, or adjust the `style`/`audience` parameters.

**Q: How can I modify the generated PPT?**
A: You can use the `/slide-optimize` command to optimize an existing PPT, or manually edit the Markdown file.

**Q: What languages are supported?**
A: The default is English, but you can switch to Chinese with `--language=chinese`.

### Performance Optimization
- For complex topics, it is recommended to generate them in stages.
- For large presentations, you can first generate an outline and then gradually refine it.
- Make full use of existing templates and components.

## Example Output

### Input
```bash
/slide-intelligent "The Ethical Challenges of Artificial Intelligence" --style=academic --duration=35
```

### Structure of the Generated Presentation
```markdown
---
theme: seriph
title: The Ethical Challenges of Artificial Intelligence
info: Exploring the ethical issues and solutions in the development of AI technology
class: text-center
highlighter: shiki
transition: slide-left
mdc: true
---

# The Ethical Challenges of Artificial Intelligence
## Balancing Technological Advancement and Humanistic Care

<div v-click class="pt-12">
  <span class="text-6xl">⚖️</span>
  <div class="text-xl opacity-75 mt-4">
    Finding a Moral Compass in the AI Era
  </div>
</div>

---
layout: center
---

# Table of Contents
<Toc maxDepth="2" columns="2" />

---

# 🤖 The Current State of AI Development

<v-clicks>

## Technological Breakthroughs
- **Deep Learning**: Significant progress in computer vision and language understanding.
- **Large-Scale Models**: The emergence of general AI such as GPT and DALL-E.
- **Automation**: Widespread application in manufacturing, finance, healthcare, and other fields.

## Application Areas
- 🏥 **Medical Diagnosis**: Image analysis, drug discovery.
- 🚗 **Autonomous Driving**: Improvements in traffic safety and efficiency.
- 💼 **Business Intelligence**: Decision support, user experience optimization.

</v-clicks>

---
...more pages
```

## Changelog

### v2.10.2 (Latest)
- ✅ Integrated the complete Slidev knowledge base.
- ✅ Added a new intelligent generation command.
- ✅ Supports a variety of presentation styles.
- ✅ Optimized user experience and error handling.

## Next Steps

- [ ] Add more theme templates.
- [ ] Support multilingual localization.
- [ ] Integrate image and chart generation.
- [ ] Add collaboration and sharing features.

---

Start creating your professional presentations now with `/slide-intelligent`!
