# SPEC-004: AI Resource Extraction (Ollama)

**Feature ID:** SPEC-004
**Priority:** High
**Complexity:** Medium
**Status:** Planned
**Dependencies:** SPEC-006 (Ollama Core Integration)
**Estimated Effort:** 4-5 days

---

## Overview

Automatically extract and categorize all resources mentioned in podcast transcripts—including products, books, people, companies, websites, projects, and technologies—using Ollama LLM to create a structured reference list.

## User Story

**As a** podcast listener interested in mentioned resources
**I want to** see a categorized list of all products, books, people, and links discussed
**So that** I can easily find and follow up on recommendations without manually searching through the transcript

## Problem Statement

Podcasts often reference many resources (books, tools, websites, people, etc.) throughout the conversation. Finding these references later requires reading the entire transcript and manually noting them down. An AI-powered extraction tool can automatically identify, categorize, and list all resources for easy reference.

## Acceptance Criteria

- [ ] "Resources" tab appears in transcript modal
- [ ] Resources organized into clear categories (Products, Books, People, etc.)
- [ ] Each resource includes context snippet from transcript
- [ ] Clickable links auto-detected and formatted
- [ ] Resources cached in IndexedDB
- [ ] Generation takes <45 seconds for typical transcripts
- [ ] Copy entire list or individual categories
- [ ] Export resources as markdown list
- [ ] Deduplication of repeated mentions
- [ ] Empty categories hidden (not shown if no items)

## Functional Requirements

### 1. Resource Categories

Extract and categorize the following:

**Products & Tools**
- Software applications (e.g., "Visual Studio Code", "Notion")
- Hardware devices (e.g., "MacBook Pro", "Sony microphone")
- Online services (e.g., "Spotify", "GitHub")

**Books & Publications**
- Book titles and authors
- Research papers
- Articles, blog posts

**People**
- Individuals mentioned by name
- Authors, researchers, influencers
- Companies/organizations (separate from individuals)

**Companies & Organizations**
- Tech companies (e.g., "OpenAI", "Google")
- Research institutions
- Startups mentioned

**Websites & URLs**
- URLs explicitly mentioned
- Website names (e.g., "arxiv.org")

**Projects & Initiatives**
- Open source projects (e.g., "React", "Linux")
- Research initiatives
- Community projects

**Concepts & Technologies**
- Technical terms (e.g., "Transformer architecture")
- Frameworks (e.g., "TensorFlow", "Next.js")
- Methodologies (e.g., "Agile", "Test-Driven Development")

### 2. Output Format

```markdown
## Resources Mentioned

### 📦 Products & Tools
- **Visual Studio Code** - Microsoft's code editor, discussed in context of developer productivity
- **Notion** - Note-taking and collaboration tool mentioned for project management

### 📚 Books & Publications
- **"Thinking, Fast and Slow"** by Daniel Kahneman - Referenced when discussing decision-making
- **"The Pragmatic Programmer"** by David Thomas & Andrew Hunt

### 👤 People
- **Geoffrey Hinton** - AI researcher, mentioned regarding neural network innovations
- **Yann LeCun** - Discussed contributions to deep learning

### 🏢 Companies & Organizations
- **OpenAI** - AI research lab, discussed in context of GPT models
- **DeepMind** - Referenced for AlphaGo and reinforcement learning work

### 🔗 Websites & Links
- **arxiv.org** - Mentioned as source for AI research papers
- **github.com/anthropics** - Referenced for open source tools

### 🚀 Projects & Initiatives
- **React** - JavaScript library for building user interfaces
- **Kubernetes** - Container orchestration platform discussed for cloud deployment

### 💡 Concepts & Technologies
- **Transformer Architecture** - Discussed as foundation of modern LLMs
- **Retrieval-Augmented Generation (RAG)** - Mentioned for improving AI accuracy
- **Vector Databases** - Technology for semantic search
```

### 3. Prompt Template

```
You are analyzing a podcast transcript to extract all mentioned resources.

Carefully identify and categorize ALL references to:
- Products & Tools (software, hardware, services)
- Books & Publications (with authors if mentioned)
- People (names of individuals referenced)
- Companies & Organizations
- Websites & URLs
- Projects & Initiatives (open source, research, etc.)
- Concepts & Technologies (technical terms, frameworks, methodologies)

For each item:
1. Extract the exact name/title
2. Provide brief context (why it was mentioned)
3. Include author names for books
4. Format URLs as clickable links

Output as a markdown list organized by category. Skip categories with no items.
Be comprehensive but avoid duplicates. If something is mentioned multiple times, include it once with the most relevant context.

Transcript:
{transcript_text}

Resources:
```

## Technical Requirements

### 1. API Configuration

**Endpoint:** `http://localhost:11434/api/generate`

**Request:**
```javascript
{
  model: "llama3",  // Or mixtral for better extraction
  prompt: resourceExtractionPrompt,
  stream: false,
  options: {
    temperature: 0.2,     // Lower for accuracy
    num_predict: 1500,    // More tokens for comprehensive lists
    top_p: 0.8
  }
}
```

