# Lessons Learned

## Purpose
This document captures key insights, mistakes, and discoveries from building app creation pipelines. Each lesson helps improve future pipelines.

---

## Pipeline #1: React Web Apps

### What Worked Well ✅

1. **Documentation-First Approach**
   - Writing docs WHILE building prevents forgetting important details
   - Creates clear blueprint for next pipelines

2. **Keeping Everything in One Project**
   - Better than scattering files across desktop
   - Easier for Cursor to maintain context
   - Ready for version control

3. **Starting with React**
   - Good choice for first pipeline due to mature ecosystem
   - Lots of AI training data available
   - Can progressively add complexity

### Challenges Encountered 🚧

1. **Context Management**
   - [To be documented when we hit context limits]

2. **Prompt Engineering**
   - [Will document which prompt styles work best]

3. **Error Handling**
   - [Will note common generation errors]

### Time Estimates vs Reality

| Task | Estimated | Actual | Why Different? |
|------|-----------|--------|----------------|
| Setup | 30 min | [TBD] | [TBD] |
| First App Generation | [TBD] | [TBD] | [TBD] |

### Key Decisions That Shaped the Pipeline

1. **Markdown Triggers**
   - Why: Simple, readable, versionable
   - Impact: [To be evaluated]

2. **Question-First Approach**
   - Why: Prevents assumptions, gets complete requirements
   - Impact: [To be evaluated]

3. **Template System**
   - Why: Reusability, consistency
   - Impact: [To be evaluated]

---

## General Insights (Cross-Pipeline)

### The 70% Problem
- AI consistently gets about 70% of the way
- The last 30% needs human intervention
- Plan for this in all pipelines

### Effective Patterns

1. **Iterative Development**
   - Don't try to generate everything at once
   - Build → Test → Fix → Enhance

2. **Clear Boundaries**
   - Define what AI handles vs what humans handle
   - Set realistic expectations

3. **Validation Checkpoints**
   - Always validate after generation
   - Catch errors early

### Anti-Patterns to Avoid

1. **Over-Ambitious Scope**
   - Start simple, add complexity gradually
   - MVP first, features later

2. **Ignoring Error Messages**
   - [Specific examples to be added]

3. **Skipping Documentation**
   - Document as you go, not after
   - Include the "why" not just "what"

---

## Prompt Engineering Discoveries

### What Makes a Good Prompt

1. **Specificity**
   - [Examples to be added]

2. **Context**
   - [Examples to be added]

3. **Constraints**
   - [Examples to be added]

### Prompt Templates That Work

```
[Will add successful prompt templates here]
```

### Prompts That Failed

```
[Will document what didn't work and why]
```

---

## Tool-Specific Lessons

### Cursor/Claude Specific

1. **Context Window Management**
   - [Strategies that work]

2. **File Organization**
   - [Best practices discovered]

3. **Command Patterns**
   - [Effective ways to instruct Claude]

---

## Metrics and Measurements

### Success Metrics

| Metric | Definition | How to Improve |
|--------|------------|----------------|
| Generation Success | % of apps that run first try | Better templates, validation |
| Time to MVP | Minutes from idea to running app | Streamline questions, better defaults |
| Manual Fixes | Number of human interventions | Identify patterns, improve prompts |

### Tracking Template

For each app generated, track:
- Time taken
- Number of errors
- Manual fixes required
- User satisfaction
- What broke
- What worked perfectly

---

## Recommendations for Next Pipeline

Based on React pipeline experience:

1. **Before Starting**
   - [ ] Review this document
   - [ ] Set realistic automation target
   - [ ] Prepare templates early

2. **During Development**
   - [ ] Document decisions immediately
   - [ ] Test with real examples early
   - [ ] Track time accurately

3. **After Completion**
   - [ ] Update this document
   - [ ] Compare with React pipeline
   - [ ] Identify reusable components

---

## Questions for Future Investigation

1. How to handle database setup automatically?
2. Can we automate deployment configuration?
3. How to manage API keys securely?
4. What's the optimal question set size?
5. How to handle design/UI preferences?

---

## Pipeline Comparison Matrix

| Aspect | React Web | CLI Tools | REST APIs | Mobile |
|--------|-----------|-----------|-----------|---------|
| Automation % | 75% | TBD | TBD | TBD |
| Avg Time | TBD | TBD | TBD | TBD |
| Common Issues | TBD | TBD | TBD | TBD |
| Best For | TBD | TBD | TBD | TBD |

---

## Final Thoughts

[To be added after completing first pipeline]

## Archive of Failed Approaches

[Document what we tried that didn't work, so we don't repeat mistakes]