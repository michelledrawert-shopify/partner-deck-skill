# Build Partner Deck

Create a new Google Slides partner presentation from the standard template by **duplicating** existing template slides — never creating new ones. Duplication preserves all visual design: backgrounds, graphics, colors, and decorative elements.

## Template
**Template ID:** `1hIgQ5F9tAhijnT_jMuqoeP4WQj7fqUkiZX40PcV6obk`
Never modify this file. Always copy it first.

**Slide reference:** Google Doc ID `1K_pqR0oLyerT8jKuej7Ne9CjB4OdjU-kcizUJlefySQ`
Before Phase 3 Step 3, read this doc using `read_file` (service: docs). It describes every template slide (layout, element count, what content it suits) and includes usage annotations in `[ NOTES ]` fields. Use these notes to guide template matching.

---

## Input Format

The user provides content — either a structured slide flow or a rough outline/prose. Either format is fine; parse it into a working slide plan before reviewing.

Each slide in the working plan should have:
- **Title** — headline text
- **Body / Content** — body text, bullets, or structured content
- **Speaker Notes** (optional)

---

## Phase 1 — Technical Accuracy + Story

**Do this first. Do not proceed to Phase 2 until the user explicitly says to.**

### Context — always assume this unless told otherwise
- **Audience:** Agency partners (peers of the PSE — technically capable, partner-aligned, not merchant-facing)
- **Presenters:** Solutions Engineers (PSEs)
- Content should be technically accurate, peer-to-peer in tone, and grounded in current Shopify product reality

### Step A — Verify all factual claims

Use available tools to check every claim in the content that asserts product behavior, feature availability, platform capability, program details, or data:

- **Shopify Dev MCP** (`learn_shopify_api` → `search_docs_chunks`) — verify feature names, API behavior, plan availability, native capabilities, checkout logic, app requirements. Call `learn_shopify_api` with the relevant API surface first (`admin`, `functions`, `liquid`, `storefront-graphql`, etc.) before searching. Flag anything that contradicts or cannot be confirmed against current docs.
- **Vault** (`vault_search_all`, `vault_get_page`, `vault_get_product`) — verify internal product claims, partner program details, merchant stats, or initiative context. Check product pages and Codex docs for accuracy.
- **Slack** (`get_messages` with `action: search`) — if the content references a recent launch, program change, or internal initiative, search Slack to confirm it's current and accurate.

For each claim you cannot verify: flag it as **"⚠ UNVERIFIED: [claim]"** and note what you searched. Do not flag experiential claims the PSE would know firsthand (a customer story, a migration they ran, a conversation they had).

### Step B — Story and narrative review

With accuracy established, assess the narrative:

- Does the deck have a clear arc — setup, insight, action?
- Does each section build on the previous one, or do they feel disconnected?
- Is the "so what" clear at the end? Would a partner leave knowing what to do next?
- Are there slides that are doing too much (split them) or too little (cut or combine them)?
- Are there obvious missing pieces — a framing slide, a proof point, a next-step CTA?
- Does the agenda match the actual sections?

### How to deliver Phase 1 feedback

Structure your output:
1. **Accuracy flags** — list any ⚠ UNVERIFIED items with context on what you couldn't confirm
2. **Story observations** — what's working, what's missing, what's out of order
3. **Suggested changes** — specific, slide-level where possible
4. **Clarifying questions** — anything that would change the content (e.g. "Is there a customer story available for the case study section?", "Is this a 30- or 60-min slot?")
5. End with: **"Ready for editorial review, or would you like to revise the content first?"**

### Loop

After delivering feedback, wait. Repeat Phase 1 as many times as needed.

Only when the user says they're ready (e.g. "move to editorial", "looks good", "next step") proceed to Phase 2.

---

## Phase 2 — Editorial + Template Fit

**Do not proceed to Phase 3 until the user explicitly says to.**

With accurate, story-sound content in hand, review it against the constraints of the template and slide format.

### What to review

