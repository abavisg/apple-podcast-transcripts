# SPEC-002: AI Quick Summary (Ollama)

**Feature ID:** SPEC-002
**Priority:** High
**Complexity:** Medium
**Status:** Planned
**Dependencies:** SPEC-006 (Ollama Core Integration)
**Estimated Effort:** 3-4 days

---

## Overview

Generate concise 2-3 sentence summaries of podcast episodes using locally-running Ollama LLM, displayed inline on episode cards to help users quickly decide what to listen to or read.

## User Story

**As a** podcast consumer browsing many episodes
**I want to** see a brief AI-generated summary on each episode card
**So that** I can quickly understand the episode's content without reading the full transcript

## Problem Statement

Users currently see only the episode title, description, and metadata. Many podcast descriptions are vague marketing copy or simply missing. A quick AI summary of the actual transcript content would help users identify relevant episodes faster.

## Acceptance Criteria

- [ ] "Generate Summary" button appears on each episode card
- [ ] Button shows loading state while AI processes
- [ ] 2-3 sentence summary displays below episode description
- [ ] Summary is cached locally (IndexedDB) to avoid regeneration
- [ ] Graceful error handling if Ollama is unavailable
- [ ] Summary can be copied to clipboard
- [ ] Option to regenerate summary with different model
- [ ] Summary displays within 5-10 seconds on modern hardware
- [ ] Works offline (after Ollama is installed and models downloaded)

## Functional Requirements

### 1. Summary Generation

**Prompt Template:**
```
Provide a concise 2-3 sentence summary of this podcast episode transcript. Focus on the main topics discussed and key takeaways. Be specific and informative.

Transcript:
{transcript_text}

Summary:
```

**Expected Output Format:**
> This episode explores the future of artificial intelligence with Dr. Jane Smith, covering recent breakthroughs in transformer models and their practical applications. The discussion includes ethical considerations around AI deployment and predictions for the next decade of machine learning research.

### 2. UI Components

**Location:** Episode card (main list view)

**Button States:**
- Initial: "Quick Summary" with sparkle/AI icon ✨
- Loading: "Generating..." with spinner
- Complete: Summary text displayed, button changes to "Regenerate"

**Summary Display:**
- Appears in a highlighted box/card below description
- Light background color to distinguish from description
- Includes small "AI Generated" badge
- Copy button in corner
- Timestamp showing when generated

### 3. Caching Strategy

**Storage:** IndexedDB
**Cache Key:** `summary_${episodeId}_${modelName}`

**Cache Structure:**
```javascript
{
  episodeId: "ABC123",
  summaryType: "quick",
  modelUsed: "llama3",
  generatedAt: "2025-01-26T10:30:00Z",
  content: "Summary text here...",
  tokenCount: 150
}
```

**Cache Invalidation:**
- User clicks "Regenerate"
- User switches model
- Cache older than 30 days (optional cleanup)

## Technical Requirements

### 1. API Integration

**Endpoint:** `http://localhost:11434/api/generate`

**Request:**
```javascript
{
  model: "llama3",  // User-selected model
  prompt: promptTemplate,
  stream: false,
  options: {
    temperature: 0.3,  // Lower = more focused
    num_predict: 150,  // Limit to ~3 sentences
    stop: ["\n\n"]     // Stop at paragraph break
  }
}
```

**Response Handling:**
```javascript
{
  model: "llama3",
  created_at: "2025-01-26T10:30:00Z",
  response: "AI-generated summary text",
  done: true
}
```

### 2. Error Handling

**Ollama Not Running:**
```
Cannot connect to Ollama. Please ensure Ollama is running locally.
[Install Ollama] [Learn More]
```

**Model Not Found:**
```
Model "llama3" not found. Please pull the model first.
[How to Install Models]
```

**Timeout (>30 seconds):**
```
Summary generation timed out. Try a smaller model or reduce transcript length.
[Retry] [Use Different Model]
```

