# PRD — Almost Human Hackathon Wheel

## 1. Overview
A playful, frontend-only “spin-the-wheel” app for the Aleph **Almost Human Hackathon**.

Participants trigger **three sequential spins**:

1. **Topic**
2. **Tech**
3. **Constraint**

After all three are selected, the system calls **GPT-5.1**, which streams a short, fun project idea onto the screen.

All configuration (lists, context, API key) is managed client-side via an **admin screen** with import/export.  
No backend. Everything persists in **localStorage**.

The UI should **vibe-match Almost Human** branding (dark, neon, playful tech aesthetic).

## 2. Core User Flow
1. Presenter opens the app on a large display.
2. Presses **Start**.
3. Hits **Spin Wheel**.
4. **Wheel 1** spins → stops → reveals Topic.
5. Automatically starts **Wheel 2** → reveals Tech.
6. Automatically starts **Wheel 3** → reveals Constraint.
7. App auto-generates LLM prompt and:
   - Calls GPT-5.1  
   - Streams idea into the UI (typing animation)
8. Shows:
   - **Regenerate Idea**  
   - **Spin Again**

## 3. Wheel Behavior

### Visual
- Three wheels visible at all times.
- Only one spins at a time (left → middle → right).
- Slices generated dynamically from list length (5–20 items).
- Slices show **display text only**.
- Smooth spin → decelerate animation.
- Optional sound effects:
  - ticking during spin  
  - ding at stop

### Reveal
- After each wheel stops: highlight the selected item.
- After the third wheel: trigger LLM generation automatically.

## 4. LLM Integration

### Model
- OpenAI **GPT-5.1** — Chat Completions API.
- API key is stored in admin config and localStorage.

### Prompt Structure

#### System Prompt
```
You are a creative but concise hackathon ideation assistant.
Generate short, funny, surprising idea pitches that combine three elements:
- a Topic 
- a Technology 
- a Silly Constraint

Keep the tone playful but smart.
Output only the idea text, no formatting instructions.
```

#### User Prompt Template
```
<context>
Topic: "{{topic_display}}"
Topic Description: "{{topic_description}}"

Tech: "{{tech_display}}"
Tech Description: "{{tech_description}}"

Constraint: "{{constraint_display}}"
Constraint Description: "{{constraint_description}}"

Write ONE short idea (3–6 sentences max) that creatively combines all three elements.
Make it fun, energetic, and demo-ready.
```

### Behavior
- Tokens stream to UI via **typing animation**.
- **Regenerate Idea** → re-run the LLM with same selections.
- **Spin Again** → reset wheels + clear output.

## 5. Admin Panel

### Fields
- Topics  
- Techs  
- Constraints  
- Context Text (plain text)  
- API Key  

### Format
Each category is a single multiline text box:  

```
Display Text | Description Text
Display Text | Description Text
...
```

### Validation
- Warn on malformed lines (no `|`).
- Skip malformed entries.

### Import/Export
- JSON file containing:
  - topics[]
  - techs[]
  - constraints[]
  - context
  - apiKey

### Persistence
- All config saved to **localStorage**
- Loaded automatically on startup
- Import replaces current state
- Export downloads current state

## 6. Main Screen UI

### Layout
- Three wheels centered horizontally.
- “Spin Wheel” button below.
- After each spin: show the selected item in large, readable text.
- After all spins:
  - Show Topic/Tech/Constraint clearly
  - Stream LLM output underneath
  - Two large buttons:
    - **Regenerate Idea**
    - **Spin Again**

### Styling
- Vibe-match “Almost Human”:
  - dark base  
  - neon accents (green/blue/pink)  
  - thin glowing borders  
  - modern sans-serif fonts  

### Presenter-Focused
- Big text  
- High contrast  
- Minimal clutter  

### Other UI Notes
- Auto-scroll to idea when generation begins.

## 7. Architecture

### Constraints
- 100% frontend-only
- No backend at all
- SPA (React recommended but optional)
- Must be smooth on a laptop with external display

### Modules
- `WheelRenderer` — canvas/SVG wheel drawing + animation
- `SpinController` — orchestrates the three spins in sequence
- `SelectionState` — stores chosen items
- `LLMService` — handles GPT-5.1 calls + streaming
- `AdminConfigStore` — localStorage + JSON import/export
- UI components: wheels, buttons, idea area, admin UI

## 8. Error Handling
- If API key missing → disable Spin Wheel with note.
- If category empty → disable the corresponding wheel.
- On LLM error → friendly retry option.
- Malformed config entries → warnings.

## 9. Performance
- Aim for smooth 60fps wheel animation.
- Default spin duration ~2–3 seconds per wheel.

## 10. Non-Goals
- No server.
- No authentication.
- No multi-user sync.
- No cloud config.

## 11. Deliverables (for implementation)
- Fully functional SPA:
  - Wheel experience
  - Admin screen
  - LLM integration + streaming
  - Config persistence
  - Import/export
  - Stage-friendly UI
