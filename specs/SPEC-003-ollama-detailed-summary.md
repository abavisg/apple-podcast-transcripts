# SPEC-003: AI Detailed Summary (Ollama)

**Feature ID:** SPEC-003
**Priority:** High
**Complexity:** Medium
**Status:** Planned
**Dependencies:** SPEC-006 (Ollama Core Integration)
**Estimated Effort:** 3-4 days

---

## Overview

Generate comprehensive 300-500 word summaries of podcast episodes with chapter-like structure, main topics, key arguments, and takeaways using locally-running Ollama LLM.

## User Story

**As a** podcast listener who wants to deeply understand an episode
**I want to** access a detailed AI-generated summary with structured breakdown
**So that** I can review key points, skip to relevant sections, and retain information better

## Problem Statement

Quick summaries (SPEC-002) provide overview, but users often need more depth—especially for long-form podcasts (1-3 hours). A detailed summary with topic breakdown helps users understand the episode structure and decide which sections to read/listen to.

## Acceptance Criteria

- [ ] "Detailed Summary" tab appears in transcript modal
- [ ] Summary includes 300-500 words of structured content
- [ ] Content organized into clear sections (Introduction, Main Topics, Key Takeaways)
- [ ] Each main topic includes 2-3 sentence description
- [ ] Summary cached in IndexedDB
- [ ] Generation takes <30 seconds for typical transcripts
- [ ] Markdown formatting supported (headers, lists, bold)
- [ ] Copy entire summary or individual sections
- [ ] Works offline with local Ollama
- [ ] Responsive design for mobile viewing

## Functional Requirements

### 1. Summary Structure

**Expected Output Format:**

```markdown
## Overview
[2-3 sentence high-level overview of the episode]

## Main Topics Discussed

### 1. [Topic Name]
[2-3 sentences describing this topic, key points raised, and context]

### 2. [Topic Name]
[2-3 sentences describing this topic, key points raised, and context]

### 3. [Topic Name]
[2-3 sentences describing this topic, key points raised, and context]

## Key Arguments & Insights
- [Bullet point of significant argument or insight]
- [Bullet point of significant argument or insight]
- [Bullet point of significant argument or insight]

## Practical Takeaways
- [Actionable or memorable takeaway]
- [Actionable or memorable takeaway]
- [Actionable or memorable takeaway]

## Conclusion
[1-2 sentences wrapping up the episode's main message or impact]
```

### 2. Prompt Template

```
You are analyzing a podcast transcript. Provide a detailed, well-structured summary of 300-500 words.

Format your response as follows:
1. Overview: 2-3 sentence high-level summary
2. Main Topics Discussed: 3-5 key topics with 2-3 sentence descriptions each
3. Key Arguments & Insights: Bullet points of significant ideas
4. Practical Takeaways: Bullet points of actionable insights
5. Conclusion: 1-2 sentence wrap-up

Use markdown formatting. Be specific and cite examples from the transcript when relevant.

Transcript:
{transcript_text}

Detailed Summary:
```

### 3. UI Components

**Location:** Transcript modal, accessible via tabs

**Tab Structure:**
```
┌─────────────────────────────────────────────┐
│  [Transcript] [Summary] [Resources] [Actions]│
├─────────────────────────────────────────────┤
│                                             │
│  Detailed Summary                           │
│  ✨ AI Generated • Llama 3 • Jan 26, 2025   │
│  [Copy All] [Regenerate]                    │
│  ─────────────────────────────────────────  │
│                                             │
│  ## Overview                                │
│  This episode explores...                   │
│                                             │
│  ## Main Topics Discussed                   │
│  ### 1. Artificial Intelligence             │
│  The discussion covers...                   │
│  ...                                        │
└─────────────────────────────────────────────┘
```

**Interactive Elements:**
- Copy button for entire summary
- Copy button for individual sections (on hover)
- Regenerate button to create new summary
- Model selector dropdown
- Loading skeleton while generating

## Technical Requirements

### 1. API Integration

**Endpoint:** `http://localhost:11434/api/generate`

**Request:**
```javascript
{
  model: "llama3",
  prompt: detailedSummaryPrompt,
  stream: false,
  options: {
    temperature: 0.4,       // Slightly higher for more detail
    num_predict: 800,       // ~500 words output
    top_p: 0.9,
    stop: ["---", "# References"]
  }
}
```

### 2. Markdown Rendering

**Library:** Use a lightweight markdown renderer
- Options: `marked.js`, `markdown-it`, or native browser rendering
- Sanitize output to prevent XSS
- Support headers, lists, bold, italic, code blocks

**Example:**
```javascript
import { marked } from 'marked';

function renderSummary(markdownText) {
  const sanitizedHTML = DOMPurify.sanitize(marked.parse(markdownText));
  return sanitizedHTML;
}
```

### 3. Caching Strategy

