# <img src="/favicons/favicon-96x96.png?raw=true" height="32px" alt=""/> Apple Podcast Transcript Viewer

A simple, privacy-first UI tool for viewing and copying full Apple Podcast transcripts directly from your Mac. No software installation needed, no data upload required - everything runs locally in your browser.

**Live Demo:** https://alexbeals.com/projects/podcasts/

<img width="1368" alt="Screenshot 2025-01-30 at 11 31 52 PM" src="https://github.com/user-attachments/assets/683f252d-4255-47d5-9e15-2c747ffefb68" />

## The Problem

The Apple Podcasts app on iOS and macOS doesn't allow you to easily export or copy large portions of podcast transcripts. However, these transcripts are stored locally on macOS when you view them in the app. This tool makes those transcripts accessible, searchable, and copyable.

## Features

- **100% Client-Side** - All processing happens in your browser, no data leaves your computer
- **Offline-First** - Works without internet after initial page load
- **Privacy-Focused** - No uploads, no tracking (beyond basic analytics), no external dependencies
- **Drag & Drop Interface** - Simple file operation to access all transcripts
- **Rich Metadata Display** - Shows podcast title, author, duration, date, and description
- **Full Transcript View** - Click any episode to view and copy the complete transcript
- **Smart Sorting** - Episodes ordered by most recently modified

## How to Use

<img width="1368" alt="Screenshot 2025-01-30 at 11 31 47 PM" src="https://github.com/user-attachments/assets/a108a39a-2fbf-4971-a1ee-336d2ec45e9e" />

