# SPEC-005: AI Actionable Items Extraction (Ollama)

**Feature ID:** SPEC-005
**Priority:** Medium
**Complexity:** Medium
**Status:** Planned
**Dependencies:** SPEC-006 (Ollama Core Integration)
**Estimated Effort:** 3-4 days

---

## Overview

Extract practical, actionable insights from podcast transcripts using Ollama LLM—including implementable ideas, recommended practices, learning opportunities, and follow-up research topics.

## User Story

**As a** podcast listener seeking practical value
**I want to** see a list of actionable takeaways and implementable ideas
**So that** I can apply learnings from the episode to my work or life

## Problem Statement

Podcasts often contain valuable advice, techniques, and ideas, but they're buried in hours of conversation. Users want to quickly identify "what can I do with this information?" without re-listening or re-reading the entire transcript.

## Acceptance Criteria

- [ ] "Actions" tab appears in transcript modal
- [ ] Actions organized into clear categories (Ideas, Best Practices, Learn More, etc.)
- [ ] Each action item includes context from transcript
- [ ] Checkbox UI for tracking completion (optional feature)
- [ ] Export to todo list format (markdown checkboxes)
- [ ] Cached in IndexedDB
- [ ] Generation takes <45 seconds
- [ ] Copy individual items or entire list
- [ ] Empty if no actionable content found

## Functional Requirements

### 1. Action Categories

**Implementable Ideas**
- Specific techniques or approaches to try
- Project ideas mentioned
- Creative applications of concepts

**Best Practices & Techniques**
- Recommended workflows
- Productivity tips
- Technical patterns or approaches

**Learning Opportunities**
- Courses to take
- Skills to develop
- Tools to explore
- Books to read (practical, how-to focused)

**Research & Exploration**
- Topics to investigate further
- Questions raised that warrant research
- Emerging trends to follow

**Direct Action Items**
- Explicit recommendations from speakers
- "You should..." statements
- Specific next steps mentioned

### 2. Output Format

```markdown
## Actionable Insights

### 💡 Implementable Ideas
- [ ] Try using vector databases for semantic search in your application
  - Context: Discussed how embedding-based search improves user experience
  - Resources: ChromaDB, Pinecone mentioned as options

- [ ] Build a personal AI assistant using open-source LLMs
  - Context: Speaker walked through architecture using Ollama + LangChain
  - Difficulty: Intermediate

### ✅ Best Practices & Techniques
- [ ] Always validate LLM outputs before presenting to users
  - Context: Discussion of AI hallucinations and safety measures
  - Apply to: Any AI-powered feature

- [ ] Use temperature=0 for deterministic AI responses
  - Context: Configuration tips for consistent results
  - Tools: OpenAI, Anthropic, Ollama APIs

### 📚 Learning Opportunities
- [ ] Learn about transformer architecture fundamentals
  - Resources: "Attention Is All You Need" paper recommended
  - Why: Foundation for understanding modern LLMs

- [ ] Explore prompt engineering techniques
  - Courses: DeepLearning.AI short course mentioned
  - Time: ~2 hours

### 🔍 Research & Exploration
- [ ] Investigate retrieval-augmented generation (RAG) patterns
  - Why: Mentioned as solution for reducing hallucinations
  - Keywords: RAG, vector search, embeddings

- [ ] Follow developments in on-device AI models
  - Context: Discussion of privacy-preserving local inference
  - Watch: Apple ML research, TinyML conferences

### 🎯 Direct Recommendations
- [ ] Start with smaller models (7B parameters) before scaling up
  - Who said: Guest expert recommendation
  - Rationale: Faster iteration, lower costs

- [ ] Set up monitoring for AI-powered features
  - Tools: Weights & Biases, LangSmith mentioned
  - Priority: High for production systems
```

### 3. Prompt Template

```
You are analyzing a podcast transcript to extract actionable insights and practical takeaways.

Identify and categorize:
1. **Implementable Ideas**: Specific techniques, projects, or approaches to try
2. **Best Practices & Techniques**: Recommended workflows, productivity tips, technical patterns
3. **Learning Opportunities**: Courses, skills, tools, or books to explore
4. **Research & Exploration**: Topics to investigate, questions to explore, trends to follow
5. **Direct Recommendations**: Explicit "you should..." advice from speakers

For each item:
- Write as a clear, actionable statement
- Include relevant context (why it matters, where it was discussed)
- Mention specific resources, tools, or people if applicable
- Estimate difficulty/time if possible
- Prioritize practical, concrete actions over abstract concepts

Format as markdown with checkboxes. Skip categories with no items.
Focus on what listeners can DO with this information.

Transcript:
{transcript_text}

Actionable Items:
```

## Technical Requirements

### 1. API Configuration

**Endpoint:** `http://localhost:11434/api/generate`

