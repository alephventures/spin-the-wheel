# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a frontend-only "spin-the-wheel" hackathon idea generator for the Aleph "Almost Human Hackathon". The entire application is contained in a single HTML file (`hackathon-wheel.html`) that uses React (via CDN) with Babel transpilation for JSX.

**Key characteristics:**
- Single self-contained HTML file with embedded CSS and JSX/React code
- No build process, no npm, no backend
- All state persists in localStorage
- Integrates with OpenAI's GPT API for streaming idea generation

## Running the Application

Open `hackathon-wheel.html` directly in a browser:
```bash
open hackathon-wheel.html
# or
python3 -m http.server 8000  # Then visit http://localhost:8000/hackathon-wheel.html
```

## Architecture

The application follows a component-based React architecture within a single file:

### Main Components (in order of appearance in code)

1. **Configuration Management** (lines 27-81)
   - `DEFAULT_CONFIG`: Hardcoded default topics, techs, constraints, context prompt, and API key
   - `loadConfig()` / `saveConfig()`: localStorage persistence layer
   - Config structure: `{ topics: [], techs: [], constraints: [], context: string, apiKey: string }`
   - Each category item has: `{ display: string, description: string }`

2. **Wheel Component** (lines 83-190)
   - Canvas-based wheel rendering with dynamic slice generation
   - Animation controlled via CSS transforms and state
   - Receives: items array, spinning state, selected index, completion callback, label
   - 3-second spin animation using cubic-bezier easing
   - Draws colored slices with HSL color distribution across 360° hue spectrum

3. **HackathonWheel Main App** (lines 192-561)
   - Game state machine: `'idle' → 'spinning' → 'generating' → 'complete'`
   - Sequential wheel orchestration: Topic → Tech → Constraint → Generate Idea
   - OpenAI streaming integration with SSE (Server-Sent Events) parsing
   - Smooth scroll to idea when generation begins

4. **AdminPanel Component** (lines 563-843)
   - Edits all configuration in textarea format: `Display | Description` (one per line)
   - JSON import/export for configuration backup/sharing
   - Validation ensures at least one item per category

### State Flow

```
User clicks "Start Spinning"
  ↓
Topic wheel spins (3 seconds)
  ↓
Tech wheel spins (3 seconds)
  ↓
Constraint wheel spins (3 seconds)
  ↓
OpenAI API call streams idea tokens
  ↓
User can "Regenerate Idea" or "Spin Again"
```

### OpenAI Integration

- Model: `gpt-4o` (not GPT-5.1 as mentioned in PRD—GPT-5.1 doesn't exist yet)
- Streaming: Uses `stream: true` with manual SSE parsing
- Error handling for: 401 (bad API key), 429 (rate limit), CORS issues
- Located in `generateIdea()` function (lines 247-364)

## Common Development Tasks

### Modifying Default Configuration

Edit the `DEFAULT_CONFIG` object (lines 28-61):
- Add/remove topics, technologies, or constraints
- Update the context prompt used for LLM generation
- Note: API key is hardcoded in default config—consider removing for security

### Changing Wheel Appearance

The `Wheel` component (lines 83-190) uses HTML Canvas:
- Wheel size: 300x300px canvas
- Colors: HSL with hue distributed evenly across items
- Animation: CSS transform with 3s cubic-bezier
- Pointer: Pink triangle at top center (lines 149-158)

### Modifying LLM Prompts

Edit the `generateIdea()` function (lines 247-364):
- System prompt at line 261-267
- User prompt template at line 269-279
- Streaming parameters at line 288-297

### Styling Updates

All styles are inline React style objects. Key style locations:
- Main container gradient: line 378-385
- Admin button: line 386-400
- Title gradient text: line 406-413
- Spin button: line 423-446
- Idea display box: line 488-496

## File Structure

```
spin-the-wheel/
├── hackathon-wheel.html           # Complete application
├── Almost_Human_Hackathon_Wheel_PRD.md  # Original requirements
└── CLAUDE.md                      # This file
```

## Security Considerations

- **API Key Exposure**: The OpenAI API key is hardcoded in the default config and stored in localStorage. For production use, remove the default key and require users to enter their own.
- **CORS**: Direct OpenAI API calls from browser may face CORS restrictions. Consider using a CORS proxy or browser extension for local development.

## Design System

**Almost Human Branding:**
- Background: Dark gradient (`#0a0e27` to `#1a1f3a`)
- Primary accent: Cyan/green (`#00ff88`)
- Secondary accent: Cyan blue (`#00d4ff`)
- Tertiary accent: Magenta (`#ff00ff`)
- Purple gradient for buttons: `#667eea` to `#764ba2`
- Glowing effects: `drop-shadow` and `box-shadow` with rgba colors at 0.3-0.6 opacity
