# CLAUDE.md

## Claude Code's Nine Honors and Nine Shames

- It is a shame to guess at interfaces, an honor to inquire carefully.
- It is a shame to execute vaguely, an honor to seek confirmation.
- It is a shame to imagine business needs, an honor to reuse what exists.
- It is a shame to create interfaces, an honor to test proactively.
- It is a shame to skip validation, an honor to have human confirmation.
- It is a shame to break the architecture, an honor to follow the specification.
- It is a shame to pretend to understand, an honor to be honestly ignorant.
- It is a shame to modify blindly, an honor to refactor cautiously.
- It is a shame to gild the lily, an honor to implement as needed.

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Output in English

# WriteFlow AI Writing Assistant

WriteFlow is an AI writing assistant based on the core architecture of Claude Code, a CLI tool designed specifically for technical writers.

## 🎯 Core Philosophy

WriteFlow applies the "specification-driven" philosophy of the GitHub Spec Kit to the writing process. Through a complete workflow of `specify → plan → task → write`, it solves the problem of traditional "ambient writing" and achieves precise, specification-driven writing.

For testing reference: `WRITEFLOW_DEBUG=true echo "Please analyze this project" | npm run dev`

# Orphaned Code Cleanup Guide

## Orphaned Code Detection Tools

WriteFlow has integrated the following dead code detection tools:

### Available Commands
- `npm run dead-code:detect` - Use ts-prune to detect unused exports
- `npm run dead-code:analyze` - Use knip for a comprehensive analysis of unused code
- `npm run dead-code:clean` - Detect dead code and run lint to fix it
- `npm run pre-commit-check` - Run a full pre-commit check (type check + dead code detection + lint)

### Prevention Strategies
1. **Git hooks automation**: Automatically run dead code detection before each commit
2. **TypeScript strict mode**: Enable `noUnusedLocals` and `noUnusedParameters`
3. **Regular cleanup**: It is recommended to run `npm run dead-code:analyze` once a week

### Safe Cleanup Process
1. Run `npm run dead-code:detect` to get a detailed report
2. Manually review the output to confirm that the code is indeed unused
3. Delete in batches, committing only a few file deletions at a time
4. Run tests to ensure no functionality is broken
5. Use descriptive commit messages like "clean: remove unused exports"

### Cautions During Cleanup
- Be cautious when deleting public API exports
- Retain code that may be used by external tools
- Check for dynamic imports and string references
- Preserve type definitions and interface declarations
