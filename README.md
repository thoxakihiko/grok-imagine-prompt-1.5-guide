# Grok Imagine 1.5 — Complete Prompt Reference Guide (Image + Video)

![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-black.svg)
![Scope: Grok Imagine 1.5](https://img.shields.io/badge/Grok%20Imagine-1.5-111.svg)
![Status: living document](https://img.shields.io/badge/status-living%20document-2ea44f.svg)
![Last verified: June 2026](https://img.shields.io/badge/last%20verified-June%202026-blue.svg)

A practical, field-tested prompting reference for **xAI Grok Imagine 1.5** — covering the full surface: text-to-image, image editing, multi-image editing, text-to-video, image-to-video, video editing, reference-to-video, and video extension. Built as a working playbook, not marketing copy: hard specs you can rely on, validated patterns you can copy-paste, and the anti-patterns that quietly waste your generations.

> **Model**: `grok-imagine-image-quality` (image) + `grok-imagine-video` / Video 1.5 (video) — xAI Aurora engine
> **Scope**: Text-to-Image, Image Editing, Multi-Image Editing, Text-to-Video, Image-to-Video, Video Editing, Reference-to-Video, Video Extension
> **Layer model**: THE GRAMMAR (hard specs & syntax) + THE PHRASEBOOK (validated patterns). The Grammar wins on every technical-constraint conflict.
> **Last verified**: June 2026 — Video 1.5 is a Preview; xAI can change behavior and limits without notice.

### Who this is for

Creators, prompt engineers, and developers shipping real work on Grok Imagine who are tired of guessing. If you've ever had a clip come out silent, an action arrive late, or a "cinematic" prompt do nothing — this guide is the fix.

### ⚠️ Disclaimer

Unofficial and not affiliated with xAI. Core hard limits in this guide were cross-checked against the official xAI documentation (`docs.x.ai`) in June 2026; figures tagged **_reported_** come from secondary sources and should be verified before you hard-commit. Video 1.5 is a **Preview** — treat every limit as a strong default and trust your own test results when they diverge. Always review generated output before using it in client deliverables.

---

## Table of Contents

1. [Model Overview — The Mental Model](#1-model-overview--the-mental-model-for-grok-imagine-15)
2. [Parameters & Specifications (Image + Video)](#2-parameters--specifications-image--video)
3. [PART A — IMAGE: `grok-imagine-image-quality`](#3-part-a--image-grok-imagine-image-quality)
4. [PART B — VIDEO: THE GRAMMAR](#4-part-b--video-the-grammar)
5. [PART C — VIDEO: THE PHRASEBOOK (validated patterns per mode)](#5-part-c--video-the-phrasebook-validated-patterns-per-mode)
6. [Anti-Patterns](#6-anti-patterns)
7. [Ready-to-Use Templates](#7-ready-to-use-templates)
8. [Routing Note — Grok Imagine 1.5 vs Seedance 2.0](#8-routing-note--grok-imagine-15-vs-seedance-20)
9. [Sources & Provenance](#9-sources--provenance)
10. [Contributing & Accuracy](#10-contributing--accuracy)

---

## 1. Model Overview — The Mental Model for Grok Imagine 1.5

Grok Imagine is xAI's image+video model running on the **Aurora engine** (autoregressive). For video, this is not a general-purpose, multi-asset model like Seedance — it is an **image-first model that thinks in short, self-contained shots that already include audio**. Native synchronized audio (dialogue + lip-sync, SFX, ambience, music) is generated in the same single pass as the motion.

### The Core Principle (the most important — memorize it)

> **The image carries composition, lighting, and style. The prompt only describes what changes.**

This model's greatest strength is the **starting frame**, not the prompt. The optimal workflow: build a dialed-in still image first (using Grok Imagine Image or your own photo), then animate it. Once the frame is right, the video prompt only needs to describe **motion + sound** — not re-state composition. This is the opposite of Seedance, which can absorb dense, multi-element instructions.

### The Sequencing Rule (the most critical timeline mechanic)

Aurora renders a clip **sequentially, from the first frame forward** — each frame informs the next. Practical consequences:

- **Action written at the START of the prompt appears at the start of the clip.** Information buried at the end of the prompt can arrive late in the generation order and fail to appear clearly.
- **Order the prompt to match the timeline of events.** Write what must happen first, first.
- This is also what makes its motion coherence strong: subject position, light direction, and camera trajectory stay stable across the clip.

> Cross-validation: older Grok Imagine community patterns ("first lines set the tone; the model prioritizes the first 20–30 words") point to the same mechanic. Front-loading is not optional — it's the rule.

### Naming

- **Consumer / app**: "Grok Imagine Video 1.5" (in the Grok app, Morphic, ImagineArt, etc.).
- **API**: model string `grok-imagine-video` (`docs.x.ai`). Some platforms use the alias `grok-imagine-video-1.5` (e.g. Replicate). Some surfaces expose standard vs `1.5-preview` variants.
- **Image**: `grok-imagine-image-quality` (current; aliases `grok-imagine-image-quality-latest`, `grok-imagine-image-quality-20260403`). `grok-imagine-image-pro` is **deprecated as of 15 May 2026** — do not use it for new requests.

---

## 2. Parameters & Specifications (Image + Video)

### Image Specs (`grok-imagine-image-quality`)

| Spec | Value |
|---|---|
| Active model | `grok-imagine-image-quality` (`-pro` deprecated 15 May 2026) |
| Modes | Text-to-image, Image editing (single), Multi-image editing |
| Batch | `n` parameter (multiple images per request; up to 10 — *reported*) |
| Resolution | 1K / 2K (*reported via secondary source*) |
| Aspect ratio | 14 ratios — 13 fixed + `auto` — via `aspect_ratio`; single-image editing **follows the input AR** |
| Multi-image edit | Up to **3 source images** per request (combine subjects / style transfer / compose scene) |
| Output | Temporary URL (default) or base64; download/process promptly |

### Video Specs (`grok-imagine-video` / 1.5)

| Spec | Value |
|---|---|
| Provider / engine | xAI / Aurora (autoregressive) |
| Resolution | **480p** (default) or **720p** |
| Frame rate | 24 fps |
| Aspect ratio | 16:9, 9:16, 4:3, 3:4, 3:2, 2:3, 1:1 (set 16:9 / 9:16 / 1:1, or follow the input image AR) |
| Audio | Native, synchronized (dialogue + lip-sync, SFX, ambience, music) |
| Prompt limit | **4096 characters** (*API-reported*) |
| Pricing | ~$0.050 per output second (*docs.x.ai*) |
| Output | Temporary URL, async (start → poll `request_id` → done) |

### Hard Limits per Mode (THE GRAMMAR — wins on conflict)

| Mode | Input | Duration |
|---|---|---|
| **Text-to-Video** | prompt only | 1–15 s |
| **Image-to-Video** | 1 image = first frame + motion prompt | 1–15 s |
| **Reference-to-Video** | 1–7 reference images (guide style/character) | ≤10 s (*reported*); **cannot be combined with a first-frame image** |
| **Video Editing** | 1 video + instruction | input video **≤8.7 s** |
| **Video Extension** | 1 video (2–15 s) | adds **2–10 NEW seconds** (not the total) |

### Compliance / Moderation Note

A filter is active on **real public figures, celebrity likeness, and trademarked brands/logos**. **False positives** on benign prompts are also frequent (especially after the January 2026 moderation tightening). No documented override exists in the consumer product.

> **Workaround**: rephrase using **fictional descriptors** instead of named entities. Generalize real brands, logos, and text. "A charismatic tech CEO on a rocket factory floor" beats a real person's name.
> **Known instability**: in-frame text, hands/fingers, and identity details can be unstable. Review before publishing — don't ship raw output as a client deliverable.

---

## 3. PART A — IMAGE: `grok-imagine-image-quality`

### 3.1 Image Prompt Anatomy

Grok Imagine Image is strong with **natural language**, not keyword dumps — no keyword stuffing required. The order that works: **Subject → style/medium → environment → lighting → mood → technical**. Front-load the subject (the sequencing rule applies to images too — the first 20–30 words carry the most weight).

```
A collage of London landmarks rendered in a stenciled street-art style, bold spray-paint textures, layered paper edges, high-contrast monochrome with one accent color, gritty urban poster aesthetic
```

### 3.2 Image Generation — Patterns

**Photoreal portrait:**
```
A close-up portrait of a woman in her early thirties wearing a charcoal wool sweater, soft window light from the left, shallow depth of field, natural skin texture, calm confident expression, muted earthy color palette, shot on 85mm
```

**Product shot:**
```
A matte-black ceramic coffee mug centered on a polished concrete surface, single soft key light from upper right, gentle reflection beneath, clean neutral background, premium commercial product photography, sharp focus
```

**Concept / stylized:**
```
A glossy abstract morphic form of transparent glass and liquid chrome, refracting prismatic bands of cyan, magenta, gold and electric blue against a pure black background, hyperreal studio lighting, physically accurate reflections and refractions
```

### 3.3 Image Editing (Single Image)

Use an **imperative/instruction** style — describe the change you want. Output AR follows the input.

> Attach your reference image before sending this prompt.

```
Give the subject a silver necklace and change the background to a softly blurred autumn park.
```

### 3.4 Multi-Image Editing (max 3 sources)

To **combine subjects, transfer style, or compose a scene** from up to 3 images.

> Attach 1–3 source images.

```
Place the product from image 1 onto the surface in image 2, matching the lighting and color grade of image 2. Keep the product label sharp and unchanged.
```

### Image Templates

```
# Generation
[subject, described concretely] in [environment], [medium/style], [lighting], [mood], [color palette], [technical: lens/quality]

# Single-image edit (attach 1 image)
[Imperative change instruction]. Keep [what must stay] unchanged.

# Multi-image edit (attach up to 3)
Combine [element from image 1] with [element from image 2], matching the [lighting/style/grade] of [image N]. Keep [anchor] consistent.
```

---

## 4. PART B — VIDEO: THE GRAMMAR

### 4.1 Prompt Anatomy — 5 Elements

A strong prompt is a **short shot brief, not a caption**. Five elements, each made explicit (don't leave them to the model):

| Element | Content | Example |
|---|---|---|
| **Subject** | who/what, concrete | a presenter in a charcoal sweater |
| **Motion** | what moves + how | she smiles and looks to camera |
| **Camera** | shot type + one move | medium shot, slow push-in |
| **Audio** | dialogue / SFX / ambience / music | she says "Welcome"; soft room tone |
| **Duration** | clip length + aspect ratio | 5 seconds, 16:9 |

### 4.2 The Sequencing Rule (applied)

Write actions in order of appearance. What happens first → write it first. For a multi-beat clip, arrange the sentences to equal the timeline. Never put the key action in the last sentence.

### 4.3 The `Sound:` Section — Write Like a Sound Designer

Always include an explicit **`Sound:`** block. A silent prompt yields a silent clip or random audio. The model can distinguish materials and spaces — use that.

- ❌ Vague: `Sound: city sounds, rain.`
- ✅ Specific: `Sound: heavy rain drumming on corrugated metal awnings, the low buzz of neon sign transformers, a distant scooter fading away, the hiss of tires on a wet road.`

**Spatial & material cues proven strong**: "heard from inside a cabin", "muffled through glass", "sea spray on a microphone", "the headset breathing of a pilot". These cues tell the model which space and materials it needs to design the soundscape.

### 4.4 Camera Movement Dictionary (validated)

**The model defaults to static** if you don't request movement — and that is often the most cinematic choice (locked camera + patient motion). Request movement only when you actually need it, and name it explicitly.

| Keyword | Effect |
|---|---|
| `locked, static` | camera holds still; most cinematic for a calm subject |
| `slow push-in` | moves in slowly; intimate/tense |
| `aerial push-in toward [subject]` | drone approaching from above |
| `camera drifts gently to the left` | subtle drift; alive without being busy |
| `tracking shot alongside [subject]` | follows the subject in parallel |
| `handheld tracking shot following [subject]` | energy, documentary, pursuit |
| `slow pan out` / `pull out` | reveals context |

### 4.5 Intensity Modifiers (scale control)

Without a modifier, the model picks its own interpretation. "The wave crests" is ambiguous. Add scale + style:

> "The wave **crests fully and pitches forward**, crashing down **with tremendous force**, white foam **exploding upward**."

Strong verbs + scalar adverbs = a clip with life. Examples: "screaming high-pitched wail", "long trail of orange sparks", "rockets past the camera".

### 4.6 Style per Mode

| Mode | Prompt style | Official xAI example |
|---|---|---|
| Image-to-Video | **declarative/descriptive** (what moves) | "Make the water crash down and slowly pan out the camera" |
| Text-to-Video | full description (scene + motion + sound) | a short, complete scene |
| Video Editing | **imperative/instruction** (the target change) | "Give the woman a silver necklace" |
| Reference-to-Video | describe the scene; let the reference hold style/character | — |

### 4.7 Lip-Sync Rules (talking head)

- Use a **front-facing portrait, mouth in frame**.
- **Short lines** for clean lip-sync.
- State voice tone/emotion in the Sound block (`her tone is hesitant then determined`).

### 4.8 One Action per Clip → Extension Chaining

**One action beat per clip**, compressed into a few seconds. For long sequences, use **video extension** from the last frame and repeat step by step. Remember the hard rule: extension duration = the number of NEW seconds (2–10 s), not the total.

---

## 5. PART C — VIDEO: THE PHRASEBOOK (validated patterns per mode)

The patterns below are derived from public stress-tests of Video 1.5 plus official xAI examples. Every prompt is full English and ready to copy-paste.

### 5.1 Atmospheric Image-to-Video (micro-motion)

For a photo or artwork brought subtly to life. Hone in on a specific object and leave the rest still.

```
A slow warm breeze moves through the frame, lifting a few strands of hair across her cheek, then settling. The firelight on her skin flickers, casting shifting shadows across her brow. Her expression stays completely still. Sound: the soft crackle of burning wood just out of frame, a slow exhale, the distant low moan of wind outside. 6 seconds, 16:9.
```

### 5.2 High-Energy Action Image-to-Video

Intensity modifiers + doppler/material sound give the action weight.

```
The superbike continues leaning hard through the corner at full speed, the knee slider scraping asphalt and throwing a long trail of orange sparks. The stone walls blur past in a torrent of motion. The rider's helmet stays locked on the apex. Sound: the screaming high-pitched wail of a 1000cc engine, the metallic scrape of slider on asphalt, the doppler-shift roar as the bike rockets past the camera. 5 seconds, 16:9.
```

### 5.3 Character Beat Sequence (multi micro-action, one subject)

One sentence per beat, in timeline order.

```
The woman touches her cheek gently, then smiles softly to herself, then turns and smiles wide directly at the camera. Soft natural light, skin texture intact. Sound: quiet room tone, a soft breath, a light fabric rustle. 6 seconds, 9:16.
```

### 5.4 Dialogue / Talking Head (lip-sync)

```
Medium shot, front-facing. The presenter looks to camera and says, "This changes everything." Her tone is calm and confident. She gives a small nod at the end. Camera locked, static. Sound: clear close dialogue, soft room tone, no background music. 5 seconds, 16:9.
```

### 5.5 Text-to-Video Scene Build

```
Handheld tracking shot following a woman through rain-slicked streets at night, neon reflections rippling on wet pavement, shallow depth of field. She pulls her coat tighter and glances back once. Sound: heavy rain on pavement, the low buzz of neon signs, a distant scooter fading away, the hiss of tires on wet road. 8 seconds, 9:16.
```

### 5.6 Video Editing (imperative, target-specific)

Change a specific element and preserve the rest. Input video ≤8.7 s.

```
Give the woman a deep green scarf and change the season outside the window to autumn. Keep her face, pose, and the room lighting unchanged.
```

### 5.7 Reference-to-Video (lock style / character)

1–7 reference images to keep look/character consistent across clips. Not combined with a first-frame image.

```
A young woman in a dark green hooded cloak walks through a misty medieval market at dawn, traders setting up wooden stalls behind her. She glances at the camera, then ahead. Keep her face, hair, and cloak consistent with the reference. Camera tracks alongside. Sound: low morning market murmur, distant footsteps on stone, a single bird call. 8 seconds, 16:9.
```

### 5.8 Extension Chaining (long sequence)

Clip 1 (image-to-video) → extension from the last frame.

```
# Clip 1 (image-to-video, first frame = your still)
A man stands in a dim stone hall, dust drifting in a shaft of light. He takes one slow step forward. Sound: deep interior quiet, a distant drip, the creak of old wood. 5 seconds, 16:9.

# Extension (continue from last frame, +6s)
He continues walking toward the far door, which slowly swings open into bright golden light. He pauses at the threshold. Sound: footsteps echoing, a low wooden groan of the door, a swell of warm wind. Extend forward 6 seconds.
```

### 5.9 Paired Workflow — Still Prompt + Motion Prompt

Separate the two: the still holds composition & color, the video prompt holds motion. This is far easier to iterate on.

```
# STILL (Grok Imagine Image)
A minimalist Belgian wabi-sabi interior: a long low oatmeal linen sofa against a cream lime-plaster wall, a rough walnut coffee table on polished concrete, a squat ceramic lamp casting a low warm glow, a linen throw draped asymmetrically. No clutter, no pattern.

# MOTION (Video 1.5, first frame = the still above)
The afternoon sunlight through an unseen window slowly shifts and dims, the shaft of warm light moving gradually to the right and narrowing, color shifting from amber to cooler blue. The lamp's glow becomes more pronounced as the room darkens. Shadows deepen in the corners. Sound: deep interior quiet, the faint hum of the city outside, a building settling in the cooling air. 8 seconds, 16:9.
```

---

## 6. Anti-Patterns

| Mistake | Why it fails | Fix |
|---|---|---|
| Prompt with no `Sound:` | silent clip / random audio | always write at least one sound cue |
| "Cinematic" / "epic" camera | tells the model nothing | name a shot type + one concrete move |
| Many actions in one clip | physics glitches, identity breaks | one action per clip, then extend |
| Key action in the last sentence | arrives late (Aurora is sequential) | front-load; order by timeline |
| Ambiguous scale ("the wave crests") | the model picks its own interpretation | add an intensity modifier + strong verb |
| Named celebrity / brand / logo | hits the moderation filter / false positive | fictional descriptors; generalize brands |
| Long, rambling prompt | detail gets diluted | focus on 3 sentences, micro-action per object |
| Negation ("no blur", "don't show X") | often ignored | state it positively ("sharp focus") |
| Expecting precise text/hands/identity | known instability | review before publishing; avoid in-frame text |
| Video prompt re-states composition | wastes tokens — the frame already holds it | i2v: describe only what CHANGES |

---

## 7. Ready-to-Use Templates

```
# TEXT-TO-VIDEO
[Camera: shot type + move]. [Subject doing action, in timeline order].
[Environment + light]. Sound: [material/spatial cues, comma-separated].
[N] seconds, [aspect ratio].

# IMAGE-TO-VIDEO (first frame = your still)
[Only what moves, in timeline order — do NOT re-describe composition].
[Optional camera move, else it stays static].
Sound: [explicit cues]. [N] seconds, [aspect ratio].

# TALKING HEAD (lip-sync)
[Shot type], front-facing. The [subject] looks to camera and says, "[short line]".
Her/his tone is [emotion]. Camera locked, static.
Sound: clear close dialogue, [room tone], [music or none]. [N] seconds, [AR].

# VIDEO EDITING (input ≤8.7s)
[Imperative change instruction]. Keep [face/pose/lighting/etc.] unchanged.

# REFERENCE-TO-VIDEO (1–7 reference images)
[Scene + subject action]. Keep [face/outfit/style] consistent with the reference.
[Camera]. Sound: [cues]. [N≤10] seconds, [AR].

# EXTENSION (continue from last frame)
[New action continuing from the final frame, timeline order].
Sound: [cues]. Extend forward [2–10] seconds.
```

---

## 8. Routing Note — Grok Imagine 1.5 vs Seedance 2.0

A quick decision table for choosing between the two when both are on the table.

| | **Grok Imagine 1.5** | **Seedance 2.0** |
|---|---|---|
| Philosophy | image-first, one complete shot with audio | multi-asset orchestration |
| Input asset | 1 image (first frame) OR 1–7 references | up to 12 mixed assets (`@image/@video/@audio`) |
| Resolution | ≤720p | up to 1080p |
| Duration | 1–15 s | 4–15 s |
| Audio | native; strong **lip-synced dialogue** | native + **music beat-sync** |
| Real human face | supported | **not supported** |
| Syntax | natural language, `Sound:` block | `@material` role-tagging, timeline segmentation |
| Iteration | fast & cheap | heavier, more control |

**Choose Grok Imagine 1.5 when**: animating a finished still, talking-head / lip-sync dialogue, short shot extensions, fast & cheap iteration, or when you need a real human face.

**Choose Seedance 2.0 when**: you need multi-asset composition (combining several images/videos/audio), music beat-sync, camera/action control from a separate reference video, 1080p output, or one continuous shot across spaces.

> **Routing heuristic**: signals like "lip-sync / talking / from this photo / extend the clip" → Grok 1.5. Signals like "combine several references / music beat / `@video` / 1080p / continuous shot" → Seedance.

---

## 9. Sources & Provenance

| Source | Contribution | Layer |
|---|---|---|
| **docs.x.ai** (Video & Image overview, generation, editing, reference-to-video, extension) | 5 official modes, hard limits (15 / 8.7 / 2–10 s), model naming, official imperative prompt style, image model & multi-image edit | GRAMMAR (authority) |
| **Replicate prompting guide** (Video 1.5 stress-test) | `Sound:` syntax, intensity modifiers, camera dictionary, focused-3-sentence rule, paired still+motion workflow | PHRASEBOOK (core) |
| **GitHub `Rion-Wu-tech/grok-video-workflow`** | 4096-char prompt limit, reference-to-video ≤10 s, dual-model API, known instability (text/hands/identity) | GRAMMAR (constraints) |
| **GitHub `that-cod/awesome-grok-imagine-prompts`** (earlier era) | community front-loading principle (cross-validates Aurora), linear actions, avoid negations | GRAMMAR (sequencing, era-flagged) |
| **Morphic guide** | 5-element anatomy, weak/strong principle, one-action-per-clip, lip-sync rule, reference-for-consistency, specs (24 fps, 7 AR) | GRAMMAR (anatomy) |
| **Secondary** (AVB / Runware / PixelDojo / Alici) | image resolution 1K/2K, image aspect ratios, `n`≤10, per-second pricing context, moderation behavior | *reported* — verify before hard-commit |

**Accuracy note**: figures tagged **_reported_** come from secondary/press sources, not an official spec sheet. The core hard limits (durations, resolution, frame rate, model strings, text-to-video support, image-pro deprecation date) were confirmed against `docs.x.ai` in June 2026. Video 1.5 is a Preview; treat limits as strong defaults and trust your own test results when they diverge.

---

## 10. Contributing & Accuracy

This is a **living document**. xAI ships fast, and Preview limits move. If you find a spec that has changed, a pattern that no longer holds, or a number that should lose its **_reported_** tag because you've confirmed it against `docs.x.ai`:

- Open an **issue** with the source link, or
- Send a **pull request** editing the relevant row and citing where the value came from.

Keep the layer model intact: **THE GRAMMAR** holds only hard, verifiable specs; **THE PHRASEBOOK** holds validated patterns. When in doubt, tag a value **_reported_** rather than presenting it as confirmed.

---

## License

Licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE). You are free to share and adapt this material for any purpose, including commercially, as long as you give appropriate credit.

> Maintained by **[@thoxakihiko](https://github.com/thoxakihiko)**. Not affiliated with or endorsed by xAI. "Grok" and "Grok Imagine" are trademarks of their respective owner.
