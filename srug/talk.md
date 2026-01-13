---
title: AI should write Rust and only Rust ;)
sub_title: (ok maybe typescript too)
author: Patrick Gray - pgray
---

# talk overview

1. whoami
2. fp: first large/serious project in rust
3. aiaw: AI aided work
4. conclusion: "what code humans vs AIs should write?"

<!-- end_slide -->

# whoami: pgray

- Patrick - `pgray` on github, sr.ht, codeberg
  - i love linux
- staff engineer at baselayer.com (de-risk loans/signups/etc.)
  - (we're hiring) (SF, NYC, Remote)
  - python on GCP Cloud Run
  - open to rust when the time/problem is right
    - we do use pydantic and astral/ty
- been a member of SRUG since 2018
  - love the discord and videos
  - thank you to Rory and Brad

![image:width:50%](pgray.png)

<!-- end_slide -->

# fp: file.photo - late 2023

- google/apple photos for RAW files (CR2, RAF, NEF, etc.)
  - "build your photo archive"
- almost at-cost s3/b2/gcs/r2/etc.

<!-- end_slide -->

# fp: goals

- cheap: pay as you go
- fast: rust, tons of caching, local-capable
  - axum, rusqlite, tokio, serde, aws (for s3 api client), async-nats
- good: frontend was lacking... but caching was easy

<!-- end_slide -->

# fp: limitations

- micro-SaaS/indiehacker project
  - just me
  - some friends helped out
    - shout out to:
    - Joe W. (api/tooling)
    - Peter E. (android)
  - but it got lonely quick
- learning a lot about rust on the fly
  - async lifetimes
  - tokio streaming (and `futures::stream::StreamExt::inspect()`)
  - api/backend DB dev (i mostly have experience with kv stores)
- started free claude.ai usage
  - no claude code
  - copy/paste compiler errors into chat

<!-- end_slide -->

# fp: hacks (to "Go" faster but come back and fix later)

- `thumbhack`
  - quickly "hack" thumbnails into place
  - golang process calling exec on a CLI tool
  - generated a jpg file for each raw
  - generate "raw" json blob and parse parts for display
  - self-contained running over `NATS` (queue/kv/object store)
    - later needs isolation

<!-- end_slide -->

# fp: exiv2, exiftool, and dcraw

- exiftool -- `perl`
  - author: Phil Harvey (SWE in Particle Astrophysics) (retired)
  - `exiftool -j $raw_file > raw_file_exif.json`
- exiv2 -- `c++`
  - author: Robin Mills (SWE at Adobe Systems) (retired)
  - `exiv2 $file > raw_tags_values.txt`
- dcraw -- `c`
  - author: Dave Coffin
  - `$stream | dcraw -e -c $img.jpg`
  - libraw
    - derived from dcraw

<!-- end_slide -->

# fp: to backburner

- burnt out
- built most of billing (storage tracking)
- got some contract work doing devops/sre/etc
- later... joined baselayer

<!-- end_slide -->

# aiaw: AI aided work

- tooling: IDEs, bots, cc
- idea: fpexif
- side-side projects: vibes.bingo and MADstack
-

<!-- end_slide -->

# aiaw: tooling

- used:
  - cursor (with composer-1)
  - zed.dev (with anthropic opus 4.5)
    - completions
    - prompts
  - github PR review bots
    - cursorbot (great ootb config)
    - claude (verbose IME)
  - claude code
    - with ralph plugin
- soon maybe:
  - opencode+gpt
  - antigravity

<!-- end_slide -->

# aiaw: AI aided work

```
The toolset used in Software Engineering evolves with time.
C++ has been around for about 35 years and, while many complain about it,
I expect it will out-live most of us. None-the-less, languages which are
less vulnerable to security issues may lead the project to a re-write in
a new language such as Rust. I hope this book provides the necessary
understanding of metadata to support such an undertaking.
```

<!-- end_slide -->

# aiaw: fpexif

- tmpdns
- vibes.bingo
- `fpexif`
  - exiv2 git submodule
  - exiftool git submodule
  - [rawsamples](rawsamples.ch) est. 2007
    - Jakob Rohrbach
    - goal to provide raw samples of every available format
    - ~7G of raw photos
  - [raw.pixls.us](raw.pixls.us) successor to rawsamples
    - ~56G of raw photos
    - used by darktable, rawspeed, and rawtherapee

<!-- end_slide -->

# aiaw:

- MADstack
  - claude-friendly maud+axum+diesel framework
  - if AI is going to write most of the code, no need for a framework other than good libraries
  - "if you're going to make things easy with AI, do it the hard way with rust"

<!-- end_slide -->

# conclusion:

- localdev or hosted dev is key
  - claude-code/opencode need a full test cycle to reliably produce
- trading speed for sureness...
  - you're still on the hook for changes and output
- token budget is finite so:
  - custom tests with smaller output is helpful
  - different output modes for tests `CI=human` vs `CI=true`

---
