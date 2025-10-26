# SPEC-021: Data Visualization - Word Clouds & Topic Clustering

**Feature ID:** SPEC-021
**Priority:** Low
**Complexity:** Medium
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 3-4 days

---

## Overview

Generate word clouds and topic clusters from transcript content to visualize common themes and concepts discussed.

## Acceptance Criteria

- [ ] Word cloud generated from transcript
- [ ] Adjustable word count (top 50, 100, 200 words)
- [ ] Filter common words (the, and, is, etc.)
- [ ] Color-coded by frequency
- [ ] Click word to search transcript
- [ ] Export word cloud as image
- [ ] Topic clustering visualization (future)

## Technical Implementation

Use **wordcloud2.js** or **D3-cloud**:

```javascript
import WordCloud from 'wordcloud';

function generateWordCloud(transcript) {
  // Tokenize and count
  const words = transcript.toLowerCase()
    .replace(/[^\w\s]/g, '')
    .split(/\s+/)
    .filter(word => word.length > 3);

  // Remove stopwords
  const stopwords = ['the', 'and', 'for', 'that', 'this', 'with'];
  const filtered = words.filter(w => !stopwords.includes(w));

  // Count frequencies
  const wordCounts = {};
  filtered.forEach(word => {
    wordCounts[word] = (wordCounts[word] || 0) + 1;
  });

  // Convert to array of [word, count]
  const wordList = Object.entries(wordCounts)
    .sort((a, b) => b[1] - a[1])
    .slice(0, 100);

  // Render
  WordCloud(document.getElementById('wordcloud'), {
    list: wordList,
    gridSize: 8,
    weightFactor: 10,
    fontFamily: 'Arial',
    color: 'random-dark',
    rotateRatio: 0.3,
    backgroundColor: '#fff'
  });
}
```

## UI Design

```
┌──────────────────────────────────────┐
│  Most Common Words                   │
│                                      │
│       ARTIFICIAL                     │
│   intelligence    MODEL              │
│       LEARNING   data                │
│  technology  AI    transformer       │
│    NEURAL     network   deep         │
│                                      │
│  [Top 50] [Top 100] [Top 200]        │
│  [Export PNG]                        │
└──────────────────────────────────────┘
```

## Related Specs

- SPEC-020: Timeline & Statistics

## References

- [wordcloud2.js](https://github.com/timdream/wordcloud2.js)
- [D3-cloud](https://github.com/jasondavies/d3-cloud)

---

**Last Updated:** 2025-01-26
