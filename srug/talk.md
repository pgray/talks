---
title: AI should write Rust and only Rust ;)
author: Patrick Gray - pgray
---

# talk overview

1. whoami
2. fp: first large/serious project in rust
3. aiaw: AI aided work
4. conclusion: "what code humans vs AIs should write?"

<!-- end_slide -->

# whoami

- Patrick - `pgray` on github, sr.ht, codeberg
  - i love linux
- staff engineer at baselayer.com (we're hiring) (SFO, NYC, Remote)
  - python on GCP but open to rust when the time/problem is right
- been a member of SRUG since 2018
  - love the discord and videos

![image:width:50%](pgray.png)

<!-- end_slide -->

# fp: file.photo - late 2023

- google photos for RAW files (CR2, RAF, NEF, etc.)
  - "build your photo archive"
- what if you, as a photographer of any level, could keep a RAW of every photo you've ever taken?

<!-- end_slide -->

# fp: goals

- cheap: pay as you go
- fast: rust, tons of caching, local-capable
- good: frontend was lacking... but caching was easy

<!-- end_slide -->

# fp: limits

- limitations
  - micro-SaaS/indiehacker project (solo work)
  - learning a lot of stuff about rust on the fly
- started free claude.ai usage (chat)
  - no claude code

<!-- end_slide -->

# fp: hacks (to go faster but come back and fix later)

- `thumbhack`
  - quickly "hack" thumbnails into place
  - golang process calling exec on a CLI tool
  - generated a jpg file for each raw
  - generate "raw" json blob and parse parts for display
  - self-contained running over NATS (queue/kv/object store)

<!-- end_slide -->

# fp: exiftool, exiv2, and dcraw

- exiftool -- `perl`
  - `exiftool -j $raw_file > raw_file_exif.json`
- dcraw -- `c`
  - libraw
- exiv2 -- `c++`
  - `exiv2`

<!-- end_slide -->

# fp: to backburner

- burnt out
- built most of billing (storage tracking)
- got some contract work doing devops/sre/etc

<!-- end_slide -->

# aiaw: AI aided work

- zed.dev
  - completions
  - prompts
- github PR review bots
  - cursor
- claude code

<!-- end_slide -->

# aiaw: fpexif

- tmpdns
- vibes.bingo
- `fpexif`
  - exiv2
  - exiftool
  - [raw samples](rawsamples.ch)

<!-- end_slide -->

# aiaw:

- MADstack
  - claude-friendly maud+axum+diesel framework
  - if AI is going to write most of the code, no need for a framework other than good libraries
  - "if you're going to make things easy with AI, do it the hard way with rust"

<!-- end_slide -->

# conclusion:

- trading speed for sureness...
  - you're still on the hook

---
