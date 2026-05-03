---
name: ad-image
description: Generate scroll-stopping Instagram ads (1080×1080) for your business using Claude Code + Higgsfield. Bakes in a proven conversion-focused template — heavy display serif headline, monospace terminal-style accent, dual color palette, floating dark proof card, pose-aware portrait placement, safe zones for cropping, branded CTA pill. On first run, walks you through a one-time setup that captures your brand colors, fonts, portraits, logo, and voice. After that, every `/ad-image` invocation generates 4 layout variations using your brand profile. Trigger via `/ad-image` or whenever the user asks to make Instagram ads for their business.
---

# Ad Image Skill

This skill produces conversion-driven Instagram ad images for any business. The template was developed and refined across dozens of iterations and is now battle-tested. Once you finish the one-time setup, generating new ads takes ~3 minutes per batch.

---

## First-time setup

If `~/.config/ad-image/brand-profile.json` does not exist, run the setup flow before doing anything else.

### Setup wizard questions (ask in order)

1. **What's your business name?** (e.g. "AI Builders", "Brewlab Coffee", "TenFoldGrowth")
2. **One-line offer.** What do you sell? (e.g. "I help non-technical people automate their business with Claude Code.")
3. **Target audience.** Who's this for in one sentence? (e.g. "Solo entrepreneurs who want AI to do their work for them.")
4. **Voice tone.** Pick one or describe: punchy/direct, professional, playful, editorial, contrarian, friendly.
5. **Brand colors.** Ask for hex codes:
   - **Primary (background dominant):** main canvas color
   - **Accent (focal element color):** what their eye lands on first
   - **Type color:** what most text uses
   - If they don't know, offer to extract from their website — ask for the URL and pull dominant colors via `tavily-extract` or by reading the homepage CSS.
6. **Optional secondary palette** (for variation). Same three colors, different vibe. Skip if they don't want one.
7. **Display font.** Default is Fraunces Black (free, distinctive serif). Other recommendations: Tiempos Headline, Instrument Serif, PP Editorial New, GT Sectra. Ask if they want to override.
8. **Accent font.** Default is JetBrains Mono Bold. Other options: Geist Mono, IBM Plex Mono, Berkeley Mono.
9. **Portrait folder path.** Absolute path to a folder of transparent-background portrait PNGs of the person who'll appear in the ads. (If they don't have any, link them to https://www.remove.bg/ or guide them to use Photoshop's Subject Select + Mask.) Examples include pointing-right, pointing-left, neutral chin pose, thumbs-up, etc.
10. **Logo or mascot path** (optional). Absolute path to a small brand mark or mascot PNG that'll appear in the top-right corner of every ad.
11. **Output folder.** Where should generated ads be saved? Default: `~/Documents/ad-image-output/`. Approved keepers go to a `Keepers/` subfolder.
12. **Image generator path.** Pick one:
    - `higgsfield` (recommended — uses `gpt_image_2` for best typography rendering)
    - `openai` (direct OpenAI API — uses `gpt-image-1`, requires `OPENAI_API_KEY` env var)

### After collecting answers

Write the profile to `~/.config/ad-image/brand-profile.json`:

```json
{
  "brand": {
    "name": "string",
    "offer": "string",
    "audience": "string",
    "voice_tone": "string"
  },
  "design": {
    "palette_a": {
      "background": "#hex",
      "accent": "#hex",
      "type": "#hex",
      "name": "string e.g. 'Dark dominant'"
    },
    "palette_b": {
      "background": "#hex",
      "accent": "#hex",
      "type": "#hex",
      "name": "string e.g. 'Light editorial'"
    },
    "display_font": "Fraunces Black",
    "accent_font": "JetBrains Mono Bold"
  },
  "assets": {
    "portrait_folder": "absolute path",
    "logo_path": "absolute path or null"
  },
  "output": {
    "save_folder": "absolute path",
    "keepers_folder": "absolute path"
  },
  "generator_path": "higgsfield"
}
```

Confirm the path is created with `mkdir -p ~/.config/ad-image && touch ~/.config/ad-image/brand-profile.json` before writing.

After writing the file, briefly summarize back what you saved and tell the user they can edit the file directly any time.

---

## Generation flow (after setup is complete)

