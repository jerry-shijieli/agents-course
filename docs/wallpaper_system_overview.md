# Wallpaper Content System — Engineering Overview

This document condenses the provided production plan into a single, at-a-glance engineering map.

## System Flow (Text Diagram)
```
Calendar/Festivals/Weather/Bible
              ↓
        Topic Generator
              ↓
        Prompt Factory
              ↓
      Approval Queue (email)
              ↓
   Model Runners (MJ / Gemini / LLM)
              ↓
   Evaluator (scoring + selection)
              ↓
    Assembler (5-image wallpaper set)
              ↓
        Publish Pack Export
              ↓
        Manual Xiaohongshu post
```

## Modules and Responsibilities
| Layer | Components | Purpose |
| --- | --- | --- |
| Scheduling | `scheduler.py` (07:30–07:35 jobs) | Triggers topic generation, prompt build, email notification, and downstream generation. |
| Data | DB tables: `topics`, `prompts`, `assets`, `wallpaper_sets` | Persist workflow state, approvals, assets, and publish-ready bundles. |
| Services | Theme generator, prompt builder, AI adapters, evaluator, assembler | Implement the core logic for creating, filtering, and assembling content. |
| Adapters | `adapters/gemini_image.py`, `adapters/midjourney_export.py`, `adapters/llm_copy.py` | Unified `generate(prompt, config) → asset` interface for image and copy generation. |
| API | FastAPI routes (`/topics`, `/prompts/{topic_id}`, `/generate`, `/wallpaper/{id}/export`, approvals) | Human-in-the-loop controls and export endpoints. |
| Storage | `storage/images`, `storage/publish_packs` | Retain raw outputs and final deliverables. |

## Wallpaper Set Rule (5 Images)
1. iPhone wallpaper — AI generated (1290×2796, 9:19.5).
2. iPad wallpaper — AI generated (2048×2732, 3:4).
3. iMac wallpaper — AI generated (5120×2880, 16:9).
4. Device showcase — template composite of the three devices.
5. Space demo — secondary render/post-process for depth effect.

## Approval Checkpoints
| Stage | Action | Effort |
| --- | --- | --- |
| Topic review | Pick 1–2 topics for the day | 2–3 minutes |
| Prompt review | Sanity check templates | ~5 minutes |
| Final selection | Choose winning images + copy | ~10 minutes |

## Publish Pack Layout
```
/publish_packs/YYYY-MM-DD_<topic>/
├── 01_iphone.png
├── 02_ipad.png
├── 03_imac.png
├── 04_devices.png
├── 05_space.png
├── copy.md
└── prompts.md
```

## Weekly vs Daily Ops
- **Weekly (60–90 minutes):** generate topic pool, batch prompts, batch images + copy, stash into asset pool.
- **Daily (≤30 minutes):** pick one set, tweak a line if needed, publish to Xiaohongshu.

## Non-Negotiable Principles
1. One topic → generate all three content tracks once.
2. Prompt templating → avoid daily prompt crafting.
3. Automate prep → humans only decide and approve.
