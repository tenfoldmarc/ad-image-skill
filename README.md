# ad-image

A Claude Code skill for generating scroll-stopping Instagram ads (1080×1080) for any business. Built on top of Higgsfield's gpt_image_2 (or your own image generator). Bakes in a battle-tested conversion-focused template — heavy display serif headlines, monospace terminal-style accents, dual color palette, floating dark proof cards, pose-aware portrait placement, safe zones for cropping, branded CTA pill.

## What it does

Type `/ad-image` in Claude Code and it walks you through generating a batch of 4 ad variations in your own brand style:

1. Asks for the angle/hook of the ad
2. Proposes 2–3 headline directions in your voice
3. Confirms composition (portrait pose, palette, proof card type, CTA)
4. Generates 4 layout variations in parallel
5. Saves to your output folder, opens them in Preview
6. Copies your picks to a `Keepers/` folder

The first time you run it, a one-time setup wizard captures your brand colors, fonts, portraits, logo, and voice, and saves them to `~/.config/ad-image/brand-profile.json`. After that, every invocation reads that profile.

## Install (beginner-friendly)

Open [Claude Code](https://claude.com/claude-code) and paste this in:

> Install https://github.com/tenfoldmarc/ad-image-skill for me so I can create Instagram ads using OpenAI's image model. Connect Higgsfield MCP if I have an account, or set up direct OpenAI API access if I have an API key. Walk me through whichever I need.

Claude will:
1. Clone the skill into `~/.claude/skills/ad-image`
2. Ask whether you want to use **Higgsfield** (browser sign-in, pay-as-you-go, best quality) or **OpenAI direct** (paste your API key, ~$0.10 per ad)
3. Configure that for you
4. Run the brand setup wizard so the skill knows your colors, fonts, portraits, and voice

After that, type `/ad-image` any time you want a new batch of 4 ad variations.

### Manual install (if you prefer terminal)

```bash
git clone https://github.com/tenfoldmarc/ad-image-skill.git ~/.claude/skills/ad-image
```

Then type `/ad-image` in Claude Code.

## Requirements

- [Claude Code](https://claude.com/claude-code) installed
- An image generator (one of):
  - **Higgsfield** account ([higgsfield.ai](https://higgsfield.ai/)) — recommended, best quality
  - **OpenAI API key** — direct access to `gpt-image-1`

## Brand profile

Your settings live in `~/.config/ad-image/brand-profile.json`. You can edit it any time by hand or by asking Claude to "update my ad-image brand profile". See `brand-profile.example.json` for the schema.

Key fields:

- **`brand`** — name, offer, audience, voice tone
- **`design`** — two palettes (A and B) with hex codes, plus display + accent font choices
- **`assets`** — paths to your portrait folder and logo file
- **`output`** — where generated ads are saved
- **`generator_path`** — `higgsfield` (uses `gpt_image_2`) or `openai` (uses `gpt-image-1` via your API key)

## Why this template works

The visual rules in this skill come from many iterations of testing what converts on Instagram for personal-brand and creator-led businesses:

- **Heavy display serif** (not sans-serif) — looks designed, not AI-generated
- **One focal element** sized 2.5× the surrounding type — the eye lands once
- **Monospace terminal accent** — signals "tech / built / real product"
- **Floating dark proof card** — provides specificity instead of just claims
- **Pose-direction rule** — pointing finger leads the eye to the headline, never off-frame
- **Safe zones** — every ad survives cropping to other placements (Reels, Stories, etc.)

## Customization

Everything is configurable through your brand profile. The skill itself ships with sensible defaults (Fraunces Black + JetBrains Mono Bold) but you can swap in any display serif and any monospace.

If you want to extend the skill, the prompt template lives in `SKILL.md` and the proof card variants in `prompt-blocks.md`. Both are plain Markdown — fork the repo, tweak the prompts, point your install at your fork.

## License

MIT — use it, fork it, share it. If you build something cool with it, tag me.
