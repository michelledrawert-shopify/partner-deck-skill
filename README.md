# Partner Deck Skill — Build Partner Deck

A Claude Code skill that turns a rough content outline into a fully built Google Slides partner presentation in one guided session.

## What it does

Runs in three phases, with a hard stop and review loop between each:

**Phase 1 — Technical Accuracy + Story**
Claude verifies all factual claims in the content against Shopify documentation (Dev MCP), Vault, and Slack before anything else. Flags unverified claims. Reviews story arc and missing pieces. Waits for your sign-off.

**Phase 2 — Editorial + Template Fit**
Reviews text volume, template layout fit, flow, title/label consistency, and whether the content is formatted cleanly for the build. Waits for your sign-off.

**Phase 3 — Build**
Copies the standard Shopify partner deck template, matches each slide to the best layout, duplicates slides, reorders, populates all text via the Slides API, and returns a link to the finished deck.

## Setup

1. **Copy the skill file** into your Claude commands folder:
   ```
   cp build-partner-deck.md ~/.claude/commands/
   ```

2. **Required MCP servers** — make sure these are configured in your Claude session:
   - Google Workspace MCP (for Slides/Docs API access)
   - Shopify Dev MCP (for technical accuracy checks in Phase 1)
   - Vault MCP (for internal product/doc lookups in Phase 1)
   - Slack MCP (for checking recent announcements in Phase 1)

3. **That's it.** No local reference files needed — the slide reference doc is hosted in Google Drive and read automatically.

## Using the skill

In any Claude Code session, paste your content (River export, Claude outline, rough notes, anything) and say:

> "Build a partner deck from this content"

Claude will run Phase 1 automatically.

## Slide reference doc

The slide reference — which describes every template layout, element count, and usage rules — lives here:

**[Partner Deck Skill — Slide Reference](https://docs.google.com/document/d/1K_pqR0oLyerT8jKuej7Ne9CjB4OdjU-kcizUJlefySQ/edit)**

If you want to suggest changes to the usage rules (the `[ NOTES ]` fields), open that doc and edit directly. Changes take effect on the next build.

## Template

The Google Slides template used for all builds:

**ID:** `1hIgQ5F9tAhijnT_jMuqoeP4WQj7fqUkiZX40PcV6obk`

Never edit the template directly. The skill always copies it — your changes would affect every future build.

## Assumptions

- **Audience:** Always agency partners
- **Presenters:** Always Solutions Engineers (PSEs)
- The skill is tuned for this context. If you're building for a different audience, note that at the start and Phase 1 will adjust its review.