1. **Read** `~/.config/ad-image/brand-profile.json`. If it doesn't exist or is missing required keys, run setup first.
2. **Ask the user for the angle/promise.** What hook is this ad selling? (e.g. "Save 20 hours a week", "Make your first $10k month")
3. **Propose 2–3 headline structures** in their voice tone. Format options:
   - One bold promise: "Claude builds my ads."
   - Stat hook: "0 to X in Y days."
   - Authority claim: "The #1 Way To [Outcome]"
   - Confessional: "I haven't [X] in [Y] months."
4. **Confirm or rewrite** the headline with them.
5. **Ask for composition variables:**
   - Which portrait? (List file names from their portrait folder; recommend a pose based on layout — see pose-direction rule)
   - Marc's placement: bottom-left or bottom-right
   - Proof card type: analytics, dashboard, notifications, ads-manager, or none (offer to upload a screenshot they want featured)
   - Palette: A or B from their profile
   - CTA copy (e.g. "Get the system →", "Join now →", "Steal my workflow →")
6. **Upload assets to Higgsfield** via `mcp__d22ec091-cc09-40c1-9c5d-86a96f9441d0__media_upload` + `media_confirm`. Required: portrait + logo (if set). Plus any proof card source images.
7. **Build the prompt** by filling the template (below) with the brand profile values and user choices.
8. **Generate** with `model: "gpt_image_2"`, `count: 4`, `aspect_ratio: "1:1"`, `quality: "high"`, `resolution: "2k"`.
9. **Poll** with `mcp__d22ec091-cc09-40c1-9c5d-86a96f9441d0__job_display` every 90–180 seconds.
10. **Download** results to the output folder with descriptive filenames (`ad-{slug}-{a|b|c|d}.png`).
11. **Read each + open in Preview** (`open -a Preview` on macOS, `xdg-open` on Linux).
12. **On approval**, copy the user's picks to `Keepers/`.

---

## Hard rules (never break)

1. **Output is 1:1 (1080×1080).** Use `aspect_ratio: "1:1"`.
2. **Safe zones: keep top 80px and bottom 80px clear** of text, CTA, logo/mascot, and handle. Allows the user to crop for different placements.
3. **Display font: heavy display serif** (e.g. Fraunces Black). Sharp, high-contrast strokes. **Never default sans-serif** — Inter / Roboto / system sans look generic and AI-generated.
4. **Accent font: monospace bold** for terminal-style accent line. Signals "tech / built / system" instantly.
5. **Pose-direction rule:** the portrait's pointing direction must face inward toward the headline.
   - On right side → don't point right (off-frame)
   - On left side → don't point left
   - Neutral poses (chin, thumbs up, etc.) → either side is fine
6. **No em dashes** in any copy. Use periods, commas, parentheses, or line breaks.
7. **One large focal element per ad.** Usually one accent-colored word or number set 2.5× larger than surrounding type. Eye lands in one place.
8. **Logo/mascot lives top-right corner**, inside safe zone (~100px from top minimum).
9. **CTA pill is filled with the accent color** with type-color text in display serif Bold, ending with `→`. Lives in bottom area inside safe zone.
10. **Handle (`@brandname` or business name) in tiny mono type, bottom-left, inside safe zone.**
11. **Floating proof cards are ALWAYS dark UI** (rounded corners, drop shadow, slight tilt 6–8°, glowing edge halo). Never full-bleed backgrounds.

---

## Prompt template

Fill the slots from the brand profile and user choices. `{{...}}` are variables.

```
Vertical 1:1 Instagram ad designed for 1080x1080 final output. CRITICAL LAYOUT CONSTRAINT: top 80 pixels and bottom 80 pixels of the canvas must be completely empty — no text, no CTA, no logo, no handle, no graphic elements in those zones.

Background: {{PALETTE_DESCRIPTION}}. {{ATMOSPHERE}}. Hex palette: type {{TYPE_COLOR}}, accent {{ACCENT_COLOR}}, background {{BG_COLOR}}.

Use the FIRST reference image ({{PORTRAIT_DESCRIPTION}}) placed {{PLACEMENT}}, large and crisp, with {{LIGHTING_NOTE}}. {{POSE_DIRECTION_NOTE}}

{{LOGO_BLOCK}}

Headline in HEAVY DISPLAY SERIF font ({{DISPLAY_FONT}}, sharp high-contrast strokes, NOT sans-serif), tight leading, with CLEAN BALANCED HIERARCHY. {{HEADLINE_LAYOUT}}

{{ACCENT_LINE_BLOCK}}

{{SUBHEAD_BLOCK}}

{{PROOF_CARD_BLOCK}}

Wide rounded-pill button positioned safely above the bottom 80px margin, filled with {{ACCENT_COLOR}}, {{CTA_TEXT_COLOR}} serif text "{{CTA}} →" in {{DISPLAY_FONT}}, glowing accent shadow underneath.

Tiny {{HANDLE_COLOR}} mono "{{HANDLE}}" in {{ACCENT_FONT}} just above the bottom safe zone.

Premium ad design with elegant balanced typography, ultra crisp text rendering, no artifacts, no extra logos.
```

