# SESSION 6c.2 — Capable Sandbox Image — CLOSURE
_2026-06-11 (GUI/Screen-Sharing session, D20 venue confirmed). Replaces the stdlib-only slim image with a capable image shared by every agent; also applied the P6-28 model rider. **Records: P6-28 (Opus) + P6-31 (capable image).**_

## Outcome
Both live agents (Marie/`pa`, Charlotte/`pro`) now run on **`openclaw-sandbox:capable-2026-06`** — LibreOffice headless, Playwright+Chromium, Google API client, python-docx/openpyxl — with the Strategy-2 isolation unchanged (`mode:all`, `network:bridge`, `readOnlyRoot:true`, secrets outside the container). The readOnlyRoot-vs-writes watch-point is resolved with **zero extra config** (image-baked ENV + OpenClaw's auto-tmpfs `/tmp`). All four toolchains smoke-pass **inside real agent containers**, including docx→PDF and a Chromium launch; the Gmail helper still works for both mailboxes (regression clean). Separately, **P6-28** put both agents on **Opus 4.7** at **`thinkingDefault:"high"`** ("Extra" effort). **6c.2 CLOSED.**

## What was done (arc)
- **P6-28 (rider, applied first — SSH-safe).** A-12: `claude-opus-4-8` is **absent from the pinned 2026.4.22 catalog** and hard-rejected at dispatch; newest accepted Opus = **`claude-opus-4-7`**. Effort knob = per-agent **`thinkingDefault`** (enum `off|minimal|low|medium|high|max`; no "extra") → **`high`**. F17-staged (jq→`.new`→atomic mv→chmod600/chgrp staff), confined-diff proof, hot-reload (no restart), F26 real Marie turn. Peter's call: **keep 4.7, do not chase 4.8** (overlay = unsupported ADR-§2 scaffolding; clean route is bumping the OpenClaw build). 4.8 overlay was staged then **discarded — never applied**.
- **Capable image (main task — GUI venue).** Recon resolved the writable-scratch watch-point empirically: container runs as **uid 502:20**, WorkingDir `/workspace`, OpenClaw auto-provides tmpfs `/tmp,/var/tmp,/run`, and **image ENV passes through** (USER/WORKDIR overridden) → config change collapses to the **image string alone**. Dockerfile (lean tier, Peter's choice — no JRE; Liberation/DejaVu/Noto-core fonts cover Latin+Cyrillic) authored + built (`c209d31c7904`, 3.04 GB). Image-level + **constrained smoke** (`--user 502:20 --read-only --tmpfs …`) both green. Config swapped in defaults+pa+pro (P6-24), recreated pa+pro **after** build+commit (P6-23), proven through **real agent turns** (docx→PDF, xlsx, Chromium, py-libs) + Gmail regression on both mailboxes.

## End-state (anchors)
- **openclaw.json:** **`9477ab40a92a`** (8732B) — `== .last-good`, **A-7 CLEAN**. Gateway pid **56991**, healthz 200 (:18789).
- **Image:** `openclaw-sandbox:capable-2026-06` (id `c209d31c7904`, 3.04 GB) in `agents.defaults.sandbox.docker.image` + per-agent pa + pro.
- **Models:** pa & pro = `anthropic/claude-opus-4-7`, `thinkingDefault:"high"`.
- **Containers:** both `scope:agent`, `net=bridge`, `readOnlyRoot:true`, on the capable image; binds = `/workspace` (rw) + `/opt/gmail_helper.py` (ro) + `/creds/google.json` (ro). Lazy respawn.
- **Dockerfile:** `~/.openclaw/sandbox-images/capable-2026-06/Dockerfile` — `51ba6d5c7213`, 2960B.
- **Agent files (unchanged this session):** Marie AGENTS `2966a8599e13` / TOOLS `d4001c96749d`; Charlotte AGENTS `bd0fa295eee0` / TOOLS `69876f4d110d`.

## Carried / new findings (see P6-31 for detail)
- **Chromium in-container flags:** `--no-sandbox --disable-dev-shm-usage` required (no userns; small /dev/shm). Document in TOOLS.md when browser capability is wired.
- **Agent-exec `HOME=/workspace`** overrides the baked `HOME=/tmp` → office/browser scratch accumulates in the host-mounted workspace (functional, not clean). Optional fix `sandbox.docker.env.HOME=/tmp` deferred to 6c.3.
- **Google library + creds integration** (google-api-python-client ⇄ `/creds/google.json`) and **TOOLS.md capability documentation** for the new toolchains ride with **6c.3**. 6c.2 proved the libraries present + egress/creds healthy; it did not wire them.
- Image size 3.04 GB (heavier than estimate; acceptable). LibreOffice docx→PDF works Java-less (lean tier validated).

## Outstanding (carried, unchanged)
- **P6-1 calendar / Google APIs → 6c.3** (next session; durable OAuth + Calendar/Drive/Docs).
- **F28 cold-reboot proof** — still pending.
- **P6-27 / P6-30** chat-surface hygiene → 6c.6. **P6-29** scheduling reliability → 6c.7. **SR-1** docs-autopilot → 6d.
- **PENDING-BATCH-APPLY** (P6-9/11/12/14), **P6-20 avatar** — post-roster / docs-locked.
- **P6-24 sandbox-block drift** — pa+pro carry full sandbox blocks; re-sync on any `agents.defaults.sandbox` change (held this session).

---
_Attachments for 6c.3: this closure + PHASE-6-DEVIATIONS-LOG.md (P6-28, P6-31) + MASTER-ARCHITECTURE-v6 + the 6c.3 opener. Older closures on demand._
