# 🚀 WriteFlow Quick Start Guide

Welcome to the WriteFlow AI Writing Assistant! This guide will help you get started quickly.

## 💡 Basic Concepts

WriteFlow completely replicates the interactive model of Claude Code, providing a simple yet powerful command-line writing experience.

### Three Input Modes

WriteFlow supports three special input prefixes:

#### 1. `/` Slash Command Mode
```bash
/outline AI technology development trends
/rewrite casual ./article.md
/research Quantum computing applications
/help                    # View all available commands
/status                  # View system status
/exit                    # Exit the program
```

#### 2. `!` Bash Execution Mode
```bash
!ls -la                  # List files
!git status             # Check git status
!npm install            # Install dependencies
!pwd                    # Show the current directory
```

#### 3. `#` Note-Taking Mode
```bash
#Today's writing plan: finish the AI technology article
#Remember: need to add more examples
#Todo: check the citation format
```

#### 4. Free-Form Conversation Mode
Enter questions or content directly, without a prefix:
```
Help me analyze the development trends of artificial intelligence.
Is there a problem with this code?
Please give me some writing advice.
```

## 🔄 Mode Switching System

WriteFlow offers four working modes, which can be cycled through using **Shift+Tab**:

### 1. Default Mode
- Executes all commands normally.
- Supports file reading/writing and network access.
- Suitable for daily writing tasks.

### 2. Plan Mode 📋
- Only executes read-only commands.
- Used for safe analysis and planning.
- Will not modify any files.

### 3. Accept Edits Mode ✅
- Automatically accepts all editing suggestions.
- Speeds up the writing process.
- Suitable for rapid iteration.

### 4. Bypass Permissions Mode 🔓
- Bypasses security restrictions.
- An advanced user mode.
- Please use with caution.

The current mode is displayed below the input box, e.g., `📋 plan mode on (shift+tab to cycle)`.

## ⚡ Common Slash Commands

### Core Writing Commands

| Command    | Description          | Example                     |
| ---------- | -------------------- | --------------------------- |
| `/outline` | Generate an article outline | `/outline AI technology development` |
| `/rewrite` | Rewrite content      | `/rewrite casual ./tech.md`   |
| `/research`| Web research         | `/research Quantum computing applications` |
| `/style`   | Adjust style         | `/style academic ./article.md` |
| `/grammar` | Check grammar        | `/grammar ./draft.md`       |

### File Operation Commands

| Command   | Description       | Example                   |
| --------- | ----------------- | ------------------------- |
| `/read`   | Read a file       | `/read ./article.md`      |
| `/edit`   | Edit a file       | `/edit ./draft.md`        |
| `/search` | Search for content | `/search "keyword" ./docs/` |
| `/save`   | Save content      | `/save ./output.md`       |

### Publishing Tool Commands

| Command    | Description         | Example                       |
| ---------- | ------------------- | ----------------------------- |
| `/publish` | Publish and convert | `/publish wechat ./article.md` |
| `/format`  | Format content      | `/format markdown ./text.txt`   |

### System Management Commands

| Command     | Description        | Example                    |
| ----------- | ------------------ | -------------------------- |
| `/help`     | Show help          | `/help` or `/help outline` |
| `/status`   | System status      | `/status`                  |
| `/clear`    | Clear the screen   | `/clear`                   |
| `/model`    | Switch the model   | `/model deepseek`          |
| `/settings` | View settings      | `/settings`                |
| `/exit`     | Exit the program   | `/exit`                    |

## 🎨 User Interface Description

WriteFlow features a minimalist design. Here is an explanation of the interface elements:

```
┌─────────────────────────────────────┐
│ 🎯 WriteFlow - Default Mode         │  ← Title bar and mode display
├─────────────────────────────────────┤
│                                     │
│ 🚀 WriteFlow v2.2.4                 │  ← Startup message
│                                     │
│ > Your question or command...▋      │  ← Input box
│                                     │
│ 📋 plan mode on (shift+tab to cycle)│  ← Mode prompt (only in non-default modes)
│                                     │
│ ✓ Ready | 7 messages | 22:50:46      │  ← Status bar
└─────────────────────────────────────┘
```

## ⌨️ Keyboard Shortcuts

- **Enter**: Submit input.
- **Shift+Tab**: Switch working modes.
- **Ctrl+C**: Clear the current input.
- **Ctrl+L**: Clear the screen.
- **Backspace**: Delete a character.

## 🔧 Advanced T-ps

### 1. Chaining Commands
```bash
/research AI development && /outline && /rewrite casual
```

### 2. File Pipelining
```bash
/read ./draft.md | /grammar | /save ./final.md
```

### 3. Bulk Processing
```bash
/edit ./docs/*.md --operation=grammar-check
```

### 4. Using Templates
```bash
/outline --template=tech-article --topic=blockchain
```

## 🆘 Getting Help

- **Command Help**: `/help [command-name]` - View detailed instructions for a specific command.
- **System Status**: `/status` - View the current configuration and system information.
- **Official Documentation**: Check the detailed documentation in the `docs/` directory.
- **Feedback**: [GitHub Issues](https://github.com/wordflowlab/writeflow/issues).

## 🎉 Start Writing

Now that you have mastered the basic usage of WriteFlow, you can start your AI-assisted writing journey!

```bash
# Start WriteFlow
writeflow

# Start your first article
> /outline My first technical article
```

Happy writing! ✨