**1. Text volume per slide**
- No slide should have more than ~5 bullets or ~60 words of body copy
- Flag any slide that exceeds this — suggest specific cuts, not just "trim this"
- Multi-column slides (3, 4, 5 columns) need even shorter copy per cell — flag anything over ~20 words per column

**2. Template fit**
- Does each slide's content map cleanly to an available template layout?
- Flag any slide where the content count doesn't match available templates (e.g. 6 bullets when no 6-bullet layout exists, or 7 steps when the process flow handles 7 max)
- Note where the user will need to leave blocks blank (e.g. 4 items on a 5-column slide)

**3. Flow and transitions**
- Do section dividers appear before each major section?
- Is there a cover, an agenda, and a close?
- Are the section numbers (01, 02…) in sequence and consistent?

**4. Title and label consistency**
- Are slide titles consistent in style — length, case (sentence case or title case — pick one throughout)?
- Are eyebrow labels short (2–4 words), consistent, and meaningful?
- Do any titles need shortening to fit a single line?

**5. Formatting for build**
- Bullets should use plain text, no markdown symbols
- Column content should be clearly delineated so it's unambiguous which text goes in which cell
- Any slide with speaker notes should have them clearly separated from slide body content

### How to deliver Phase 2 feedback

- List issues slide by slide
- Include the revised text where possible, not just a description of the problem
- End with: **"Ready to build, or would you like to adjust anything?"**

### Loop

Repeat Phase 2 as needed. Only when the user explicitly says to build, proceed to Phase 3.

---

## Phase 3 — Build

### Step 1 — Copy the template
Call `create_file`:
- `file_type`: `presentation`
- `copy_from_id`: `1hIgQ5F9tAhijnT_jMuqoeP4WQj7fqUkiZX40PcV6obk`
- `title`: Use the presentation title from the slide flow, or ask the user

Record the returned `file_id` — use it for every subsequent call.

### Step 2 — Examine every template slide

Call `get_structure` on the copy with `include_content: true`.

If the response is large (150k+ chars), save to a temp file and use Python to extract for each slide (by position, 1-indexed):
- `objectId`
- Layout display name (from `slideProperties.layoutObjectId` → look up in layouts list)
- Placeholder types present (TITLE, BODY, SUBTITLE, IMAGE, etc.)
- Number of text areas
- Any text content currently in the slide (to understand the visual intent)

Build a **slide catalog** — a numbered list of every template slide with its visual structure. For example:
```
Slide  1: BG - Forest Green | no text placeholders | cover/title
Slide  2: Title and body | TITLE + BODY | standard content
Slide  5: Title and two columns | TITLE + BODY + BODY | comparison
Slide 12: Big number | TITLE + BODY | stats/metrics
Slide 37: [layout] | TITLE + BODY + BODY + IMAGE | product with features + screenshot
Slide 46: [layout] | TITLE + IMAGE + BODY | admin screenshot + caption
...
```

The goal is to understand every slide visually — how many columns, whether there's an image slot, visual weight, how much text it can hold — so you can make intelligent matching decisions in Step 3.

### Step 3 — Match each new slide to the best template slide

For each slide in the user's flow:

1. **Understand the content purpose**: What will be shown? A product with features? A process with steps? A quote? A comparison? A screenshot from admin? A section break? A stat?

2. **Pick the single best-matching template slide** by asking:
   - Does the visual structure fit? (e.g. 4 features → slide with 4 content areas; admin screenshot → slide with image placeholder beside text)
   - Does the visual weight match the moment? (section openers need more visual impact than detail slides)
   - Is this slide a good fit for the amount of content?

3. **Enforce variety**: Track which template slides you've already chosen. Do not use the same slide more than 2–3 times across the deck unless there is truly no better alternative. A real deck uses the full range of the template — different layouts, different visual weights, different proportions.

4. **Fallback — no good match**: If no template slide fits the content well, choose the "Blank — No logo" slide (or the most generic available) and include `⚠ NEEDS GRAPHICS` as the first line of the body text. This is preferable to forcing content into a slide that doesn't fit it. Record this slide for the output summary.

Record your plan: `{position, chosenTemplateSlideId, chosenTemplateSlideNumber, content, needsGraphicsFlag}`

