# 📊 WriteFlow Intelligent Research Feature User Guide

## 🎯 Feature Introduction

WriteFlow's `/deep-research` command helps you quickly generate professional-grade research reports, similar to professional research tools like DeepResearch. This feature automatically collects information, analyzes data, and generates a structured report, significantly improving your research efficiency.

## 🚀 Quick Start

### Basic Usage

```bash
# The simplest way to use it
/deep-research "The current state of artificial intelligence development"

# The system will automatically:
# 1. Search for relevant information
# 2. Analyze and organize the content
# 3. Generate a professional research report
```

### Advanced Options

```bash
# In-depth research, academic format, 20 information sources
/deep-research "Blockchain technology development trends" --depth=deep --format=academic --sources=20

# Business analysis format
/deep-research "New energy vehicle market" --format=business --sources=15

# Quick research (for when time is tight)
/deep-research "5G technology applications" --depth=quick
```

## 📋 Parameter Details

### Research Depth (`--depth`)
- **Quick**: 15-30 minutes, basic information collection.
- **Standard**: 1-2 hours, comprehensive analysis (default).
- **Deep**: 2-4 hours, detailed research.

### Report Format (`--format`)
- **Academic**: Academic research format, including a detailed methodology.
- **Business**: Business analysis format, highlighting market value.
- **Technical**: Technical analysis format, delving into technical details.
- **Comprehensive**: A comprehensive analysis (default).

### Other Options
- **`--sources=<number>`**: The number of information sources, default is 15.
- **`--time=<range>`**: The time range, such as "last year".
- **`--lang=<language>`**: The language preference, such as "English and Chinese".

## 💡 Use Case Examples

### 1. Technical Research
```bash
/deep-research "The technical principles and applications of ChatGPT" --format=technical --depth=deep
```
**Suitable for**: Technical analysis, product design, technology selection.

### 2. Market Analysis
```bash
/deep-research "Market prospects for electric vehicles" --format=business --sources=20
```
**Suitable for**: Investment decisions, business plans, market entry strategies.

### 3. Academic Research
```bash
/deep-research "Ethical issues in artificial intelligence" --format=academic --depth=deep
```
**Suitable for**: Thesis writing, academic research, theoretical analysis.

### 4. Quick Overview
```bash
/deep-research "The concept of the metaverse" --depth=quick
```
**Suitable for**: Quick learning, background understanding, concept clarification.

## 📊 Output Report Structure

Each research report includes the following standard structure:

```markdown
## 📊 [Topic] In-Depth Research Report

### 📋 Executive Summary
- Core findings and key insights
- Important data and statistics
- Main conclusions and actionable recommendations

### 🎯 Research Background and Significance
- Problem definition and research scope
- Explanation of the value and significance of the research

### 📈 Comprehensive Analysis of the Current Situation
- Review of the development history
- Current state of development
- Key influencing factors

### 🔍 In-Depth Analysis and Insights
- Analysis of technological developments
- Market opportunities and challenges
- Typical application cases

### 🚀 Future Development Forecast
- Short-term outlook (1-2 years)
- Mid-term trends (3-5 years)
- Long-term vision (5-10 years)

### 💡 Conclusions and Recommendations
- Core conclusions
- Differentiated viewpoints
- Targeted actionable recommendations

### 📚 Information Sources and References
- A list of authoritative information sources
- Citation links and access times
```

## 🔍 How It Works

### Phase 1: Intelligent Information Collection (20%)
- Multi-source search: search engines, academic databases, news media.
- Keyword expansion: AI intelligently generates related search terms.
- Authority assessment: Automatically assesses the credibility of information sources.

### Phase 2: In-Depth Content Analysis (50%)
- Viewpoint extraction: Identifies core viewpoints and different stances.
- Data validation: Cross-verifies key data.
- Trend analysis: Identifies development trends and changes.

### Phase 3: Report Generation (30%)
- Structure organization: Organizes content in a logical structure.
- Format optimization: Generates a professionally formatted report.
- Citation management: Automatically generates a bibliography.

## 📚 Practical Tips

### 1. Suggestions for Choosing a Topic
- **Be Specific and Clear**: "The application of artificial intelligence in medical diagnosis" > "Artificial intelligence".
- **Keep the Scope Moderate**: Avoid topics that are too broad or too narrow.
- **Timeliness**: Choose popular topics with ongoing developments.

### 2. Suggestions for Parameter Combinations
```bash
# Investment analysis
/deep-research "Investment opportunities in the new energy industry" --format=business --depth=deep --sources=25

# Technology selection
/deep-research "The pros and cons of microservices architecture" --format=technical --depth=standard

# Quick learning
/deep-research "An explanation of the Web3 concept" --depth=quick --format=comprehensive
```

### 3. Optimizing the Results
- **Multiple Runs**: For complex topics, you can conduct research multiple times from different angles.
- **Parameter Adjustment**: Adjust the depth and format parameters according to your needs.
- **Post-Processing**: Further optimize the report using commands like `/rewrite` and `/polish`.

## ⚠️ Important Notes

### Information Accuracy
- The system automatically verifies information, but it is still recommended to manually confirm key data.
- For highly specialized content, it is advisable to consult with domain experts.
- Pay attention to the timeliness of the information, especially in rapidly changing fields.

### Usage Recommendations
- Use when you have a good network connection to ensure the quality of information collection.
- For complex topics, it is recommended to choose the "deep" mode.
- You can first use the "quick" mode for a general understanding, and then use the "deep" mode for a detailed analysis.

### Copyright and Citations
- The report will automatically cite the sources of information.
- Please be mindful of copyright issues for commercial use.
- For academic use, please cite the references according to the proper standards.

## 🔧 Troubleshooting

### Common Problems

**Q: What if the report content is not deep enough?**
A: Try using the `--depth=deep` parameter, or increase the number of information sources with `--sources=30`.

**Q: What if the information in a certain field is inaccurate?**
A: You can use the `/check` command for fact-checking, or manually verify key information.

**Q: What if the generation time is too long?**
A: Choose `--depth=quick` or reduce the number of information sources.

**Q: What if I need a report in a specific format?**
A: Use the `--format=` parameter to select a suitable format, or adjust it later with `/rewrite`.

### Getting Help
```bash
# View command help
/help deep-research

# View all commands
/help

# System status check
/status
```

## 🎉 Get Started

Now, try generating your first research report:

```bash
/deep-research "any topic you are interested in"
```

WriteFlow will become your most capable research assistant!

---

*Need more help? Check out the [complete feature documentation](../features/intelligent-research-system.md) or submit an issue in the project.*
