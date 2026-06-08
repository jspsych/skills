---
name: jspsych-timeline-builder
description: Guide for creating shareable jsPsych timelines (experiment components). Use when the user wants to create a reusable experimental procedure, task, or component to share in the jspsych-timelines repository, or when they ask about building modular experiment components in jsPsych. Covers the timeline export structure (createTimeline, timelineUnits, utils), initialization, parameterization, documentation, and publishing.
---

# jsPsych Timeline Builder

This skill guides the creation of shareable jsPsych timelines - reusable experiment components that can be easily adapted and combined by other researchers.

## What Are Timelines?

Timelines are modular, parameterized experiment components built in jsPsych (v8+). They represent reusable **procedures** that can be composed into larger experiments. Unlike plugins (which define individual screen events), timelines compose plugins together to create reusable experimental units like:

- A lexical decision task
- A working memory assessment  
- A Stroop task
- A priming manipulation procedure
- A practice phase with feedback

**Important:** A timeline typically represents a single experimental procedure or component, not a complete multi-procedure experiment. For example, an experiment studying correlations between working memory and language processing might include both a working memory timeline and a lexical decision timeline as separate components.

## When to Create a Timeline

**Create a timeline when:**
- Building a reusable experimental procedure (e.g., Stroop task, lexical decision task)
- Implementing standard experimental paradigms that others might adapt
- Creating cognitive assessments or standardized tests
- The procedure is complex enough that sharing saves others significant implementation time
- You want to enable others to incorporate your procedure into their experiments

**Don't create a timeline when:**
- The experiment is highly specific with no reusable procedures
- Simple plugin usage suffices for the use case
- Building a one-off custom experiment unlikely to have reusable components

**Key insight:** Think about whether the procedure you're building could be useful as a component in someone else's experiment, not just as a standalone experiment.

## The Timeline Export Structure

Every timeline exports three modules:

### 1. createTimeline()
Function that returns the complete procedure as a jsPsych timeline. This is the "top-level" export representing the full reusable component (e.g., a complete lexical decision task, a working memory assessment, etc.) that can be incorporated into larger experiments.

### 2. timelineUnits
Object containing reusable components, each being a timeline or function that returns a timeline. Components can be:
- Individual trials
- Sequences of trials (e.g., practice blocks)
- Procedures (e.g., lexical decision task)
- Sections (e.g., 1-list and 2-list sections)

**Key principle:** No rigid hierarchy. Export whatever components make sense for your experiment. The only requirement is that items in timelineUnits are timelines or functions returning timelines.

### 3. utils
Supporting functions, constants, or utilities:
- Stimulus sets
- Helper functions for stimulus generation
- Response processing utilities
- Translation functions

## Timeline Development Workflow

### 1. Determine Distribution Intent

**Before initializing**, clarify:
- **For contribution to jspsych-timelines**: Fork and clone jspsych-timelines, then run npx command inside the fork
- **For personal use or independent publishing**: Run npx command in any directory

### 2. Initialize Timeline Package

**For contribution to jspsych-timelines:**

```bash
# Fork https://github.com/jspsych/jspsych-timelines on GitHub
git clone https://github.com/YOUR-USERNAME/jspsych-timelines.git
cd jspsych-timelines/packages
npx @jspsych/new-timeline
```

**For personal/independent use:**

```bash
npx @jspsych/new-timeline
```

The tool prompts for:
- Name
- Description
- Author
- GitHub link (optional)
- Language (TypeScript or JavaScript)
- README link (optional)

Fill out all fields if sharing publicly.

This creates:
- Source script (src/index.ts or src/index.js) with export stubs
- Example HTML (example/index.html)
- README template
- Package configuration (package.json)

### 3. Implement the Timeline

**Plugin dependencies:**
If your timeline requires functionality not available in existing plugins, create a new plugin first:
1. Use the jspsych-plugin-builder skill to create a new plugin as a separate package
2. Publish the plugin (to jspsych-contrib or independently)
3. Add the plugin as a dependency in your timeline's package.json

**Important:** Never create plugins inside timeline packages. Plugins should be independent packages that timelines depend on.

**Build from bottom up:**
1. Identify or create necessary plugins
2. Create individual trials using plugins
3. Group related trials into reusable timeline units
4. Expose meaningful components in timelineUnits
5. Implement the full procedure in createTimeline()
6. Add supporting utilities to utils

