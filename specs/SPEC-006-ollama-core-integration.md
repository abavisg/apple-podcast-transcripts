# SPEC-006: Ollama Core Integration

**Feature ID:** SPEC-006
**Priority:** High (Blocker for SPEC-002 through SPEC-005)
**Complexity:** High
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 5-7 days

---

## Overview

Establish core integration with Ollama for local AI processing, including connection management, health checks, model selection, error handling, and caching infrastructure. This is a foundational spec that all other Ollama-based features depend on.

## User Story

**As a** developer building AI features
**I want** robust Ollama integration infrastructure
**So that** all AI-powered features work reliably, handle errors gracefully, and provide good UX

## Problem Statement

Multiple features (SPEC-002 through SPEC-005) require Ollama integration. Rather than implementing connection logic, error handling, and caching separately in each feature, we need a centralized, reusable integration layer.

## Acceptance Criteria

- [ ] Detect Ollama availability on app load
- [ ] Display clear setup instructions if Ollama not found
- [ ] Support model selection (dropdown with installed models)
- [ ] Implement health check mechanism
- [ ] Centralized error handling for all Ollama calls
- [ ] IndexedDB caching infrastructure for all AI results
- [ ] Configuration UI for user preferences (default model, timeout, etc.)
- [ ] Graceful degradation when Ollama unavailable
- [ ] Background model download support
- [ ] Performance monitoring (track API call times)

## Functional Requirements

### 1. Ollama Connection Management

**Health Check:**
```javascript
async function checkOllamaHealth() {
  try {
    const response = await fetch('http://localhost:11434/api/version', {
      method: 'GET',
      signal: AbortSignal.timeout(5000)  // 5s timeout
    });

    if (!response.ok) return { status: 'error', message: 'Ollama not responding' };

    const data = await response.json();
    return {
      status: 'online',
      version: data.version
    };
  } catch (error) {
    return {
      status: 'offline',
      message: 'Ollama not running. Please start Ollama service.'
    };
  }
}
```

**Periodic Health Checks:**
- Check on app load
- Re-check every 60 seconds (if previously offline)
- Re-check before each API call (cached for 30s)

### 2. Model Management

**List Available Models:**
```javascript
async function listOllamaModels() {
  const response = await fetch('http://localhost:11434/api/tags');
  const data = await response.json();

  return data.models.map(model => ({
    name: model.name,
    size: model.size,
    modified: model.modified_at,
    digest: model.digest
  }));
}
```

**Recommended Models:**
- **llama3** (8B) - Fast, good quality, recommended default
- **mixtral** (8x7B) - Better for complex tasks, slower
- **mistral** (7B) - Fast alternative to llama3
- **command-r** - Good for instruction-following

**Model Download UI:**
```
┌─────────────────────────────────────────┐
│  AI Model Setup                         │
├─────────────────────────────────────────┤
│  No models found. Download one to start │
│                                         │
│  Recommended:                           │
│  ○ Llama 3 (8B) - 4.7GB                 │
│    Fast, high quality, best for most    │
│    tasks                                │
│    [Download]                           │
│                                         │
│  ○ Mixtral (8x7B) - 26GB                │
│    Slower, highest quality              │
│    [Download]                           │
│                                         │
│  Or install via terminal:               │
│  $ ollama pull llama3                   │
└─────────────────────────────────────────┘
```

### 3. Configuration UI

**Settings Panel:**
```
┌─────────────────────────────────────────┐
│  AI Settings                            │
├─────────────────────────────────────────┤
│  Default Model:                         │
│  [▼ llama3            ]                 │
│                                         │
│  Request Timeout:                       │
│  [60] seconds                           │
│                                         │
│  Cache Duration:                        │
│  [30] days                              │
│                                         │
│  ☑ Enable auto-summaries                │
│  ☑ Cache AI responses                   │
│  ☐ Show debug info                      │
│                                         │
│  [Clear All Caches]  [Save Settings]    │
└─────────────────────────────────────────┘
```

**Persisted Settings:**
```javascript
{
  defaultModel: "llama3",
  requestTimeout: 60000,  // ms
  cacheDuration: 30,       // days
  autoSummaries: true,
  enableCaching: true,
  showDebugInfo: false
}
```

