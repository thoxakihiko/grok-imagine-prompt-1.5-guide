# Example — Cinematic Romance Drama (Text-to-Video, single generation)

A 15-second emotional drama with **five distinct camera angles, hard cuts, and spoken English dialogue — produced in one Grok Imagine 1.5 text-to-video generation** (no editing, no extension chaining).

This is a worked example of three techniques from the main [guide](../README.md): **timeline segmentation**, **explicit `Sound:` direction**, and **constraint-locking** for cross-cut consistency.

---

## The prompt

```
[VIDEO-T2V — Grok Imagine 1.5]

A cinematic romance drama, 15 seconds, with spoken English dialogue, lip-synced. Multiple camera angles with hard cuts between shots.

Subject: A 22-year-old American college man, casual sweater and jeans, behind a brick university building in soft late-afternoon light. He hides a small bouquet of flowers behind his back. A 21-year-old American woman, light jacket, backpack on one shoulder, faces him.

Shots and dialogue in timeline order:
0-3s: WIDE SHOT, both visible. The man shifts his weight, swallows hard, pulls the bouquet from behind his back and holds it out. He speaks, voice shaky: "I... I've wanted to tell you this for so long. I like you."
3-6s: HARD CUT to a TIGHT CLOSE-UP on the woman's face. Her expression softens with regret, eyes lowering. She replies softly: "I'm sorry. I can't. I'm leaving the country."
6-9s: HARD CUT to a TIGHT CLOSE-UP on the man's face, locked and static. His hopeful look freezes, his smile fades, his eyes glisten. He stays silent.
9-12s: HARD CUT to a WIDE SHOT from behind the man. The woman turns and walks away into the background, growing smaller. He stands still, the bouquet lowering to his side.
12-15s: HARD CUT to an EXTREME CLOSE-UP on the man's face, static framing. A single tear brims, his jaw trembles, he blinks hard and forces a shaky half-smile. He whispers, voice breaking: "I'm okay."

Camera: Each shot is a locked, static frame — no zooming or drifting. Transitions are clean hard cuts only. Shallow depth of field, warm golden backlight, soft campus bokeh.

Sound: Soft ambient campus afternoon, faint birds, gentle wind. A low melancholic piano enters at 6s and swells, peaking in the final seconds. Clear spoken English dialogue — the man's voice nervous then breaking, the woman's soft and apologetic.

Style: Cinematic, film grain, warm desaturated color grade, emotional indie-drama tone, lingering final frame.
```

### Negative constraints

- No costume or appearance changes.
- No shifts in lighting color temperature or intensity.
- No visible lighting equipment in frame.
- No additional characters entering frame.
- No text overlays or subtitles.
- No jarring camera movements.
- No appearance or disappearance of objects.

---

## Why it works

1. **Timeline segmentation beats the "one action per clip" default.** The guide's baseline is one beat per clip — but because Aurora renders sequentially from the first frame forward, you can pack a *shot list* into a single generation by labelling each beat with an explicit time range (`0-3s`, `3-6s`, …) and an explicit `HARD CUT to [shot type]`. Prompt order becomes screen order.
2. **Shot type + cut are named, never implied.** `WIDE SHOT`, `TIGHT CLOSE-UP`, `EXTREME CLOSE-UP`, and `HARD CUT to …` are concrete instructions. "Cinematic, multiple angles" alone would tell the model nothing.
3. **Audio is directed, not left to chance.** The `Sound:` block places the piano on the heartbreak beat (`enters at 6s and swells`) and assigns per-shot vocal emotion. Native audio is generated in the same pass as the motion.
4. **Constraints lock consistency across cuts.** Five shots of the same two people is where most models drift — different faces, changing clothes, shifting light. The negative constraints forbid exactly those failure modes, which is why wardrobe and identity hold across every cut.

## Observed output

Rendered at 15.0s / 24fps with native stereo audio. Five distinct shots with **consistent characters and wardrobe across every cut** — the flowers, the walk-away, and the final single tear all land as written.

Caveats (Preview behaviour): the mid-clip cut to the man's close-up drifted slightly later than the written `6s` mark, and lip-sync fidelity varies shot to shot. Treat the timeline as a strong steer, not a frame-accurate guarantee — see **Known instability** in the [main guide](../README.md#compliance--moderation-note).

---

> Part of the [Grok Imagine 1.5 — Complete Prompt Reference Guide](../README.md). Licensed under [CC BY 4.0](../LICENSE).
