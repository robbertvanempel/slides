# Slides

A Codex skill/plugin for creating stunning HTML presentations — from scratch or by converting PowerPoint files. The core `SKILL.md` can also be read by other coding agents with filesystem and shell access.

## What This Does

**Slides** helps non-designers create beautiful web presentations without knowing CSS or JavaScript. It uses a "show, don't tell" approach: instead of asking you to describe your aesthetic preferences in words, it generates visual previews and lets you pick what you like.

### Demo

This short walkthrough was made with Slides itself. It shows the generated presentation experience, explains the workflow, and highlights the presenter controls that are included in every deck.

https://github.com/user-attachments/assets/11caf144-19dc-4cbc-9408-4cc191692e25

[Watch the demo on YouTube](https://youtu.be/NbtklKdjwnM).

### Key Features

- **Zero Dependencies** — Single HTML files with inline CSS/JS. No npm, no build tools, no frameworks.
- **Visual Style Discovery** — Can't articulate design preferences? No problem. Pick from generated visual previews.
- **PPT Conversion** — Convert existing PowerPoint files to web, preserving all images and content.
- **Anti-AI-Slop** — Curated distinctive styles that avoid generic AI aesthetics (bye-bye, purple gradients on white).
- **Bold Template Pack** — Optional design-forward templates bundled locally, loaded progressively so safe presets still work as the default fallback.
- **Local Presenter Runtime** — Every delivered deck includes keyboard shortcuts, fullscreen behavior, a slide-count jump, and an `E` slide menu for visibility, titles, deletion, and user-added text slides.
- **Production Quality** — Accessible, fixed 16:9, well-commented code you can customize.

## Installation

### Codex Plugin Installation

This repo also includes Codex plugin metadata. In the Codex app, install it from the plugin interface when using this repo as a plugin source. In Codex CLI, open the plugin interface with:

```bash
/plugins
```

Search for `slides`, select it, and follow the prompts.

### Codex Skill Installation

Install directly into your Codex skills directory:

```bash
# Create the skill directory
mkdir -p ~/.codex/skills/slides/scripts

# Copy the user-facing skill files
cp SKILL.md STYLE_PRESETS.md viewport-base.css html-template.md presenter-runtime.md animation-patterns.md ~/.codex/skills/slides/
cp -R bold-template-pack ~/.codex/skills/slides/
cp scripts/extract-pptx.py scripts/deploy.sh scripts/export-pdf.sh ~/.codex/skills/slides/scripts/
```

Or clone directly:

```bash
git clone https://github.com/robbertvanempel/slides.git ~/.codex/skills/slides
```

Then use it by asking Codex to use the Slides skill, or by naming `$slides` when your Codex environment supports explicit skill triggers.

### Other Coding Agents

Agents such as Kimi Code, OpenCode, Gemini CLI, or other local coding assistants can use the same core skill. The simplest path is to send the agent this GitHub repo link and ask it to use the Slides skill:

```text
https://github.com/robbertvanempel/slides
```

If the agent can read GitHub repos or browse files, it should start from `SKILL.md` and load only the referenced support files it needs:

- `STYLE_PRESETS.md`
- `viewport-base.css`
- `html-template.md`
- `presenter-runtime.md`
- `animation-patterns.md`
- `bold-template-pack/`
- `scripts/`

Some agents can also install the skill for you if they have filesystem access and a known local skills directory. If not, they can still follow `SKILL.md` directly for the current session.

The Codex plugin metadata gives Codex a native plugin install surface. Other agents usually read `SKILL.md` directly or use their own local skills directory.

## Usage

### Create a New Presentation

```text
Use the Slides skill to create a pitch deck for my AI startup.
```

If your Codex environment supports explicit skill triggers, you can also mention `$slides`.

The skill will:

1. Ask about your content (slides, messages, images)
2. Generate 3 visual style previews for you to compare, inferring the vibe from your brief unless you already named one
3. Let you pick the visual direction
4. Create the full presentation in your chosen style
5. Open it in your browser

### Convert a PowerPoint

```text
Use the Slides skill to convert my presentation.pptx to a web slideshow.
```

The skill will:

1. Extract all text, images, and notes from your PPT
2. Show you the extracted content for confirmation
3. Let you pick a visual style
4. Generate an HTML presentation with all your original assets

## Included Styles

### Dark Themes

- **Bold Signal** — Confident, high-impact, vibrant card on dark
- **Electric Studio** — Clean, professional, split-panel
- **Creative Voltage** — Energetic, retro-modern, electric blue + neon
- **Dark Botanical** — Elegant, sophisticated, warm accents

### Light Themes

- **Notebook Tabs** — Editorial, organized, paper with colorful tabs
- **Pastel Geometry** — Friendly, approachable, vertical pills
- **Split Pastel** — Playful, modern, two-color vertical split
- **Vintage Editorial** — Witty, personality-driven, geometric shapes

### Specialty

- **Neon Cyber** — Futuristic, particle backgrounds, neon glow
- **Terminal Green** — Developer-focused, hacker aesthetic
- **Swiss Modern** — Minimal, Bauhaus-inspired, geometric
- **Paper & Ink** — Literary, drop caps, pull quotes

### Bold Template Pack

The skill also includes 34 optional bundled bold design systems, such as **Neo-Grid Bold**, **Editorial Tri-Tone**,
**Creative Mode**, **Broadside**, **Signal**, and **Vellum**.

During style discovery, the preview set is:

- 1 safe preset from `STYLE_PRESETS.md`
- at least 1 bold template option from `bold-template-pack/selection-index.json`
- 1 wildcard option, either another bold template or a self-generated custom design

The agent reads the compact bold template index first, then loads only the
shortlisted candidates' small `preview.md` cards for title-slide previews. It
loads the full `design.md` for exactly one bold template only after the user
picks that template for the final deck. If the user picks a custom wildcard,
the agent expands that preview's own CSS and layout system into the full deck.

## Bold Template Gallery

The bold template pack is bundled locally in `bold-template-pack/`. The skill reads `selection-index.json` first, loads only the shortlisted `preview.md` files for style discovery, and reads one selected `design.md` when generating the final deck. The generated deck must not depend on remote template screenshots or source-repo URLs.

## Presenter Runtime

Every generated deck includes the local presenter runtime described in `presenter-runtime.md`. That means the delivered HTML has keyboard navigation, fullscreen behavior, an editable slide-count jump, media controls, and an `E` menu for editing the visible presentation in the browser.

Keyboard shortcuts:

- Arrow keys, Page Up/Down, Home/End, and Space for navigation.
- `E` opens or closes the slide menu.
- `F` toggles fullscreen.
- `M` toggles mute when the deck contains media.

The menu supports:

- `All`: show all non-deleted slides.
- `Selection`: present only the selected slides.
- `Save`: persist menu changes to browser `localStorage`.
- `+ Slide`: add a plain text slide after the current or previewed slide.
- Inline slide titles, row preview, delete, visibility toggles, and text-slide color, position, and size controls.

These edits are intentionally local to the browser. Static deployments should use local files, a local static server, or Vercel through `scripts/deploy.sh`; generated decks must not contain private server domains, project-specific save APIs, or unauthenticated write endpoints.

## Architecture

This skill uses **progressive disclosure** — the main `SKILL.md` is a workflow map, with supporting files loaded on-demand only when needed:

| File                      | Purpose                        | Loaded When               |
| ------------------------- | ------------------------------ | ------------------------- |
| `SKILL.md`                | Core workflow and rules        | Always (skill invocation) |
| `STYLE_PRESETS.md`        | 12 curated visual presets      | Phase 2 (style selection) |
| `bold-template-pack/selection-index.json` | Compact bold template metadata for candidate selection | Phase 2 (style selection) |
| `bold-template-pack/templates/*/preview.md` | Tiny style cards for shortlisted bold previews | Phase 2 after shortlisting |
| `bold-template-pack/templates/*/design.md` | Full design system for the selected bold template | Phase 3 after user selection |
| `viewport-base.css`       | Mandatory fixed-stage CSS      | Phase 3 (generation)      |
| `html-template.md`        | HTML structure and JS features | Phase 3 (generation)      |
| `presenter-runtime.md`    | Local controls, shortcuts, slide menu, and localStorage behavior | Phase 3 (generation)      |
| `animation-patterns.md`   | CSS/JS animation reference     | Phase 3 (generation)      |
| `scripts/extract-pptx.py` | PPT content extraction         | Phase 4 (conversion)      |
| `scripts/deploy.sh`       | Deploy to Vercel               | Phase 6 (sharing)         |
| `scripts/export-pdf.sh`   | Export slides to PDF           | Phase 6 (sharing)         |

Maintenance-only source metadata and regeneration helpers live outside the
user-facing skill package. Normal users do not need them.

This design follows agent-skill best practices: give the agent a map first,
then reveal only the specific files needed for the current choice.

## Philosophy

This skill was born from the belief that:

1. **You don't need to be a designer to make beautiful things.** You just need to react to what you see.

2. **Dependencies are debt.** A single HTML file will work in 10 years. A React project from 2019? Good luck.

3. **Generic is forgettable.** Every presentation should feel custom-crafted, not template-generated.

4. **Comments are kindness.** Code should explain itself to future-you (or anyone else who opens it).

## Sharing Your Presentations

After creating a presentation, the skill offers two ways to share it:

### Deploy to a Live URL

One command deploys your slides to a permanent, shareable URL that works on any device — phones, tablets, laptops:

```bash
bash scripts/deploy.sh ./my-deck/
# or
bash scripts/deploy.sh ./presentation.html
```

Uses [Vercel](https://vercel.com) (free tier). The skill walks you through signup and login if it's your first time.

### Export to PDF

Convert your slides to a PDF for email, Slack, Notion, or printing:

```bash
bash scripts/export-pdf.sh ./my-deck/index.html
bash scripts/export-pdf.sh ./presentation.html ./output.pdf
```

Uses [Playwright](https://playwright.dev) to screenshot each slide at 1920×1080 and combine into a PDF. Installs automatically if needed. Animations are not preserved (it's a static snapshot).

## Requirements

- A local coding agent with filesystem access and the ability to run shell commands
- Codex is required only for the native plugin install flow and explicit `$slides` skill trigger
- For PPT conversion: Python with `python-pptx` library
- For URL deployment: Node.js + Vercel account (free)
- For PDF export: Node.js (Playwright installs automatically)

## License

MIT — Use it, modify it, share it.