### 3. Transcript Truncation

For very long transcripts (>100,000 characters):
- Take first 50,000 + last 50,000 characters
- Add note: "(Summary based on beginning and end of transcript)"
- Or: Smart extraction of key sections using heuristics

## Implementation Tasks

- [ ] Design UI components
  - [ ] Create "Quick Summary" button component
  - [ ] Design summary display card
  - [ ] Add loading spinner/skeleton state
  - [ ] Create "AI Generated" badge
  - [ ] Add copy-to-clipboard button
- [ ] Implement Ollama API client
  - [ ] Create `generateQuickSummary(transcript, model)` function
  - [ ] Build prompt template
  - [ ] Handle request/response
  - [ ] Implement timeout logic (30s)
- [ ] Build caching layer
  - [ ] Set up IndexedDB schema for summaries
  - [ ] Implement `cacheSummary(episodeId, summary)` function
  - [ ] Implement `getCachedSummary(episodeId, model)` function
  - [ ] Add cache invalidation logic
- [ ] Error handling
  - [ ] Detect Ollama availability
  - [ ] Handle connection errors gracefully
  - [ ] Show user-friendly error messages
  - [ ] Provide recovery actions
- [ ] Testing
  - [ ] Test with various transcript lengths
  - [ ] Test with different Ollama models
  - [ ] Test offline behavior
  - [ ] Test cache hit/miss scenarios
  - [ ] Test error states
  - [ ] Performance testing (ensure <10s generation)

## Testing Strategy

### Unit Tests
- Prompt template generation
- Cache key generation
- Summary text validation (length, quality checks)

### Integration Tests
- End-to-end summary generation flow
- Cache storage and retrieval
- Model switching behavior

### Edge Cases
- Very short transcripts (<100 words)
- Very long transcripts (>100k chars)
- Transcripts with unusual characters/formatting
- Ollama returning empty response
- Network interruption mid-generation
- IndexedDB quota exceeded

## UI/UX Considerations

1. **Progressive Enhancement**
   - Show button only if Ollama is detected
   - Graceful degradation if unavailable

2. **Loading States**
   - Clear visual feedback during generation
   - Show estimated time remaining (if possible)
   - Allow cancellation of in-progress requests

3. **Accessibility**
   - Button keyboard accessible (Tab + Enter)
   - Screen reader announces "Generate AI summary"
   - Summary content properly labeled for screen readers

4. **Mobile Experience**
   - Button size optimized for touch targets
   - Summary text readable on small screens
   - Consider collapsible summary on mobile

## Success Metrics

- Summary generation success rate >95%
- Average generation time <10 seconds
- Cache hit rate >70% (users revisiting episodes)
- User engagement: % of episodes where summary is generated
- User satisfaction: Surveys on summary quality/usefulness

## Privacy & Performance

- ✅ All processing happens locally (Ollama)
- ✅ No data sent to external servers
- ✅ Works completely offline
- ✅ No API costs
- ⚠️ Requires Ollama installation (one-time setup)
- ⚠️ Requires sufficient RAM (4GB+ recommended)

## Future Enhancements

- [ ] Batch generate summaries for all episodes
- [ ] Compare summaries from different models side-by-side
- [ ] Allow users to edit/improve AI summaries
- [ ] Share summaries with other users (opt-in)
- [ ] Support for multilingual summaries
- [ ] Generate summary from audio (if integrated with transcription)

## Related Specs

- SPEC-003: Detailed Summary
- SPEC-004: Resource Extraction
- SPEC-005: Actionable Items
- SPEC-006: Ollama Core Integration (dependency)

## References

- [Ollama API Documentation](https://github.com/ollama/ollama/blob/main/docs/api.md)
- [Llama 3 Model Card](https://github.com/meta-llama/llama3)
- [IndexedDB Best Practices](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)

---

**Last Updated:** 2025-01-26
**Author:** Engineering Team
**Reviewers:** TBD
