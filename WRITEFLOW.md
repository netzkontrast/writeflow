# WriteFlow AI Assistant Guide

WriteFlow is an AI writing assistant designed for technical writers, providing professional-grade writing tools and a task management system.

## TodoList System Usage Guidelines

### Core Philosophy
WriteFlow's TodoList system is an intelligent task management tool that helps AI assistants organize and track complex writing tasks. The system is based on the core design principles of Claude Code v1.0.33, specifically optimized for writing scenarios.

### Automatic Trigger Scenarios
The AI assistant should proactively create and use a TodoList in the following situations:

#### 1. Complex Multi-Step Writing Tasks
- Automatically create a TodoList when a task involves 3 or more writing steps.
- Example: Technical blog (Research → Outline → Draft → Proofread → Optimize)
- Example: Marketing copy (Requirement analysis → Brainstorming → Copywriting → Performance optimization)

#### 2. Multiple Content Creation
- When the user provides multiple writing tasks (e.g., "write three articles").
- Bulk content creation (e.g., product introduction series, tutorial series).
- Similar content in different styles (formal version, simplified version, social media version).

#### 3. Structured Writing Projects
- Projects that require planning a complex writing process.
- Academic papers, technical documentation, business plans, etc.
- Content that requires multiple rounds of revision and optimization.

#### 4. Explicit User Request
- When the user directly asks to use a task list for management.
- When the user needs to see the writing progress and status.

### Tool Usage Details

#### todo_write - Task List Management Tool
**Function**: Updates and manages the current list of writing tasks.
**When to use**:
- Immediately upon receiving a new writing task.
- Mark as `in_progress` before starting to execute a task.
- Mark as `completed` immediately after finishing a task.
- Add new sub-tasks to the list when they are identified.

**Input Format**:
```json
{
  "todos": [
    {
      "id": "todo-1234-5678",
      "content": "Research the latest trends in artificial intelligence",
      "activeForm": "Researching the latest trends in artificial intelligence",
      "status": "pending",
      "priority": "high"
    }
  ]
}
```

#### todo_read - Task Status Query Tool
**Function**: Reads the status and progress of the current writing tasks.
**When to use**:
- When you need to know the current writing progress.
- When providing a status update to the user.
- When deciding on the next writing direction.

**Input Format**: Empty object `{}` (no parameters required).

### State Management Rules

#### 1. Task Status Definitions
- **pending**: The task has not yet started and is awaiting execution.
- **in_progress**: The task is currently being worked on (only one at a time).
- **completed**: The task has been finished.

#### 2. Dual Description System
Each task must include two types of descriptions:
- **content**: An imperative description stating what needs to be done.
  - Example: "Write product introduction copy"
- **activeForm**: a progressive tense description showing what is currently being done.
  - Example: "Writing product introduction copy"

#### 3. Single Active Principle
- **Key Rule**: Only one task can be in the `in_progress` state at any given time.
- Before starting a new task, the current one must be completed or paused.
- This ensures a focused writing process and orderly task execution.

#### 4. Immediate Update Principle
- Mark a task as `completed` immediately after finishing it.
- Do not update statuses in bulk.
- If a task is blocked, keep it as `in_progress` and create a new task to resolve the issue.

### Writing Scenario Examples

#### Scenario 1: Technical Blog Creation
User: "Help me write a technical blog post about the development of AI, including background, technical analysis, application cases, and future outlook."

The AI should create the following task list:
```
1. [pending] Research the background and latest trends of AI development.
2. [pending] Write the technical analysis section.
3. [pending] Collect and write application cases.
4. [pending] Analyze and write the future outlook.
5. [pending] Proofread and optimize the entire article.
```

#### Scenario 2: Multi-Style Copywriting
User: "Write three versions of the introduction for our new product: a professional version, a casual version, and a social media version."

The AI should create the following task list:
```
1. [pending] Analyze product features and target audience.
2. [pending] Write the professional version of the product introduction.
3. [pending] Write the casual version of the product introduction.
4. [pending] Create the social media version of the introduction.
5. [pending] Check and optimize the consistency of the three versions.
```

#### Scenario 3: Document Structure Optimization
User: "Help me reorganize the structure of this report to make it more logical and add transition paragraphs."

The AI should create the following task list:
```
1. [pending] Analyze the existing document structure and logical issues.
2. [pending] Design a new document structure framework.
3. [pending] Rearrange the order of chapters and paragraphs.
4. [pending] Add transition paragraphs and connecting sentences.
5. [pending] Review the overall document for coherence and flow.
```