1. **Cache the transcript locally** - Go to the episode in the [MacOS Podcasts app](podcasts://) and click where it says 'Transcript' (or the triple dots ··· > 'View Transcript')

2. **Navigate to the data folder** - Open a Finder window and select 'Go' > 'Go to Folder' from the menu (or type `Cmd+Shift+G`) and enter:
   ```
   ~/Library/Group Containers/243LU875E5.groups.com.apple.podcasts
   ```

3. **Drag and drop** - Drag all the contents of that folder anywhere on the [website window](https://alexbeals.com/projects/podcasts/)

4. **Browse and copy** - Click on any episode to view the full transcript, which you can then copy and paste to whatever tool you want

<img width="1144" alt="Screenshot 2025-01-31 at 12 08 35 AM" src="https://github.com/user-attachments/assets/0985f961-b661-4679-b6db-026539fa7062" />

## Technical Architecture

### Tech Stack
- **Frontend:** Vanilla JavaScript (ES6 modules), HTML5, CSS3
- **Database:** SQL.js (SQLite compiled to WebAssembly)
- **Special:** Custom-modified SQL.js with WAL (Write-Ahead Logging) support
- **Deployment:** Static site (GitHub Pages compatible)

### File Structure
```
apple-podcast-transcripts/
├── index.html              # Main app UI and logic (561 lines)
├── sql-wasm.js            # Modified SQL.js library
├── sql-wasm.wasm          # WebAssembly SQLite engine
├── favicons/              # App icons
├── images/                # UI assets
│   ├── finder.png
│   ├── github.svg
│   └── og.png
└── README.md
```

### Data Sources

The app reads from Apple's local podcast storage:
```
~/Library/Group Containers/243LU875E5.groups.com.apple.podcasts/
├── Library/
│   └── Cache/
│       └── Assets/
│           └── TTML/           # Transcript XML files (.ttml)
└── Documents/
    └── MTLibrary.sqlite        # Podcast metadata database
    └── MTLibrary.sqlite-wal    # Write-ahead log file
```

## How It Works

### 1. Drag & Drop File Processing
- Recursively traverses the dropped folder structure
- Identifies and extracts:
  - `.ttml` files (Timed Text Markup Language XML transcripts)
  - `MTLibrary.sqlite` (podcast metadata)
  - `MTLibrary.sqlite-wal` (database write-ahead log)

### 2. Transcript Parsing
- Parses TTML XML format to extract:
  - Speaker identification (`ttm:agent` attribute)
  - Sentence-level text content
  - Podcast episode ID from filename
- Tracks file modification timestamps for sorting

### 3. Metadata Extraction
Queries the SQLite database for rich episode information:
- Author name
- Episode title
- Description/subtitle
- Duration
- Publication date (with Apple epoch conversion)

Handles multiple database schema versions gracefully by checking for column existence before querying.

### 4. UI Rendering
- Generates podcast episode cards with all metadata
- Sorts by last modified timestamp
- Provides modal popup for full transcript view
- Enables text selection and copying

## Technical Highlights

### Custom SQL.js WAL Support

The most interesting technical aspect is the manual modification to `sql-wasm.js` to support SQLite WAL (Write-Ahead Logging) files. Apple's podcast database uses WAL mode, which splits data across both `.sqlite` and `.sqlite-wal` files.

**The Problem:** Standard SQL.js doesn't support loading WAL files, only the main database file.

**The Solution:** Manually patched the compiled WebAssembly JavaScript wrapper to accept a second parameter for the WAL file, creating virtual filesystem entries for both files.

#### Original Code (sql-wasm.js)
```javascript
function e(g){this.filename="dbfile_"+(4294967295*Math.random()>>>0);if(null!=g){var l=this.filename,n="/",t=l;n&&(n="string"==typeof n?n:ja(n),t=l?x(n+"/"+l):
n);l=ka(!0,!0);t=la(t,(void 0!==l?l:438)&4095|32768,0);if(g){if("string"==typeof g){n=Array(g.length);for(var w=0,A=g.length;w<A;++w)n[w]=g.charCodeAt(w);g=n}ma(t,l|146);n=na(t,577);oa(n,g,0,g.length,0);pa(n);ma(t,l)}}
```

#### Modified Code (sql-wasm.js)
```javascript
function e(g,zzz){this.filename="dbfile_"+(4294967295*Math.random()>>>0);if(null!=g){var l=this.filename,n="/",t=l;n&&(n="string"==typeof n?n:ja(n),t=l?x(n+"/"+l):
n);l=ka(!0,!0);t=la(t,(void 0!==l?l:438)&4095|32768,0);if(g){if("string"==typeof g){n=Array(g.length);for(var w=0,A=g.length;w<A;++w)n[w]=g.charCodeAt(w);g=n}ma(t,l|146);n=na(t,577);oa(n,g,0,g.length,0);pa(n);ma(t,l)}}if(null!=zzz){var l=this.filename+"-wal",n="/",t=l;n&&(n="string"==typeof n?n:ja(n),t=l?x(n+"/"+l):
n);l=ka(!0,!0);t=la(t,(void 0!==l?l:438)&4095|32768,0);if(zzz){if("string"==typeof zzz){n=Array(zzz.length);for(var w=0,A=zzz.length;w<A;++w)n[w]=zzz.charCodeAt(w);zzz=n}ma(t,l|146);n=na(t,577);oa(n,zzz,0,zzz.length,0);pa(n);ma(t,l)}}
```

**Usage:**
```javascript
// With both database and WAL file
const db = new SQL.Database(
  new Uint8Array(mainDB),
  new Uint8Array(walFile)
);

// Fallback to just main database if WAL not available
const db = new SQL.Database(new Uint8Array(mainDB));
```

This [GitHub issue](https://github.com/sql-js/sql.js/issues/372) was the key to understanding the approach.

### Error Handling & Edge Cases

- **No transcripts found:** Reminds user to view transcript in Podcasts app first
- **Missing metadata:** Falls back to "Unknown" for missing database fields
- **Schema variations:** Checks for column existence before querying
- **Mobile detection:** Displays informative message on touch devices
- **Offline capability:** Initializes SQL.js on page load for offline use

## Future Improvements

### 1. Markdown Export

Add the ability to download transcripts in well-formatted markdown files with complete metadata.

**Proposed Features:**
- Download button on each transcript popup
- Include frontmatter with metadata:
  - Podcast title and author
  - Episode title
  - Publication date
  - Duration
  - Episode description
- Clean markdown formatting with proper sections
- Filename: `[PodcastName] - [EpisodeTitle] - [Date].md`

**Example Output:**
```markdown
---
podcast: The Example Podcast
author: John Doe
episode: Episode 42: The Future of AI
date: January 15, 2025
duration: 1 HR 23 MIN
---

# Episode 42: The Future of AI

**Podcast:** The Example Podcast
**Host:** John Doe
**Date:** January 15, 2025
**Duration:** 1 HR 23 MIN

## Description
This episode explores the cutting edge of artificial intelligence...

## Transcript

[Full transcript content here...]
```

**Implementation Notes:**
- Use `Blob` API to generate downloadable files
- Add download button to transcript modal
- Preserve speaker attribution if available

### 2. AI-Powered Transcript Analysis via Ollama

Integrate local LLM analysis using Ollama to provide intelligent insights without compromising privacy.

**Why Ollama?**
- Runs completely locally (privacy-first)
- No API costs
- Supports multiple open-source models (Llama 3, Mistral, etc.)
- Fast inference on modern hardware

**Proposed Analysis Features:**

#### a) Quick Summary
- 2-3 sentence overview of the episode
- Display inline on episode card or in modal header
- Ideal for quickly deciding what to listen to

#### b) Detailed Summary
- Comprehensive breakdown of main topics discussed
- Key arguments and takeaways
- Chapter-like structure for long episodes
- 300-500 word summary

#### c) Resource Extraction
Automatically identify and list:
- **Products mentioned:** Software, hardware, tools
- **Projects referenced:** Open source projects, startups, initiatives
- **Books recommended:** Titles and authors
- **Websites/URLs:** Resources shared during discussion
- **People mentioned:** Guests, referenced experts
- **Companies discussed:** Organizations, brands
- **Concepts/Technologies:** Technical terms, frameworks, methodologies

Output as categorized markdown list for easy reference.

#### d) Actionable Items & Ideas
Extract practical takeaways:
- Implementable ideas for developers
- Recommended practices or techniques
- Learning opportunities (courses, tools to try)
- Follow-up research topics
- Action items mentioned by speakers

**UI/UX Considerations:**
- "Analyze with AI" button on each transcript
- Tabbed interface within modal: Transcript | Summary | Resources | Actions
- Loading states while Ollama processes
- Cache analysis results locally (IndexedDB)
- Model selection dropdown (let users choose Llama 3, Mistral, etc.)
- Batch analysis option (analyze all transcripts)

**Technical Implementation:**
```javascript
// Example Ollama API call
async function analyzeTranscript(transcript, analysisType) {
  const prompts = {
    summary: `Provide a 2-3 sentence summary of this podcast transcript:\n\n${transcript}`,
    detailed: `Provide a detailed summary with main topics and key takeaways:\n\n${transcript}`,
    resources: `Extract all mentioned products, projects, books, tools, and resources from this transcript. Format as categorized markdown list:\n\n${transcript}`,
    actionable: `Extract actionable items, implementable ideas, and practical takeaways from this transcript:\n\n${transcript}`
  };

  const response = await fetch('http://localhost:11434/api/generate', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      model: 'llama3',  // or user-selected model
      prompt: prompts[analysisType],
      stream: false
    })
  });

  return response.json();
}
```

**Requirements:**
- Ollama installed and running locally (`ollama serve`)
- User instruction to install Ollama if not detected
- Graceful degradation if Ollama is unavailable

### 3. Enhanced File Access Methods

Provide multiple ways to access podcast data while maintaining the simplicity of the current drag-and-drop approach.

**Option A: File System Access API** (Modern Approach)
- Button to "Select Podcast Folder"
- Uses [File System Access API](https://developer.mozilla.org/en-US/docs/Web/API/File_System_Access_API)
- Grants persistent permission to read from selected directory
- "Refresh" button to re-scan folder for new transcripts
- Auto-refresh option (check for changes every N seconds/minutes)

**Benefits:**
- No need to drag files repeatedly
- Always up-to-date with new transcripts
- More intuitive for regular users
- Better for watching a folder over time

**Limitations:**
- Requires Chromium-based browsers (Chrome, Edge, Brave)
- Not supported in Firefox or Safari yet
- Needs user permission grant

**Option B: File Input with Directory Selection**
- Fallback for browsers without File System Access API
- `<input type="file" webkitdirectory>` for folder selection
- Manual refresh button required (no persistent access)
- More compatible but less convenient

**Proposed Hybrid UX:**

```
┌─────────────────────────────────────────────────────┐
│  Apple Podcast Transcript Viewer                   │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Choose how to load your transcripts:              │
│                                                     │
│  ┌──────────────────┐  ┌──────────────────┐        │
│  │  Select Folder   │  │  Drag & Drop     │        │
│  │                  │  │                  │        │
│  │  Pick the Apple  │  │  Drag the entire │        │
│  │  Podcasts folder │  │  folder here     │        │
│  │  and keep it     │  │                  │        │
│  │  synced          │  │  Quick one-time  │        │
│  │                  │  │  access          │        │
│  │  [Select Folder] │  │  [Drop Zone]     │        │
│  └──────────────────┘  └──────────────────┘        │
│                                                     │
│  [ ] Auto-refresh every 5 minutes                  │
│  [Refresh Now]                                      │
└─────────────────────────────────────────────────────┘
```

**Implementation Strategy:**
1. Keep existing drag-and-drop as default/primary method
2. Add "Select Folder" button for persistent access
3. Feature detection: show File System Access API option only if supported
4. Store folder handle in IndexedDB for future sessions
5. Add refresh button when folder is selected
6. Optional auto-refresh with configurable interval

**Additional UX Enhancements:**
- Show "last updated" timestamp
- Visual indicator when new transcripts are found
- Notification badge for new episodes since last check
- Search/filter functionality across all transcripts
- Favorite/bookmark specific episodes

**Browser Compatibility:**
```javascript
// Feature detection
if ('showDirectoryPicker' in window) {
  // Use File System Access API
  const dirHandle = await window.showDirectoryPicker();
  // Store handle in IndexedDB for persistence
} else if ('webkitdirectory' in HTMLInputElement.prototype) {
  // Fallback to directory input
  <input type="file" webkitdirectory>
} else {
  // Keep only drag-and-drop
}
```

## Local Development

Loading files doesn't work just by opening from `file://` in your browser due to CORS restrictions. Instead, use Python to set up a local server:

```bash
cd apple-podcast-transcripts
python3 -m http.server
```

Then visit http://localhost:8000/ in your browser.

## Credits

Shoutout to [@mattdanielmurphy](https://github.com/mattdanielmurphy) and his [apple-podcast-transcript-extractor repo](https://github.com/mattdanielmurphy/apple-podcast-transcript-extractor) which provided initial inspiration for this project.

## License

This project is open source. Feel free to use, modify, and distribute as needed.

## Contributing

Found a bug or have a feature request? Please [create an issue on GitHub](https://github.com/dado3212/apple-podcast-transcripts/issues).
