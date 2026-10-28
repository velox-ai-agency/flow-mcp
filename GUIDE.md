# flow-mcp User Guide

Comprehensive guide to using Google Flow from the command line, Claude Desktop, Codex, and Antigravity. Covers installation, all MCP tools, Google Flow functions, and the new Tools tab applets.

---

## 📦 Installation

```bash
# 1. Clone the repo
git clone https://github.com/velox-ai-agency/flow-mcp.git
cd flow-mcp

# 2. Install dependencies
npm install

# 3. Build TypeScript
npm run build

# 4. Sign in to Flow (opens Chrome window)
npm run login

# 5. Open or create a Flow project in the Chrome window

# 6. Connect to your AI assistant

# Claude Code
claude mcp add --scope user flow -- node /absolute/path/to/flow-mcp/dist/index.js

# Claude Desktop
# Add to ~/Library/Application Support/Claude/claude_desktop_config.json:
# { "mcpServers": { "flow": { "command": "node", "args": ["/absolute/path/to/flow-mcp/dist/index.js"] } } }

# Codex
# Add to ~/.codex/config.toml:
# [mcp_servers.flow]
# command = "node"
# args = ["/absolute/path/to/flow-mcp/dist/index.js"]

# Antigravity
# Add to ~/.gemini/antigravity/mcp_config.json:
# { "mcpServers": { "flow": { "command": "node", "args": ["/absolute/path/to/flow-mcp/dist/index.js"] } } }
```

**⚠️ Important:**
- **Mac**: Do NOT put the folder in `~/Desktop`, `~/Documents`, or `~/Downloads` (macOS protection: `EPERM: operation not permitted`). Use `~/flow-mcp` or similar.
- **Windows**: Keep out of OneDrive-synced folders.
- A display must stay awake — Chrome is being driven in real time.

---

## 🚀 Your First Film (Step-by-Step)

| Step | Action | MCP Tool | Notes |
|------|--------|----------|-------|
| 1 | Check session | `flow_status` | Confirms sign-in, project, credits |
| 2 | Create character | `flow_character` | Free: Flow draws portrait. e.g. `"Pip, a small lamplighter in flat cartoon style"` |
| 3 | Shoot it | `flow_generate` | Spends credits. e.g. `"3 × 10-second shots, 720p, max 15 credits each"` |
| 4 | Narrate it | `flow_narrate` | Free, any length. e.g. `"Record this line with a warm documentary voice"` |
| 5 | Cut it | `flow_assemble` | Join clips with optional music/voiceover. e.g. `"Join the three shots under the narration"` |
| 6 | Download | `flow_download` | Free including 1080p upscale. File lands in `~/flow-mcp-out/<project>/` |

---

## 🛠️ MCP Tools Reference

All tools communicate via stdio using the Model Context Protocol. In Claude/Codex/Antigravity you never type tool names — just ask in plain English. The assistant maps your request to the appropriate tool.

### Status & Control

| Tool | Example | Purpose |
|------|---------|---------|
| `flow_status` | `"Check my Flow session"` | Session state, queue, credits, plan |
| `flow_wait` | Wait for jobs, collect file paths | Answers within ~40s. Returns `{ finished, files, credits }` |
| `flow_cancel` | Cancel a queued job | Use before generation starts |

### Media Management

| Tool | Example | Purpose |
|------|---------|---------|
| `flow_assets` | List images/videos in project | Returns `FlowAsset[]` with name, kind, id |
| `flow_download` | `flow_download("clip-01.mp4")` | Download existing media. Free, includes 1080p upscale |
| `flow_trash` | `flow_trash("old-clip.mp4")` | Move to Flow's Trash (recoverable). Exact title only |
| `flow_assets` | `flow_assets()` | List all assets; usable as `asset:<title>` in generation |

### Generation

| Tool | Params | Example | Purpose |
|------|--------|---------|---------|
| `flow_generate` | `prompt`, `type`, `max_credits`, `model`, `duration`, `aspect_ratio`, `first_frame`, `last_frame`, `reference_images` | `"Turn those into three 10-second shots, 720p, no more than 15 credits each"` | Queue scenes. Reads Flow's own credit quote first |
| `flow_edit` | `asset`, `prompt`, `download_quality` | Edit an existing clip (relight, relocate, remove objects). ~20 credits for 4s |
| `flow_continue` | `from_asset`, `prompt`, `attach`, `mode` | Continue a clip from its last frame **with character/avatar attached**. Composer allows start frame **or** references, never both, so this goes through agent mode |
| `flow_agent_images` | `scenes`, `aspect_ratio`, `download_quality`, `references`, `scene_references` | Up to 50 images in parallel, 0 credits. Agent rewords prompts, matches tiles to scenes by word overlap (≥50%). Missing scenes re-asked (up to 2 rounds), then one-by-one |

