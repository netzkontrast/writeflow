# WriteFlow Dynamic Status Prompt System

WriteFlow's dynamic status prompt system is an intelligent UI component system designed with reference to the Claude Code yellow prompt box mechanism, specifically optimized for AI writing scenarios.

## Core Features

### 🎯 Intelligent Scene Recognition
- Automatically detects user input intent
- Intelligently switches writing scene states
- Context-aware state inference

### ⚡ Rich Visual Effects
- **Light and Shadow Scan Animation**: Mimics the dynamic scan line effect of Claude Code
- **Multiple Theme Variants**: Four styles including Claude, Enhanced, Minimal, and Glass
- **Dynamic Color System**: Automatically adjusts the color theme based on the scene
- **Progress Visualization**: Real-time display of task completion progress

### 💡 Intelligent Suggestion System
- **Contextual Analysis**: Provides relevant suggestions based on the current state and history
- **Action Suggestions**: Recommends the next executable actions
- **Writing Prompts**: Professional suggestions for the current writing stage
- **Quick Actions**: One-click execution of common commands

### 📊 Rich Writing Scenarios
Supports 16 specialized writing scenarios, including:

- **Article Management**: Creating, editing, and reading articles
- **Research Tools**: Topic research, web search
- **AI Writing**: Outline generation, content rewriting, grammar checking, style adjustment
- **Publication Preparation**: Format conversion, publication optimization
- **System Status**: AI thinking, responding, memory management

## How to Use

### Basic Usage

```typescript
import { 
  useWritingStatus, 
  AdaptiveStatusBanner,
  WRITING_SCENARIOS 
} from '../ui/status'

// Use in a component
function MyWritingApp() {
  const { currentStatus, setStatus } = useWritingStatus()
  
  // Set the writing status
  const handleStartWriting = () => {
    setStatus(WRITING_SCENARIOS.ARTICLE_CREATE, {
      progress: 25,
      subMessage: 'Thinking about the article structure...'
    })
  }
  
  return (
    <div>
      {currentStatus && (
        <AdaptiveStatusBanner
          status={currentStatus}
          showTips={true}
          showActions={true}
          animated={true}
        />
      )}
    </div>
  )
}
```

### Intelligent Suggestion System

```typescript
import { 
  useIntelligentAnalysis, 
  SuggestionsManager 
} from '../ui/status'

function SmartWritingInterface() {
  const { analyzeInput, currentAnalysis } = useIntelligentAnalysis()
  
  const handleUserInput = (input: string) => {
    // Analyze user input
    const analysis = analyzeInput(input, {
      messageHistory: [], // Pass message history
      currentScenario: 'article.create'
    })
    
    console.log('Detected intent:', analysis.detectedIntent)
    console.log('Confidence:', analysis.confidence)
  }
  
  return (
    <div>
      {currentAnalysis && (
        <SuggestionsManager
          analyses={[currentAnalysis]}
          onActionSelect={(action) => {
            console.log('User selected:', action.label)
          }}
        />
      )}
    </div>
  )
}
```

### Custom Status Scenarios

```typescript
import { writingStatusManager } from '../ui/status'

// Create a custom status
writingStatusManager.setStatus('article.create', {
  progress: 60,
  subMessage: 'Perfecting the third chapter...',
  metadata: {
    chapterCount: 5,
    currentChapter: 3,
    wordCount: 2500
  }
})
```

## Component Details

### AdaptiveStatusBanner
An adaptive status banner that automatically selects the best display style based on the scene.

**Props:**
- `status: WritingStatus` - The current writing status
- `showTips?: boolean` - Whether to show tip messages
- `showActions?: boolean` - Whether to show quick actions
- `animated?: boolean` - Whether to enable animation effects

### SuggestionsManager
An intelligent suggestion manager that displays context-relevant action suggestions.

**Props:**
- `analyses: ContextAnalysis[]` - An array of analysis results
- `maxVisible?: number` - The maximum number of suggestions to display
- `onActionSelect?: (action) => void` - A callback for when an action is selected

### StatusBannerManager
A status banner manager that supports the simultaneous display and priority management of multiple statuses.

## Visual Themes