**Cache Key:** `detailed_summary_${episodeId}_${modelName}`

**Storage:**
```javascript
{
  episodeId: "ABC123",
  summaryType: "detailed",
  modelUsed: "llama3",
  generatedAt: "2025-01-26T10:30:00Z",
  content: "## Overview\n...",
  wordCount: 487,
  tokenCount: 650
}
```

### 4. Progressive Generation (Optional)

For better UX, consider streaming the response:
- Stream chunks as they arrive from Ollama
- Render progressively in UI
- User sees content building in real-time

```javascript
async function generateDetailedSummary(transcript, model, onChunk) {
  const response = await fetch('http://localhost:11434/api/generate', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      model: model,
      prompt: buildPrompt(transcript),
      stream: true  // Enable streaming
    })
  });

  const reader = response.body.getReader();
  const decoder = new TextDecoder();
  let summary = '';

  while (true) {
    const { done, value } = await reader.read();
    if (done) break;

    const chunk = decoder.decode(value);
    const lines = chunk.split('\n').filter(line => line.trim());

    for (const line of lines) {
      const parsed = JSON.parse(line);
      summary += parsed.response;
      onChunk(summary);  // Update UI incrementally
    }
  }

  return summary;
}
```

## Implementation Tasks

- [ ] Design tabbed UI for transcript modal
  - [ ] Create tab navigation component
  - [ ] Design summary display layout
  - [ ] Add section-specific copy buttons
  - [ ] Create loading skeleton for streaming
- [ ] Implement detailed summary generation
  - [ ] Build comprehensive prompt template
  - [ ] Create `generateDetailedSummary()` function
  - [ ] Implement streaming support (optional)
  - [ ] Add timeout handling (60s)
- [ ] Markdown rendering
  - [ ] Integrate markdown parser library
  - [ ] Implement XSS sanitization
  - [ ] Style rendered markdown
  - [ ] Test edge cases (malformed markdown)
- [ ] Caching layer
  - [ ] Extend IndexedDB schema for detailed summaries
  - [ ] Implement cache read/write/invalidate
  - [ ] Add cache warming (pregenerate on idle)
- [ ] Copy functionality
  - [ ] Implement "Copy All" button
  - [ ] Implement section copy on hover
  - [ ] Show success toast on copy
- [ ] Testing
  - [ ] Test with various episode lengths
  - [ ] Validate markdown rendering
  - [ ] Test streaming behavior
  - [ ] Test cache performance
  - [ ] Cross-browser testing

## Testing Strategy

### Unit Tests
- Prompt template with different transcript lengths
- Markdown parsing and sanitization
- Word count validation (300-500 words)

### Integration Tests
- Full generation flow with Ollama
- Cache hit/miss scenarios
- Tab switching behavior
- Copy functionality

### Edge Cases
- Transcripts with special characters/code
- Very short episodes (<10 min)
- Very long episodes (>3 hours)
- Ollama returning incomplete markdown
- Network interruption during streaming

## UI/UX Considerations

1. **Loading States**
   - Show skeleton loader while generating
   - If streaming, show progressive text reveal
   - Indicate estimated time (e.g., "~30 seconds")

2. **Tab Management**
   - Remember last viewed tab per episode
   - Lazy load summaries (only generate when tab opened)
   - Show indicator if summary already cached

3. **Readability**
   - Optimal line length for reading (60-80 chars)
   - Proper spacing between sections
   - Clear visual hierarchy (headers, lists)

4. **Mobile Optimization**
   - Collapsible sections on small screens
   - Touch-friendly copy buttons
   - Readable font sizes

## Success Metrics

- Summary quality score (user feedback)
- Completion rate (% of summaries fully generated)
- Average generation time
- Cache hit rate
- User engagement (time spent reading summary vs transcript)

## Performance Considerations

- **Generation Time:** Target <30s, max 60s
- **Memory:** Efficient handling of large transcripts
- **Caching:** Reduce redundant API calls
- **Battery:** Consider background generation (service worker)

## Future Enhancements

- [ ] Allow users to customize summary structure
- [ ] Support multiple summary styles (academic, casual, bullet-only)
- [ ] Enable user editing/refinement of summaries
- [ ] Generate summaries in multiple languages
- [ ] Add "expand section" for deeper dives
- [ ] Link summary sections to transcript timestamps

## Related Specs

- SPEC-002: Quick Summary (dependency)
- SPEC-004: Resource Extraction
- SPEC-005: Actionable Items
- SPEC-006: Ollama Core Integration (dependency)

## References

- [Ollama Streaming API](https://github.com/ollama/ollama/blob/main/docs/api.md#generate-a-completion)
- [Marked.js Documentation](https://marked.js.org/)
- [DOMPurify for XSS Prevention](https://github.com/cure53/DOMPurify)

---

**Last Updated:** 2025-01-26
**Author:** Engineering Team
**Reviewers:** TBD
