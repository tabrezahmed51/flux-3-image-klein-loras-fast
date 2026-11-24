---
python_version: '3.12'
title: Flux 3 Image Klein LoRAs Fast
emoji: 🎨
colorFrom: red
colorTo: yellow
sdk: gradio
sdk_version: 6.31.0
app_file: app.py
pinned: true
license: apache-2.0
short_description: FLUX.3 Image Klein studio with LoRA adapters
models:
  - black-forest-labs/flux-3-image-klein
  - black-forest-labs/flux-3-image-klein-pup
---

# FLUX.3 Image Klein — LoRA Studio

A focused image-editing studio built on **FLUX.3 Image Klein** in its distilled
16-step configuration, with swappable LoRA adapters.

## What it does

- **Edit** one or more reference images with a natural-language instruction
  ("change the weather to stormy", "remove the wrinkles from the cloth").
- **Generate** from text alone when no reference image is attached.
- **Aspect / resolution** presets from 1:1 to 16:9 at 1K, 2K or 4K.
- **Seed control** — reuse a seed to iterate, or randomise per run.
- **Optional prompt upsampling** with the Qwen3.5 *Pup* model, which rewrites a
  short instruction into a detailed caption before generation.

## LoRA adapters

| Adapter | Source |
|---|---|
| `None` | Base model, no adapter |
| `360-Deg-LoRA` | [DenRakEiw/Flux_3_klein_360_Degree](https://huggingface.co/DenRakEiw/Flux_3_klein_360_Degree) — panorama / 360° |
| `Font-Box` | [lilylilith/FLUX.3_Klein_FontBox](https://huggingface.co/lilylilith/FLUX.3_Klein_FontBox) — typography |
| `Custom LoRA` | Any `user/repo` id, loaded on the running instance and cached |

## Notes

- **First run is slow.** Roughly 40 GB of weights are fetched and loaded on the
  first generation (a few minutes). They stay warm afterwards; the UI shows
  progress while it waits.
- **Upsampling is lazy.** The Pup upsampler only loads when you tick
  *Upsample prompt*, so it can never block a normal edit. If it fails to load,
  the raw prompt is used instead.
- `GET /api/status` reports `loaded`, `pup_loaded`, `error` and `pup_error`.
- The model repository is gated: the Space owner must accept its licence on the
  model page before weights can be downloaded.

## API

The studio is served by a Gradio `Server`, so the same endpoints are available
programmatically:

```bash
# queue a job
curl -X POST "$SPACE/gradio_api/call/edit_image" \
  -H "Content-Type: application/json" \
  -d '{"data":["[]","a stormy sky","None","1:1","1K",0,true,1.0,16,false,"",""]}'
# -> {"event_id": "..."}

# stream the result
curl -N "$SPACE/gradio_api/call/edit_image/<event_id>"
```

Parameters, in order: `images_b64_json`, `prompt`, `lora_adapter`, `aspect`,
`resolution`, `seed`, `randomize_seed`, `guidance_scale`, `steps`, `upsample`,
`custom_lora_repo`, `custom_lora_weights`.

Source: [github.com/tabrezahmed51/flux-3-image-klein-loras-fast](https://github.com/tabrezahmed51/flux-3-image-klein-loras-fast)