### Claude Theme
Mimics the classic yellow prompt box style of Claude Code, simple and professional.

### Enhanced Theme
An enhanced visual effect with richer colors and border styles.

### Minimal Theme
A minimalist style, suitable for scenarios where a clean interface is desired.

### Glass Theme
A frosted glass effect for a modern visual experience, especially suitable for AI-related statuses.

## Animation Effects

### Scan Line Animation
```typescript
// Enable scan effect
setStatus('ai.thinking', {
  // Will automatically enable the scan animation
})
```

### Progress Animation
```typescript
// Show progress
setStatus('content.rewrite', {
  progress: 75, // Shows a progress bar and percentage
  subMessage: 'Almost done...'
})
```

### Breathing Effect
Suitable for waiting states, a soft breathing light effect.

## Best Practices

### 1. Use Animations Reasonably
```typescript
// Use animations for high-priority tasks
setStatus('ai.thinking', { animated: true })

// Avoid excessive animations for long-running states
setStatus('research.active', { 
  progress: 45,
  animated: false // Avoid distraction
})
```

### 2. Provide Meaningful Sub-Messages
```typescript
setStatus('outline.generate', {
  progress: 30,
  subMessage: 'Generated 3 main chapters, now refining sub-topics...'
})
```

### 3. Utilize Metadata
```typescript
setStatus('article.edit', {
  metadata: {
    wordCount: 1500,
    targetLength: 2000,
    lastSaved: Date.now()
  }
})
```

### 4. Clear Status in a Timely Manner
```typescript
// Clear the status after the task is completed
const { clearStatus } = useWritingStatus()

useEffect(() => {
  // Clear the status 2 seconds after the task is completed
  const timer = setTimeout(clearStatus, 2000)
  return () => clearTimeout(timer)
}, [taskCompleted])
```

## Performance Optimization

### 1. Debounce Updates
For frequent status updates, use a debouncing mechanism:

```typescript
import { debounce } from 'lodash'

const debouncedStatusUpdate = debounce((status) => {
  setStatus(status.type, status.options)
}, 300)
```

### 2. Conditional Rendering
Only display complex components when necessary:

```typescript
{writingStatus && 
  writingStatus.scenario.priority === 'high' && (
    <AdaptiveStatusBanner status={writingStatus} />
  )
}
```

### 3. Lazy Loading Animations
Automatically disable animation effects on devices that do not support them.

## Extension Development

### Adding a New Scene
```typescript
// 1. Add the new scene configuration in WritingStatusManager
const newScenario = {
  id: 'custom.task',
  icon: '⚡',
  title: 'Custom Task',
  color: 'cyan',
  message: 'Executing custom task...',
  tips: ['Tip 1', 'Tip 2'],
  showProgress: true,
  scanEffect: true,
  priority: 'high'
}

// 2. Add intent recognition in ContextAnalyzer
intentPatterns.set('custom_task', [/custom task/i])
scenarioMapping.set('custom_task', 'custom.task')
```

### Custom Animations
```typescript
// Create a custom scan effect
const customScanEffect = () => {
  const patterns = ['◆◇◇◇', '◇◆◇◇', '◇◇◆◇', '◇◇◇◆']
  return patterns[animationFrame % patterns.length]
}
```

## Troubleshooting

### Common Problems

**Q: The status banner is not displayed?**
A: Check if the status is set correctly: `setStatus('article.create')`

**Q: The animation is not smooth?**
A: Animations are automatically disabled on low-performance devices. You can manually set `animated={false}`.

**Q: The intelligent suggestions are not accurate?**
A: Provide more contextual information to the `analyzeInput` method, including message history and the current scene.

**Q: The custom theme is not applied?**
A: Make sure the `variant` prop is correctly passed in the component.

## Changelog

### v1.0.0 (Current Version)
- ✅ Complete status management system
- ✅ Support for 16 writing scenarios
- ✅ 4 visual themes
- ✅ Intelligent analysis and suggestion system
- ✅ Rich animation effects
- ✅ Full TypeScript support

### Future Plans
- 🔄 More custom animation effects
- 🔄 Keyboard shortcut support
- 🔄 Persistent state storage
- 🔄 Multi-language support
- 🔄 Accessibility optimization