### Slot fillers

**Dark-dominant palette block** (when palette has dark bg):
```
Background: dark radial gradient using palette colors, rich {{BG_COLOR}} edges blooming to {{ACCENT_COLOR}} highlight at center, warm vignette, cinematic moody lighting
Atmosphere: cinematic, moody, scroll-stopping
Lighting note: a warm rim-light glow separating the subject from the dark background
CTA text color: cream-white version of the type color
Handle color: cream-white
```

**Light-dominant palette block** (when palette has light bg):
```
Background: warm light radial gradient, soft {{BG_COLOR}} outer edges, slightly darker mid-tone, subtle warm highlight at center, airy editorial atmosphere
Atmosphere: airy, premium, magazine-ad feel
Lighting note: a soft grounding shadow under the subject
CTA text color: cream version of the bg
Handle color: type color
Proof card note: dark UI proof cards POP against a light bg, keep them dark themed
```

**Accent line — terminal-style (use when "tech / built / system" signal helps):**
```
Directly below the headline, a single accent line in {{ACCENT_COLOR}} MONOSPACE font ({{ACCENT_FONT}}) styled as a terminal command with leading dollar sign and blinking cursor: "$ {{COMMAND}}█".
```

**Accent line — title-case promise:**
```
Below the headline, an accent subhead in {{ACCENT_COLOR}} MONOSPACE font ({{ACCENT_FONT}}) at modest size, two lines tight stacked, no leading dollar sign, no cursor: "{{LINE_1}}" / "{{LINE_2}}".
```

**Proof card variants:**
- Analytics card: shows title, big number, timeframe, % delta, line chart
- Dashboard card: based on a screenshot reference image
- Notification stack: 3 stacked iOS notifications with brand avatar
- Ads manager: Spend / Reach / CPL / Active Campaign metric layout
- None: omit the block entirely

(Detailed proof card prompt blocks live in `prompt-blocks.md` next to this file — read on demand.)

---

## Generate-image call shape

```
mcp__d22ec091-cc09-40c1-9c5d-86a96f9441d0__generate_image
{
  "params": {
    "model": "gpt_image_2",
    "aspect_ratio": "1:1",
    "quality": "high",
    "resolution": "2k",
    "count": 4,
    "prompt": "<filled template>",
    "medias": [
      { "value": "<portrait_media_id>", "role": "image" },
      { "value": "<logo_media_id>", "role": "image" }
    ]
  }
}
```

---

## Voice + copy guidelines

Pulled from the brand profile's `voice_tone` field. Apply consistently.

- **Headlines = one strong promise.** Specific result or specific use-case. Not abstract.
- **Avoid fear-based "you'll be replaced" framing.** It rarely converts as well as a positive promise of capability.
- **Avoid vague benefit copy** ("transform your business", "unlock your potential"). Be concrete.
- **Title Case** for sub-headlines and CTA copy.
- **No em dashes.** Periods, commas, line breaks instead.
- **CTA verbs that convert:** Get, Steal, Join, Copy, Build, Lock in, Start, Claim.
- **Always end CTA with `→`.**

---

## Filename + output convention

- During generation: `ad-{slug}-{a|b|c|d}.png` in the user's output folder
- On approval: `cp` to `Keepers/` subfolder unchanged
- Example slug: derived from headline — kebab-case, max 5 words

---

## Notes for community members

- **Costs:** Higgsfield gpt_image_2 high/2k runs ~$0.10 per generation, $0.40 per batch of 4. OpenAI direct is similar.
- **Iteration is cheap.** Generate 4 variations, pick one, regenerate the rest with adjustments. Don't try to nail it in one prompt.
- **Save your wins.** The Keepers folder doubles as a future reference library — the model gets better at matching your style each time you give it your own approved ads as references.
- **Update your brand profile any time** by editing `~/.config/ad-image/brand-profile.json` directly or asking the assistant to "update my ad-image brand profile".
