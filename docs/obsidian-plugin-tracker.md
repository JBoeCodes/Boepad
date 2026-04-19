# nodepad → Obsidian plugin — project tracker

**Status:** brainstorming, paused mid-flow.
**Last updated:** 2026-04-18
**Owner:** Jameson (personal project, not Propark work)

This file captures the running context of an in-progress design conversation so the next session can resume without re-deriving the framing. It is not a spec. The spec will live at `docs/superpowers/specs/YYYY-MM-DD-nodepad-obsidian-plugin-design.md` once brainstorming completes.

## Motivating ask

> "I want to turn this tool into something that is linked to my Obsidian and connects my entire vault / actively finds new connections etc."

## Framing decision

The original ask framed this as "turn nodepad into something that connects to Obsidian." That framing was pushed back on because much of what nodepad would provide against a vault is already native or plugin-served in Obsidian:

- Graph view (core) — force-directed vault graph, same shape as nodepad's graph mode
- Backlinks + tags (core) — explicit connection layer
- Canvas (core) — spatial placement of notes without AI
- Dataview — programmatic vault queries
- Smart Connections / Copilot / Smart Second Brain plugins — embedding-based "related notes" and chat-over-vault

What nodepad uniquely adds on top of Obsidian:

1. **Auto-classification into 14 content types** (claim, question, idea, task, entity, quote, reference, definition, opinion, reflection, narrative, comparison, thesis, general) with colored accents and body treatments (blockquote, italic, checkbox, confidence bar, muted).
2. **Live AI annotations** rendered *underneath the note itself*, not on-demand in a chat panel — a 2-4 sentence augmentation (counter-argument, adjacent framework, key tension, logical implication) that is not a summary.
3. **Ghost notes / emergent thesis** — a 15-25 word cross-category synthesis that bridges tensions across a set of notes, solidifiable into a real note or dismissable.

Three paths were offered:

- **A.** Obsidian plugin that ports those three mechanics into the real vault — inherits Obsidian's graph/backlinks/search for free.
- **B.** Keep nodepad as a separate canvas that *reads* the vault as a file source (companion lens, not replacement).
- **C.** Scope down hard: pick the single mechanic most wanted and build only that.

**Decision:** Path **A** — Obsidian plugin.

## Known constraints and context

- **Personal project.** Not Propark. Not on company time / Railway / GCP. Timing unconstrained by the Dan Martino 2026-04-22 demo.
- **Vault is large.** `/mnt/projects/Second-brain` has thousands of markdown files spanning Propark work, personal, homelab, game-library curation, daily notes. Per-note enrichment on a full-vault batch is cost-prohibitive; any classification/annotation flow needs to be opt-in per-note or targeted (recent notes, current focus, a single folder).
- **Vault is the source of truth.** Plugin should not own data. Annotations and classifications must be stored inside markdown (frontmatter, inline blocks, or a sidecar), never in a separate plugin DB that can drift from the vault.
- **AI provider pattern is already set.** `lib/ai-settings.ts` already supports OpenRouter / OpenAI / Z.ai with per-provider key storage and `customBaseUrl`. The plugin can reuse the same mental model.
- **Existing nodepad UI stack (React 19, Next.js 16, Tailwind v4, Framer Motion, D3) does not port cleanly to Obsidian plugins.** Obsidian plugins run inside Obsidian's Electron process as TypeScript modules with access to the Obsidian API. Some React is feasible inside a plugin view, but Next.js / Tailwind v4 / server routes are not. The reusable assets are the prompt engineering, the JSON schemas, the content-type taxonomy, and the parsing logic.

## Open questions (brainstorm will resume here)

1. **Scope of "connects my entire vault."** Real-time enrichment on every note? On-demand per-note? Batch against a selected folder/tag? Live ghost-thesis panel that watches the active note + its neighbors?
2. **Where annotations live.** Frontmatter fields? A fenced code block appended to the note? A sidecar `.ann.md` file? Obsidian-native callouts? Each choice has different blast radius on the user's markdown.
3. **Connection discovery.** Lean on Obsidian's existing backlinks/tags, add AI-inferred-connection suggestions as a separate layer, or replace the graph view entirely?
4. **Cost model.** Full-vault enrichment at OpenAI/OpenRouter rates against thousands of notes is real money. What's the budget ceiling, and what's the triggering heuristic to keep costs bounded?
5. **Visual surface.** Sidebar panel? Inline rendering inside the note? Replacement of the Canvas view? Modal over the graph view?

## What has NOT been decided

- The exact set of mechanics to port (all three, or a subset).
- Storage format for annotations / classifications.
- Whether the plugin ships with the nodepad-style canvas view at all, or whether the canvas stays in the standalone web app and the plugin is annotation-only.
- Visual companion (browser-based mockup tool) was offered but not yet accepted.

## Next step

Resume `superpowers:brainstorming` from task #2 (offer visual companion) or #3 (clarifying questions) in the next session. The five open questions above are the clarifying-question queue.