**Request:**
```javascript
{
  model: "llama3",  // Or command-r for instruction-following
  prompt: actionableItemsPrompt,
  stream: false,
  options: {
    temperature: 0.3,     // Balance creativity with accuracy
    num_predict: 1200,    // Room for comprehensive lists
    top_p: 0.85
  }
}
```

### 2. Checkbox Tracking (Optional Feature)

**Local Storage:**
```javascript
{
  episodeId: "ABC123",
  completedActions: [
    {
      actionId: "action-1",  // Hash of action text
      completedAt: "2025-01-26T14:30:00Z",
      notes: "Completed - worked well!"
    }
  ]
}
```

**UI Behavior:**
- Clicking checkbox toggles completion state
- Completed items styled differently (strikethrough, faded)
- Persist state across sessions
- Option to "Reset All" checkboxes

### 3. Export Formats

**Markdown (for todo apps):**
```markdown
- [ ] Try using vector databases for semantic search
- [ ] Build a personal AI assistant using open-source LLMs
- [ ] Learn about transformer architecture fundamentals
```

**Plain Text:**
```
• Try using vector databases for semantic search
• Build a personal AI assistant using open-source LLMs
• Learn about transformer architecture fundamentals
```

**JSON (for integration with task managers):**
```json
{
  "actions": [
    {
      "category": "Implementable Ideas",
      "title": "Try using vector databases for semantic search",
      "context": "Discussed how embedding-based search improves UX",
      "resources": ["ChromaDB", "Pinecone"],
      "difficulty": "intermediate"
    }
  ]
}
```

## Implementation Tasks

- [ ] Design Actions tab UI
  - [ ] Create category sections with icons
  - [ ] Design checkbox list items
  - [ ] Add context/resource sections per item
  - [ ] Implement copy buttons
  - [ ] Add export dropdown (MD/TXT/JSON)
- [ ] Implement extraction logic
  - [ ] Build actionable items prompt template
  - [ ] Create `extractActionableItems(transcript, model)` function
  - [ ] Parse markdown output into structured data
  - [ ] Categorize items correctly
- [ ] Checkbox tracking (optional)
  - [ ] Implement local storage for completion state
  - [ ] Add toggle functionality
  - [ ] Style completed items
  - [ ] Add "Reset All" option
- [ ] Export functionality
  - [ ] Markdown export with checkboxes
  - [ ] Plain text export
  - [ ] JSON export for integrations
  - [ ] Copy to clipboard
- [ ] Caching
  - [ ] Store extracted actions in IndexedDB
  - [ ] Track action count per category
  - [ ] Cache invalidation logic
- [ ] Testing
  - [ ] Test with various podcast types
  - [ ] Validate categorization accuracy
  - [ ] Test checkbox state persistence
  - [ ] Test export formats

## Testing Strategy

### Unit Tests
- Action item parsing
- Categorization logic
- Checkbox state management
- Export format generation

### Integration Tests
- Full extraction flow
- Cache storage and retrieval
- Checkbox persistence across sessions
- Export to different formats

### Edge Cases
- Transcripts with no actionable content
- Very long action items (truncation)
- Checkbox state with cache invalidation
- Concurrent checkbox toggles

## UI/UX Considerations

1. **Visual Design**
   - Use icons for categories (💡 ✅ 📚 etc.)
   - Checkboxes styled like todo lists
   - Clear visual feedback for completed items

2. **Interactivity**
   - Click anywhere on item to toggle checkbox
   - Hover to reveal copy button
   - Expand/collapse context sections

3. **Progress Tracking**
   - Show completion percentage (e.g., "5/12 completed")
   - Visual progress bar (optional)
   - "Clear completed" option

4. **Mobile Experience**
   - Touch-friendly checkboxes
   - Swipe to complete/uncomplete
   - Collapsible categories to save space

## Success Metrics

- Extraction quality (% of genuinely actionable items)
- Categorization accuracy
- Checkbox usage (% of users who track completion)
- Export rate (% of users who export)
- User feedback on usefulness

## Privacy & Performance

- ✅ All processing local (Ollama)
- ✅ Checkbox state stored locally
- ✅ No external dependencies
- ⚠️ Large action lists may impact performance

## Future Enhancements

- [ ] Integration with task management APIs (Todoist, Notion, etc.)
- [ ] Priority/difficulty estimation for each action
- [ ] Estimated time to complete
- [ ] Group related actions into "projects"
- [ ] Remind users of incomplete actions
- [ ] Share action lists with others
- [ ] Generate calendar events for learning opportunities
- [ ] Track action completion statistics over time

## Related Specs

- SPEC-002: Quick Summary
- SPEC-003: Detailed Summary
- SPEC-004: Resource Extraction
- SPEC-006: Ollama Core Integration (dependency)

## References

- [Markdown Checkbox Syntax](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax#task-lists)
- [Todoist API](https://developer.todoist.com/)
- [Notion API](https://developers.notion.com/)

---

**Last Updated:** 2025-01-26
**Author:** Engineering Team
**Reviewers:** TBD