### Characters

| Tool | Example | Purpose |
|------|---------|---------|
| `flow_character` | `"Create a character called Pip, a small lamplighter in a flat cartoon style"` | Free. Flow draws portrait. Use as `asset:Pip` in later generations |
| `flow_characters` | List project characters | Returns names (strips Material icon ligatures) |
| `flow_character_edit` | `name`, `change?`, `job?` | Restyle existing character in place (free). Pass `look` with full description; without it change is appended `"and this wins"` |
| `flow_character_edit` | Save portrait only (no change) | Re-saves current portrait without modification |

### Narration & Voice

| Tool | Params | Example | Purpose |
|------|--------|---------|---------|
| `flow_narrate` | `engine`, `voice`, `style`, `text`, `max_credits` | `"Record this line with a warm documentary voice"`<br>`engine: "mac"` = computer's own voices (free)<br>`engine: "gemini"` = Google AI Studio (needs key) |
| `flow_voices` | — | Lists available narrator voices + whether Google key is set |
| `flow_transcribe` | — | Hear words in any video/audio on your computer, with each word's start/end time (local whisper.cpp, free) |

### Captions

| Tool | Params | Example | Purpose |
|------|--------|---------|---------|
| `flow_captions` | `text`, `style`, `language`, `position`, `size`, `letters` | `"Burn spoken-word captions into my video"`<br>Styles: `bold`, `highlight`, `reveal`, `clean`, `boxed`<br>Languages: `en`, `es`, `auto` (detect)<br>Position: `bottom`, `middle`, `top`, or number 0–100 |
| `flow_transcribe` | — | Hear words with timestamps; fix misheard words then call `flow_captions` |

### Techniques & Techniques

| Tool | Example | Purpose |
|------|---------|---------|
| `flow_techniques` | — | Lists 39 film-technique presets (camera moves, product shots, transitions, image commands). Pass `id` as a scene's `technique` param |
| `flow_techniques` | `technique: "dutch_tilt"` | Apply a technique to a scene's prompt |

### Tools Tab (Applets Marketplace)

| Tool | Params | Example | Purpose |
|------|--------|---------|---------|
| `flow_tools_list` | `category`, `search` | `flow_tools_list({ category: "Templates" })` | Lists 34 Templates, 34 Community, 6 My Tools. Filter by name/description/author |
| `flow_tool_open` | `name` | `flow_tool_open({ name: "Simple Sketch" })` | Opens appet by exact name, returns iframe URL where applet runs in sandbox |
| `flow_applet_run` | `name`, `prompt`, `tool`, `model`, `wait_seconds` | `flow_applet_run({ name: "Simple Sketch", prompt: "A cute cat sitting on a windowsill", tool: "brush", model: "FAST", wait_seconds: 90 })` | **New**: Drives sketch-to-image applets (Simple Sketch) end-to-end: selects model→tool→draws canvas stroke→types prompt→clicks Generate→waits for completion. Returns `{ name, url, frameUrl }` |

#### Simple Sketch (sketch-to-image) Walkthrough

```javascript
// 1. Open Tools tab and find Simple Sketch
flow_tool_open({ name: "Simple Sketch" })
// → returns: { name: "Simple Sketch", url: "https://flow.google.com/.../tool/...?mode=APP&fromViewSource=tools" }

// 2. Run the applet with a prompt and model
flow_applet_run({
  name: "Simple Sketch",
  prompt: "A cute cat sitting on a windowsill",
  tool: "brush",         // pencil, eraser, rectangle, circle, text, upload, undo, redo
  model: "FAST",         // FAST (quick) or PRO (higher quality)
  wait_seconds: 90       // max wait; generation completes within this time
})
// → returns: { name: "Simple Sketch", url: "...", frameUrl: "blob:..." }
// The canvas stroke is drawn automatically, prompt is submitted, generation completes
```

#### Available Tools in Simple Sketch (Material icon names)

| User-Friendly | Material Icon | Usage |
|--------------|--------------|-------|
| `pencil` | `edit` | Edit |
| `brush` | `edit` | Draw/paint |
| `eraser` | `delete` | Erase |
| `move` | `near_me` | Move selection |
| `rectangle` | `rectangle` | Draw rectangle |
| `circle` | `circle` | Draw circle |
| `text` | `title` | Add text label |
| `upload` | `add_photo_alternate` | Upload reference image |
| `undo` | `undo` | Undo last action |
| `redo` | `redo` | Redo last undone action |

### Tools Tab in the Studio Panel