**Example structure (for a lexical decision task):**
```javascript
export function createTimeline() {
  // Returns the complete lexical decision task procedure
  return {
    timeline: [
      instructionsTimeline,
      practiceTimeline, 
      testTimeline
    ]
  };
}

export const timelineUnits = {
  instructions: instructionsTimeline,
  practice: practiceTimeline,
  test: testTimeline,
  // Or parameterized functions:
  testBlock: (stimuli, feedback) => { /* return timeline */ }
};

export const utils = {
  stimulusSets: { /* ... */ },
  generateStimuli: (wordList) => { /* ... */ }
};
```

**Composing timelines into experiments:**
Researchers can then compose multiple timelines into larger experiments:
```javascript
// An experiment studying working memory and language
const experiment = {
  timeline: [
    primingTimeline.createTimeline(),
    lexicalDecisionTimeline.createTimeline(),
    workingMemoryTimeline.createTimeline()
  ]
};
```

### 4. Design Parameters

**Effective parameterization:**
- Expose parameters that researchers would meaningfully vary
- Match parameters to experimental concepts (not implementation details)
- Provide sensible defaults
- Use clear, intuitive parameter names

**Example:** A working memory test section might take:
- `dimension`: Number of stimulus categories (1 for 1-list, 2 for 2-list)
- `stimulusSets`: Array of stimulus arrays grouped by category
- `trials`: Number of test trials
- `giveFeedback`: Boolean for practice vs test phases

### 5. Document Components

**Critical:** Add comprehensive DocStrings to every exported component.

**Format:**
```javascript
/**
 * Creates a practice phase with feedback
 * @param {number} numTrials - Number of practice trials (default: 2)
 * @param {Array} stimuli - Array of stimulus objects
 * @param {boolean} showInstructions - Whether to show instructions first (default: true)
 * @returns {Object} jsPsych timeline object
 */
export function practicePhase(numTrials = 2, stimuli, showInstructions = true) {
  // implementation
}
```

These DocStrings auto-generate the README documentation table.

### 6. Test Thoroughly

Use example/index.html to test:
- Full procedure via createTimeline()
- Each component in timelineUnits independently
- Various parameter combinations
- Edge cases
- Integration with other timelines

### 7. Publish

**To jspsych-timelines repository:**
1. Ensure package is complete (source, examples, documentation)
2. Create pull request from your fork
3. jsPsych team reviews and publishes to npm

**Independent publishing:**
Publish directly to npm as a package

## Best Practices

**Plugin separation:**
- Create new plugins as separate packages, never inside timeline packages
- Use the jspsych-plugin-builder skill for new plugin development
- Add plugins as dependencies in package.json
- This maintains modularity and allows plugins to be reused across multiple timelines

**Flexibility:**
- Avoid rigid hierarchies in timelineUnits
- Allow components to be mixed and matched
- Enable interoperability between timeline levels

**Documentation:**
- Write clear, complete DocStrings
- Explain what each component does
- Document all parameters with types and defaults
- Include usage examples in README

**Interoperability:**
- Components from different timelines can be combined
- Design parameters to be intuitive to external users
- Consider how components might be used in unexpected ways

**Testing:**
- Test each exported component independently
- Verify parameter variations work as expected
- Ensure timeline composes correctly with other timelines

## Common Use Cases

**Standard experimental procedures:** Create reusable timelines for common tasks
- Stroop task timeline (parameterized for stimulus timing, congruency conditions)
- Flanker task timeline (parameterized for trial types, feedback)
- Lexical decision task timeline (parameterized for stimuli, timing)
- Working memory assessment timeline (parameterized for list length, categories)

**Multi-phase procedures:** Package instruction + practice + test as a cohesive unit
- Instructions, practice with feedback, and test phases as a single timeline
- Components can still be used independently via timelineUnits

**Cognitive assessments:** Individual tests that might be part of larger batteries
- Each test in NIH Toolbox as its own timeline
- Researchers can combine these into custom batteries

**Experimental manipulations:** Reusable procedure components
- Priming procedure timeline
- Training phase timeline
- Calibration procedure timeline

**Composing experiments:** Mix timelines to create multi-component studies
- Combine lexical decision + working memory to study correlations
- Add priming timeline before main task timeline
- Sequence multiple assessment timelines in custom order