### 4. Caching Infrastructure

**IndexedDB Schema:**
```javascript
const dbSchema = {
  name: 'OllamaCache',
  version: 1,
  stores: {
    // AI responses cache
    responses: {
      keyPath: 'cacheKey',
      indexes: {
        episodeId: 'episodeId',
        summaryType: 'summaryType',
        modelUsed: 'modelUsed',
        createdAt: 'createdAt'
      }
    },
    // User settings
    settings: {
      keyPath: 'key'
    },
    // Model metadata
    models: {
      keyPath: 'name'
    }
  }
};
```

**Cache Key Generation:**
```javascript
function generateCacheKey(episodeId, summaryType, modelName, promptHash) {
  // Include prompt hash to invalidate when prompts change
  return `${episodeId}_${summaryType}_${modelName}_${promptHash}`;
}
```

**Cache Invalidation:**
- Manual: User clicks "Regenerate"
- Automatic: Cache older than configured duration (default 30 days)
- Prompt change: Different prompt template = cache miss

### 5. Error Handling

**Error Types:**

```javascript
class OllamaError extends Error {
  constructor(message, type, details = {}) {
    super(message);
    this.name = 'OllamaError';
    this.type = type;  // 'connection', 'model', 'timeout', 'generation'
    this.details = details;
    this.userMessage = this.getUserFriendlyMessage();
  }

  getUserFriendlyMessage() {
    switch(this.type) {
      case 'connection':
        return 'Cannot connect to Ollama. Please make sure Ollama is running.';
      case 'model':
        return `Model "${this.details.model}" not found. Please install it first.`;
      case 'timeout':
        return 'Request timed out. Try using a smaller model or reducing transcript length.';
      case 'generation':
        return 'Failed to generate response. Please try again.';
      default:
        return 'An unexpected error occurred.';
    }
  }
}
```

**Error UI:**
```
┌─────────────────────────────────────────┐
│  ⚠️ Cannot Connect to Ollama            │
├─────────────────────────────────────────┤
│  AI features require Ollama to be       │
│  running locally.                       │
│                                         │
│  1. Install Ollama:                     │
│     https://ollama.com                  │
│                                         │
│  2. Start Ollama:                       │
│     $ ollama serve                      │
│                                         │
│  3. Install a model:                    │
│     $ ollama pull llama3                │
│                                         │
│  [Check Again]  [Dismiss]               │
└─────────────────────────────────────────┘
```

### 6. API Wrapper

**Centralized Request Handler:**
```javascript
class OllamaClient {
  constructor(config = {}) {
    this.baseUrl = config.baseUrl || 'http://localhost:11434';
    this.defaultModel = config.defaultModel || 'llama3';
    this.timeout = config.timeout || 60000;
    this.cache = new OllamaCache();
  }

  async generate(prompt, options = {}) {
    // 1. Check health
    const health = await this.checkHealth();
    if (health.status !== 'online') {
      throw new OllamaError('Ollama is offline', 'connection');
    }

    // 2. Check cache
    const cacheKey = this.cache.generateKey(prompt, options);
    const cached = await this.cache.get(cacheKey);
    if (cached && options.useCache !== false) {
      return cached;
    }

    // 3. Make request
    const model = options.model || this.defaultModel;
    const response = await this.request('/api/generate', {
      model,
      prompt,
      stream: options.stream || false,
      options: {
        temperature: options.temperature || 0.3,
        num_predict: options.num_predict || 500,
        ...options.modelOptions
      }
    });

    // 4. Cache response
    if (options.useCache !== false) {
      await this.cache.set(cacheKey, response, options.cacheMetadata);
    }

    return response;
  }

  async request(endpoint, body, options = {}) {
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), this.timeout);

    try {
      const response = await fetch(`${this.baseUrl}${endpoint}`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(body),
        signal: controller.signal
      });

      clearTimeout(timeoutId);

      if (!response.ok) {
        throw new OllamaError(
          `HTTP ${response.status}`,
          'generation',
          { status: response.status }
        );
      }

      return await response.json();
    } catch (error) {
      clearTimeout(timeoutId);

      if (error.name === 'AbortError') {
        throw new OllamaError('Request timed out', 'timeout');
      }

      throw error;
    }
  }

  async checkHealth() {
    // Implementation from earlier
  }

  async listModels() {
    // Implementation from earlier
  }
}
```