**Show this plan to the user before building** if the deck is more than 10 slides — a quick "Here's which template slide I'm using for each" confirmation step prevents rebuilds.

### Step 4 — Duplicate template slides
Batch ALL `duplicateObject` requests in a single `batch_workspace_operations` call — one per slide in the user's flow:
```json
{"duplicateObject": {"objectId": "<chosenTemplateSlideId>"}}
```

The same template slide may be duplicated multiple times. Each duplication is independent.

From the batch replies, collect new slide IDs in order:
```python
new_ids = [r['duplicateObject']['objectId'] for r in replies]
```

Duplicated slides are inserted immediately after their source — they are scattered throughout the presentation at this point.

### Step 5 — Reorder new slides to the front
Call `batch_workspace_operations` with a single `updateSlidesPosition` request:
```json
{
  "updateSlidesPosition": {
    "slideObjectIds": ["newSlide1", "newSlide2", ...all in desired order...],
    "insertionIndex": 0
  }
}
```
This moves all new slides to positions 0 through N-1. Original template slides are pushed to positions N onward.

### Step 6 — Delete original template slides
Call `get_structure` to get the current slide list. Original slides are now at the end.

Batch `deleteObject` requests for all original template slide IDs.

### Step 7 — Populate text content
For each new slide with text content:

**7a.** Call `get_object` with `object_type: slide` on the slide's objectId. Find all shapes with a `placeholder` property — note each placeholder's `type` (TITLE, BODY, SUBTITLE) and `objectId`.

**7b.** Build two operations per placeholder that needs content:
```json
{"deleteText": {"objectId": "<elementId>", "textRange": {"type": "ALL"}}}
```
```json
{"insertText": {"objectId": "<elementId>", "insertionIndex": 0, "text": "<content>"}}
```
Always `deleteText` before `insertText` to clear any existing template text.

Batch across multiple slides. Keep batches under ~50 operations to avoid timeouts.

**Text insertion rules:**
- TITLE → title placeholder
- Body/bullets → BODY placeholder; use `\n` for line breaks
- Two-column layouts: left column → first BODY, right column → second BODY
- Subheadings/descriptors → SUBTITLE placeholder
- **Skip IMAGE placeholders — leave blank**
- No shapes or text boxes outside existing placeholders
- No formatting changes (font, size, color, bold, alignment)

**BG slides (Cover, Section Dividers):** These slides have no API-accessible text placeholders. Duplicate them for correct visual design. Note in the final output which slides need manual text entry.

**Fallback slides:** For slides flagged `needsGraphics`, insert `⚠ NEEDS GRAPHICS` as the first line of the body text, followed by the slide content.

### Step 8 — Return the link
```
✓ Presentation created: https://docs.google.com/presentation/d/{file_id}/edit

Slides requiring manual text entry (BG layout — no API text placeholders):
- Slide 1: [cover title]
- Slide X: [section name]

Slides flagged for graphics work:
- Slide Y: [slide title] — content added as text; needs visual treatment
```

Ask if the user wants any slides adjusted.

---

## Slide Selection Principles

Do not use a fixed layout name → slide mapping. Instead, reason from content to visual structure:

**How many distinct content areas does this slide need?**
- 1 idea, high visual weight → single-column, large type, or background slide
- 1 idea + supporting image → image + caption layout
- 2 topics side by side → two-column layout
- 3–4 features or steps → multi-item grid or two-column with grouped bullets
- A stat or number → big number layout
- A quote or executive statement → full-bleed background layout

**What is the visual purpose of this moment in the deck?**
- Opening or closing → cover/section divider
- Transition between chapters → section divider or opener with subtitle
- Proof point or reference → lighter visual treatment, more text
- Concept or framework → more structured, visual layout
- Screenshot-dependent content → slide with image placeholder

**Variety check — use the full template:**
Scan your plan before building. If more than 3 slides in a row use the same template slide, revise. A well-varied deck uses cover slides, dividers, single-column, two-column, image+text, stat, and featured-quote slides across its length.