### 2. Post-Processing

**Link Detection:**
```javascript
function enrichWithLinks(resourceList) {
  // Auto-detect and format URLs
  const urlPattern = /https?:\/\/[^\s]+/g;

  // Convert website names to links
  const websiteMap = {
    'arxiv.org': 'https://arxiv.org',
    'github.com': 'https://github.com',
    // ... more mappings
  };

  return resourceList.replace(websiteMap);
}
```

**Deduplication:**
```javascript
function deduplicateResources(markdown) {
  // Parse markdown to extract items
  // Compare using fuzzy matching (e.g., "VS Code" vs "Visual Studio Code")
  // Keep first occurrence with most context
  // Return deduplicated markdown
}
```

### 3. Data Structure

**Cache Schema:**
```javascript
{
  episodeId: "ABC123",
  summaryType: "resources",
  modelUsed: "llama3",
  generatedAt: "2025-01-26T10:30:00Z",
  content: "## Resources Mentioned\n...",
  categories: {
    products: 12,
    books: 5,
    people: 8,
    companies: 6,
    websites: 4,
    projects: 7,
    concepts: 15
  },
  totalResources: 57
}
```

## Implementation Tasks

- [ ] Design Resources tab UI
  - [ ] Create category sections with icons
  - [ ] Design resource item cards
  - [ ] Add collapsible sections
  - [ ] Implement copy buttons per category
  - [ ] Add "Export All" button
- [ ] Implement extraction logic
  - [ ] Build comprehensive prompt template
  - [ ] Create `extractResources(transcript, model)` function
  - [ ] Implement post-processing pipeline
  - [ ] Add URL detection and linking
  - [ ] Implement deduplication algorithm
- [ ] Markdown parsing and rendering
  - [ ] Parse categories from markdown
  - [ ] Render with proper formatting
  - [ ] Make URLs clickable
  - [ ] Add icon support for categories
- [ ] Caching
  - [ ] Store extracted resources in IndexedDB
  - [ ] Implement resource count tracking
  - [ ] Add cache invalidation
- [ ] Export functionality
  - [ ] Export as markdown file
  - [ ] Export as JSON
  - [ ] Export as clipboard-friendly text
- [ ] Testing
  - [ ] Test with various podcast types (tech, business, etc.)
  - [ ] Validate deduplication
  - [ ] Test link detection accuracy
  - [ ] Cross-browser URL handling

## Testing Strategy

### Unit Tests
- Category parsing
- Deduplication algorithm
- URL detection and formatting
- Resource counting

### Integration Tests
- End-to-end extraction flow
- Cache storage and retrieval
- Export functionality

### Edge Cases
- Transcripts with no resources
- Transcripts mentioning same resource multiple times
- Ambiguous names (e.g., "Swift" - language or artist?)
- Malformed URLs
- Very long resource lists (>100 items)

## UI/UX Considerations

1. **Visual Organization**
   - Use icons for each category (📦 🔗 👤 etc.)
   - Collapsible sections to reduce scrolling
   - Highlight newly discovered resources

2. **Interactivity**
   - Click to copy individual resource
   - Click external link icon to visit
   - Search/filter within resources

3. **Loading States**
   - Show "Extracting resources..." message
   - Skeleton loader for categories
   - Progressive reveal if streaming

4. **Mobile Experience**
   - Stack categories vertically
   - Touch-friendly buttons
   - Horizontal scroll for long names

## Success Metrics

- Extraction accuracy (% of resources correctly identified)
- Categorization accuracy (% correctly categorized)
- Deduplication effectiveness (no redundant entries)
- User engagement (time spent in Resources tab)
- Export usage (% of users who export resources)

## Privacy & Performance

- ✅ All processing local (Ollama)
- ✅ No data sent externally
- ⚠️ Generation time may be longer for resource-heavy episodes
- ⚠️ Requires models good at information extraction (Mixtral recommended)

## Future Enhancements

- [ ] Smart linking (auto-link to Amazon for books, GitHub for projects)
- [ ] Timestamp linking (click resource to jump to transcript mention)
- [ ] User manual additions/edits to resource list
- [ ] Resource popularity tracking (most mentioned across episodes)
- [ ] Integration with read-it-later services (Pocket, Instapaper)
- [ ] Generate BibTeX for academic citations
- [ ] Compare resources across multiple episodes

## Related Specs

- SPEC-002: Quick Summary
- SPEC-003: Detailed Summary
- SPEC-005: Actionable Items
- SPEC-006: Ollama Core Integration (dependency)

## References

- [Ollama Model Comparison](https://ollama.com/library)
- [Named Entity Recognition](https://en.wikipedia.org/wiki/Named-entity_recognition)
- [Fuzzy String Matching](https://github.com/seatgeek/fuzzywuzzy)

---

**Last Updated:** 2025-01-26
**Author:** Engineering Team
**Reviewers:** TBD
