# SPEC-020: Data Visualization - Timeline & Statistics

**Feature ID:** SPEC-020
**Priority:** Low
**Complexity:** Medium
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 3-4 days

---

## Overview

Provide visual analytics including listening timeline, episode statistics, and podcast distribution charts.

## Acceptance Criteria

- [ ] Timeline view of all episodes (chronological)
- [ ] Total listening time calculated
- [ ] Episodes per podcast breakdown
- [ ] Most listened podcasts chart
- [ ] Listening trends over time
- [ ] Export statistics as CSV/JSON

## Visualizations

### 1. Timeline View

```
Jan 2025  |═══════════════════════════════════|
          ↑           ↑       ↑
          Ep 1       Ep 2    Ep 3

Dec 2024  |═══════════════════════|
          ↑     ↑
          Ep 4  Ep 5
```

### 2. Statistics Dashboard

```
┌──────────────────────────────────────┐
│  Your Podcast Stats                  │
├──────────────────────────────────────┤
│  Total Episodes: 127                 │
│  Total Listening Time: 248 hours     │
│  Podcasts Followed: 15               │
│  Most Active Month: Jan 2025         │
│                                      │
│  Top Podcasts:                       │
│  1. Lex Fridman (42 episodes)        │
│  2. All-In Podcast (28 episodes)     │
│  3. Acquired (18 episodes)           │
│                                      │
│  [Export Stats] [Share]              │
└──────────────────────────────────────┘
```

## Technical Implementation

Use **Chart.js** or **D3.js** for visualizations:

```javascript
import Chart from 'chart.js/auto';

function generateStats(episodes) {
  const totalTime = episodes.reduce((sum, ep) => sum + ep.duration, 0);
  const podcastCounts = {};

  episodes.forEach(ep => {
    podcastCounts[ep.podcastName] = (podcastCounts[ep.podcastName] || 0) + 1;
  });

  return {
    totalEpisodes: episodes.length,
    totalTime: totalTime,
    podcastCounts: podcastCounts,
    topPodcasts: Object.entries(podcastCounts)
      .sort((a, b) => b[1] - a[1])
      .slice(0, 5)
  };
}

// Render pie chart
const ctx = document.getElementById('podcastChart');
new Chart(ctx, {
  type: 'pie',
  data: {
    labels: Object.keys(stats.podcastCounts),
    datasets: [{
      data: Object.values(stats.podcastCounts),
      backgroundColor: ['#FF6384', '#36A2EB', '#FFCE56', '#4BC0C0', '#9966FF']
    }]
  }
});
```

## Related Specs

- SPEC-021: Word Clouds

## References

- [Chart.js](https://www.chartjs.org/)
- [D3.js](https://d3js.org/)

---

**Last Updated:** 2025-01-26