The Studio panel (http://127.0.0.1:8787) also has a **Tools** tab:

- **Browse**: Load Templates / My Tools / Community
- **Search**: Filter applets by name/description/author
- **Open**: Open a specific applet, shows iframe URL
- **Run**: For Simple Sketch — picks model, tool, draws stroke, types prompt, clicks Generate

The panel ships with the repo but is not started for you: run `npm run studio` when you want it.

---

## 🌐 Google Flow Functions (via Web UI)

These are driven through the Flow web interface (flow.google.com), not directly as MCP tools, but the guide covers how to use them:

### Generation

- **Text → Video**: Open a project → Composer → type prompt → `Start generation` (or Enter). Reads credit quote first.
- **Image → Video** (first/last frame): Set `first_frame` / `last_frame` images → Composer → generate. Composer allows one or the other, never both.
- **Reference images**: Upload as `asset:<title>` → add as `reference_images` in generation.

### Edit

- Open a video tile → `/edit/<uuid>` → type description → `Start generation`. ~20 credits for 4s clip. No quote shown beforehand.

### Continue from Last Frame

- Composer cannot do start frame **and** character together. Use **agent mode** (`flow_continue`) which goes through Flow's agent mode and handles both.

### Narration

- **Computer voices** (`say` on Mac, built-in Windows voices): Free, any length.
- **Google voices**: Get free key from [AI Studio](https://aistudio.google.com/apikey) → paste in Studio → Voice → Google AI Studio key → Save key. Checked with Google before saving, stored only on your computer.

### Scenes

- Each scene is its own tile. Click → `/project/<id>/scene/<uuid>` → `Download scene` → one stitched mp4.
- Or use `flow_assemble` to join clips locally.

### Captions

- Local whisper.cpp hears words on your computer, nothing uploaded.
- First call downloads model (148 MB English, 574 MB other languages).
- Styles: `bold` (capitals), `highlight`, `reveal`, `clean`, `boxed`.
- Languages: ~100 including ja, zh, th, arabic. `auto` detects.
- Position: bottom/middle/top or exact height (slider). Size: slider 50–180%.
- Letters: `as_said`, `capitals`, `lowercase`.
- Break at commas/sentence ends. `text-wrap:balance` stops lone word on line two.

---

## ⚠️ Behavior & Settings

| Aspect | Detail |
|--------|--------|
| **One generation at a time** | Random 25–70s pause between them. invisible reCAPTCHA drives real interface at human pace. |
| **Credit cap** | Server reads Flow's own quote before each generation. Skips if over `max_credits` (default 25). |
| **Language** | Any Google account language works. Server adds `?hl=en` to Chrome address. Prompts can be any language. |
| **Environment vars** | `GEMINI_API_KEY`, `FLOW_MCP_PORT` (8787), `FLOW_MCP_OUTPUT`, `FLOW_MCP_HOME`, `FLOW_MCP_CDP_PORT` (9333), `FLOW_MCP_CHROME`, `FLOW_MCP_PAUSE_MIN_S`, `FLOW_MCP_PAUSE_MAX_S` |
| **Updating** | `git pull`, `npm install`, `npm run build`. Fully quit and reopen your assistant app. |

---

## 🚨 If Something Goes Wrong

| Symptom | Cause | Fix |
|---------|-------|-----|
| Mac: `EPERM: operation not permitted` | Folder in protected location | Move out of `~/Desktop`, `~/Documents`, `~/Downloads` |
| `"Not signed in"` or `"no project open"` | Chrome not signed in / no project open | Run `npm run login`, sign in, open/create a Flow project |
| Clip generated but no file arrived | Media still in Flow | Use `flow_download` — never re-generate and pay twice |
| Everything times out | Chrome closed / machine slept | Reopen Chrome with `npm run login` |
| `flow_status` hangs with `detailed:true` | Account panel slow to open | Use `detailed:false` (default) |

---

## 📦 Output Location

All finished files land in:

```
~/flow-mcp-out/<project>/
├── scene-01.mp4
├── scene-02.jpeg
├── clip-01.mp4
├── narration-01.m4a
└── _cast/<name>.jpeg  (character portraits)
```

---

## 📜 License & Risk

This automates Flow's web interface. It can break whenever Google changes that interface. Automated use may be against Google's terms of service. Use on your own account, at your own risk.

---

### Need Help?

- Run `flow_status` to check your session state
- Open the Studio panel: `npm run studio` then visit http://127.0.0.1:8787
- Check the [Flow UI map](https://github.com/velox-ai-agency/flow-mcp#flow-ui-map-verified-2026-09-19) in the repo docs
- Open issues on the GitHub repo for bug reports

---

*Guide generated from flow-mcp source code and verified against the live Flow UI (October 2026).*