### Scenarios Where TodoList Should Not Be Used

#### Simple Modification Tasks
- Polishing a single sentence: "Make this sentence more concise."
- Simple replacement: "Change all instances of 'company' to 'organization' in the text."
- Formatting adjustments: Changing fonts, adjusting spacing, etc.

#### Consultation-Type Requests
- Asking for writing tips: "How can I make my writing more persuasive?"
- Evaluating content: "What do you think of this headline?"
- Getting suggestions: "What tone should I use for this article?"

#### Immediate Tasks
- Finding information: "What time is it?"
- Simple calculation: "How many words are in this article?"
- Direct answer: "What is SEO?"

### Best Practices

#### Task Naming Convention
Start with a clear verb:
- Good Example: ✅ "Write the core product features introduction"
- Bad Example: ❌ "Product features section"

#### Task Granularity Control
- **Moderate Granularity**: A task should be completable within a reasonable amount of time.
- **Avoid Being Too Fine-Grained**: Don't separate tasks like "write the first paragraph" and "write the second paragraph."
- **Avoid Being Too Coarse**: Don't use large tasks like "complete the entire article."

#### Priority Setting Principles
- **High**: Core content creation, key chapters, urgent deadlines.
- **Medium**: Structural adjustments, format optimization, supplementary content.
- **Low**: Detail polishing, style unification, final proofreading.

### System Integration Features

#### Smart Reminder System
WriteFlow automatically injects system reminders to help the AI maintain task awareness:
- Automatic reminders when the task list changes.
- Appropriate suggestions when the task list is empty.
- Reminders to check progress during long sessions.

#### State Persistence
- Task states are preserved between sessions.
- Supports parallel management of multiple writing projects.
- Provides a complete history of writing activities.

#### Progress Visualization
- Real-time display of writing progress percentage.
- Tasks grouped by status.
- Provides statistics on writing efficiency.

## Writing Tool Ecosystem

WriteFlow's TodoList system is deeply integrated with other writing tools:

### Research Tools
- Web search and information gathering.
- Citation management and literature organization.
- Data analysis and chart generation.

### Creation Tools
- Outline generator.
- Content rewriter.
- Style adapter.
- Grammar checker.

### Publishing Tools
- Format converter.
- Social media optimizer.
- SEO optimization suggestions.

## Advanced Usage

### Templated Task Workflows
Pre-define task templates for common writing types:

#### Technical Documentation Template
```
1. Requirement analysis and goal setting.
2. Technical research and information gathering.
3. Document structure design.
4. Core content writing.
5. Example and diagram creation.
6. Full-text proofreading and formatting.
```

#### Marketing Copy Template
```
1. Target audience analysis.
2. Core selling point refinement.
3. Creative concept development.
4. Main copy writing.
5. Call-to-action optimization.
6. A/B test version preparation.
```

#### Novel Writing Template
```
1. Conceive the basic framework and setting of the novel.
2. Design the main characters and their relationships.
3. Write the opening chapters of the novel.
4. Develop the plot and conflicts.
5. Proofread and unify the style of the entire text.
```

**Important**: Novel writing must strictly follow the order above to ensure:
- The framework exists before the characters.
- The characters exist before the plot is written.
- The foundation is laid before the details are perfected.

### Bulk Task Management
Supports managing multiple writing projects simultaneously:
- Use different task ID prefixes for different projects.
- Supports task dependencies between projects.
- Provides a cross-project progress overview.

### Collaborative Writing Support
- Supports task assignment for multi-person collaboration.
- Provides mechanisms for task comments and feedback.
- Integrates version control and change tracking.

## Error Handling and Recovery

### Common Problem Solving
- **Task Stuck**: Create sub-tasks to break down the problem.
- **Requirement Changes**: Update the task list in a timely manner.
- **Quality Not Met**: Add proofreading and optimization tasks.

### Best Recovery Strategies
1. Maintain the authenticity of task statuses.
2. Do not forcibly mark a task as complete when encountering problems.
3. Promptly record solutions and lessons learned.

## Conclusion

WriteFlow's TodoList system is the core of a professional writing workflow. Through intelligent task management, status tracking, and system integration, it helps users and AI assistants collaborate to complete high-quality writing projects.

**Key Success Factors**:
- Timely creation and updating of the task list.
- Strict adherence to the single active principle.
- Maintaining clarity in task descriptions.
- Timely utilization of system reminders and progress feedback.

By following the guidelines and best practices in this guide, WriteFlow will become your most capable AI writing assistant!