## Implementation Tasks

- [ ] Set up IndexedDB schema
  - [ ] Create database with stores (responses, settings, models)
  - [ ] Add indexes for queries
  - [ ] Implement migration logic
- [ ] Implement OllamaClient class
  - [ ] Connection health checks
  - [ ] Request/response handling
  - [ ] Timeout management
  - [ ] Error handling
- [ ] Build caching layer
  - [ ] Cache key generation
  - [ ] Get/set/delete operations
  - [ ] Cache invalidation logic
  - [ ] Storage quota management
- [ ] Implement model management
  - [ ] List installed models
  - [ ] Model selection UI
  - [ ] Model download instructions/integration
  - [ ] Model metadata storage
- [ ] Create configuration UI
  - [ ] Settings panel design
  - [ ] Settings persistence (localStorage or IndexedDB)
  - [ ] Default values
  - [ ] Validation
- [ ] Build error handling system
  - [ ] Custom error classes
  - [ ] Error UI components
  - [ ] User-friendly messages
  - [ ] Recovery actions
- [ ] Add monitoring/debugging
  - [ ] Log API call times
  - [ ] Track cache hit/miss rates
  - [ ] Debug mode with detailed logs
- [ ] Testing
  - [ ] Unit tests for all core functions
  - [ ] Integration tests with real Ollama instance
  - [ ] Error scenario testing
  - [ ] Performance benchmarks
  - [ ] Cache behavior tests

## Testing Strategy

### Unit Tests
- Cache key generation
- Error message generation
- Settings validation
- Health check parsing

### Integration Tests
- Full request/response cycle
- Cache storage and retrieval
- Model listing
- Timeout behavior

### Edge Cases
- Ollama stops mid-request
- Disk quota exceeded (IndexedDB)
- Very large responses (>10MB)
- Concurrent requests to same endpoint
- Model deleted while in use

## UI/UX Considerations

1. **First-Time Setup**
   - Detect new user (no Ollama configured)
   - Show friendly onboarding guide
   - Test connection and provide feedback

2. **Status Indicators**
   - Global status badge (Ollama online/offline)
   - Model loaded indicator
   - Cache status (size, hit rate)

3. **Progressive Disclosure**
   - Hide advanced settings by default
   - Show debug info only when enabled
   - Provide "Learn More" links

4. **Accessibility**
   - Settings accessible via keyboard
   - Screen reader announcements for status changes
   - Clear error messages

## Success Metrics

- Connection success rate >99% (when Ollama running)
- Cache hit rate >60%
- Average API response time <5s for quick summaries
- Zero crashes from Ollama errors
- Settings save success rate 100%

## Security Considerations

- Ollama runs on localhost only (no external access)
- No API keys or authentication needed
- Cache stored locally (IndexedDB) - user's browser only
- No telemetry or data sent externally
- CORS: Ollama allows localhost requests by default

## Performance Considerations

- Cache to minimize redundant API calls
- Timeout to prevent hanging requests
- Lazy load models list (only when settings opened)
- Debounce health checks (don't check every second)
- Monitor IndexedDB size, implement cleanup if needed

## Future Enhancements

- [ ] Support for remote Ollama instances (custom URL)
- [ ] Streaming response support (progressive UI updates)
- [ ] Model comparison (A/B test different models)
- [ ] Export/import settings and caches
- [ ] Ollama API analytics dashboard
- [ ] Auto-update prompts when newer versions available
- [ ] Integration with other local LLM runners (LM Studio, etc.)

## Related Specs

**Blocked Specs (require this as dependency):**
- SPEC-002: Quick Summary
- SPEC-003: Detailed Summary
- SPEC-004: Resource Extraction
- SPEC-005: Actionable Items

## References

- [Ollama API Documentation](https://github.com/ollama/ollama/blob/main/docs/api.md)
- [IndexedDB API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)
- [AbortController for Timeouts](https://developer.mozilla.org/en-US/docs/Web/API/AbortController)
- [Ollama Model Library](https://ollama.com/library)

---

**Last Updated:** 2025-01-26
**Author:** Engineering Team
**Reviewers:** TBD