**When nothing fits:**
Use the most generic available slide. Add `⚠ NEEDS GRAPHICS` as the first line. This signals to the user that a human design pass is needed for this slide — which is far better than forcing content into a slide that misrepresents it.

---

## Per-Slide Usage Rules

These rules override general matching logic. Read them before finalizing template selections in Step 3.

### Deck structure — always apply
- **Slide 13** — Always use for the Agenda slide. It should be the second slide, immediately after the cover. Formatted for 5 agenda items; if more are needed, add only the first 5 and note that the user should complete the rest manually.
- **Slides 8 and 9** — Use for every section divider / chapter break, numbered sequentially (01, 02, 03…). Use Slide 8 for 1-line section titles, Slide 9 for 2-line titles (less common). Each agenda item gets one.
- **Slides 5 and 7** — Never use in a build. Keep them in the deck but do not populate text.

### Cover slide — text rules
- **Slide 6** — Fill the presentation title only. Do not fill in the presenter name, role, title, or company — leave that block blank for the user. If the title exceeds 2 lines, paste it anyway and note that the user will adjust.

### Strict "only use when" constraints
- **Slide 11** — Only if the content would support a coordinating image. Never use otherwise.
- **Slide 12** — Only if a product image makes sense alongside the bullets. Never use otherwise.
- **Slide 22** — Only when the content includes 3 or 4 data points. Do not use for general prose.
- **Slide 42** — Only when the content is a case study. Not for general quotes.
- **Slide 21** — Only for 3 or 4 data points (large stats/numbers). No other use.
- **Slide 23** — Only when presenting a single statistic. No other use.
- **Slide 24** — Only for 3 or 4 data points with comparison. No other use.
- **Slide 25** — Only when presenting exactly 1 data metric. Never for anything else.
- **Slide 27** — Only for growth-focused content. Not for general financial data.
- **Slide 28** — Only for growth-focused content. Not for general timelines.
- **Slide 31** — Only when discussing a hierarchy. Not for general process flows.
- **Slide 46** — Only when the supporting visual is a computer-frame screenshot (e.g. Shopify Admin, a website). Do not use for general images.
- **Slide 47** — Only when text is minimal. Also valid as a closing slide or single call-to-action.

### Flexible item counts — ignore extra blocks
When content has fewer items than the layout supports, leave unused blocks blank (do not fill or delete them):
- **Slide 15** — Supports 4 or 5 items. If 4, leave the 5th block blank.
- **Slide 16** — Supports 4 or 5 sequential steps. If 4, leave the 5th block blank.
- **Slide 17** — Supports 4 or 5 items. If 4, leave the 5th block blank.
- **Slide 18** — Supports 3 or 4 items. If 3, leave the 4th block blank.
- **Slide 34** — Supports 4 to 6 items. Leave extra blocks blank.

### Choosing between similar slides
- **Slide 15 vs. Slide 17** (both 5-column): Use Slide 15 for short, punchy statements per column. Use Slide 17 when content per column is longer and needs more room.
- **Slide 14 vs. Slide 15/17**: Use Slide 14 only when the content has exactly 4 points — it has no unused-block flexibility.
- **Slide 26** (3 financial figures): Also valid for a "good / better / best" framing with 3 tiers.
- **Slide 45** (2 images): Use when content describes two distinct experiences — e.g. two parts of a checkout flow, two different user journeys.

---

## Hard Rules
- **Never** modify the template file (`1hIgQ5F9tAhijnT_jMuqoeP4WQj7fqUkiZX40PcV6obk`)
- **Never** use `createSlide` — always use `duplicateObject`. Creating new slides loses all visual design.
- **Never** change fonts, colors, sizes, line spacing, or any text formatting via the API
- **Never** generate or insert images — leave all image placeholders blank
- **Never** add shapes, text boxes, or elements outside existing placeholders
- Always `deleteText` (type: ALL) before `insertText` on duplicated slides to clear template content
- If a slide has no defined text, still duplicate with the correct layout — skip the text steps
- Do not use the same template slide more than 2–3 times unless there is genuinely no better alternative
