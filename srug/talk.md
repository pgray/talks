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

- Patrick - `pgray` on github, sr.ht, codeberg, pgray.dev
  - i love linux
  - background in: bash, Go, python, systems, SRE/DevOps/etc
- staff engineer at baselayer.com (de-risk loans/signups/etc.)
  - (we're hiring) (SF, NYC, Remote)
  - python on GCP Cloud Run
  - open to rust when the time/problem is right
    - we do use pydantic and astral/ty which rock
- been a member of SRUG since 2018
  - love the discord and videos
  - thank you to Rory and Brad

![image:width:50%](pgray.png)

<!-- end_slide -->

# fp: file.photo - late 2023

- google/apple photos for RAW files (CR2, RAF, NEF, etc.)
- almost at-cost s3/b2/gcs/r2/etc.
- photographers/technologists build custom NAS/backup setups

<!-- end_slide -->

# fp: goals

- cheap: pay as you go
- fast: rust, tons of caching, local-capable features
  - axum, rusqlite, tokio, serde, aws (for s3 api client), async-nats
- good: frontend was lacking... but caching was easy

<!-- end_slide -->

# fp: limitations

- micro-SaaS/indiehacker project
  - just me at first
  - self-teaching svelte
  - some friends helped out
    - shout out to:
    - Joe W. (api/tooling)
    - Peter E. (android)
  - but it got lonely quick
- learning a lot about rust on the fly
  - async lifetimes
  - api/backend DB dev (i mostly have experience with kv stores)
  - async-nats
  - tokio streaming (and `futures::stream::StreamExt::inspect()`)
- started free `claude.ai` usage
  - no claude code
  - copy/paste compiler errors into chat

<!-- end_slide -->

# fp: hacks (to "Go" faster but come back and fix later)

- `thumbhack`
  - quickly "hack" thumbnails into place
  - `Go` process calling exec on a CLI tool
    - generate a jpg file for each raw
    - generate "raw" json blob and parse parts for display
    - rewrite "raw" json to curated set of tags (aperture, ISO, shutter speed)
  - self-contained running over `NATS` (queue/kv/object store)
    - later needs isolation via VM+sockets (no network)

<!-- end_slide -->

# fp: exiv2, exiftool, and dcraw

- exiv2 -- `c++`
  - author: Robin Mills (SWE at Adobe Systems) (retired)
  - `exiv2 $file > raw_tags_values.txt`
- exiftool -- `perl`
  - author: Phil Harvey (SWE in Particle Astrophysics) (retired)
  - `exiftool -j $raw_file > raw_file_exif.json`
- dcraw -- `c`
  - author: Dave Coffin (ended: 2018)
  - `$stream | dcraw -e -c $img.jpg`
  - succeeded by libraw via `dcraw_emu`

<!-- end_slide -->

# fp: to backburner

- burnt out
  - microSaaS+rust+kids/life/etc
- got some contract work doing devops/sre/etc
- built most of billing (storage tracking)
- later... joined baselayer
  - pydantic, uv (ty), pyo3 experiment (~3-5x perf or memory-footprint)

<!-- end_slide -->

# aiaw: AI aided work

- tooling: IDEs, bots, cc
- side-side projects: tmpdns, vibes.bingo and MADstack
- idea: fpexif

<!-- end_slide -->

# aiaw: tooling

- used:
  - cursor (with model:composer-1 mainly)
    - completions
    - prompts
    - plan mode
  - zed.dev (with claude code/anthropic opus 4.5)
    - completions
    - prompts
  - claude code
    - plan mode
    - with ralph plugin
  - opencode
    - default setup
    - TUI is lovely but needs plan mode (coming soon)
  - antigravity
    - google models by default
    - gemini made fun of me
  - github PR review bots
    - cursorbot (great ootb config)
    - claude (verbose IME... maybe bad config)

<!-- end_slide -->

# aiaw:

- tmpdns
  - periodic curl to algorand API
  - look for transactions to given wallet
  - parse transaction note json and create DNS record
- vibes.bingo
  - Carla built a bingo board website
  - I supported it by
    - deploying on digital ocean via app service
    - converted it from tera templates (jinja) to maud (html macro)
    - create idea for MADstack
- MADstack github.com/pgray/madstack
  - --> next slide

<!-- end_slide -->

# aiaw:

- MADstack
  - claude/AI friendly maud+axum+diesel "framework"
  - really a demo app ready to be converted into a template repo
    - `cargo new --madstack-app` ?
  - if AI is going to write most of the code:
    - no need for a framework other than good libraries
    - maybe push folks towards specifications for endpoints/functionality
  - "if you're going to make things easy with AI, do it the hard way with rust"

<!-- end_slide -->

# aiaw: fpexif

```
...
The toolset used in Software Engineering evolves with time.
C++ has been around for about 35 years and, while many complain about it,
I expect it will out-live most of us.

None-the-less, languages which are less vulnerable to security issues
may lead the project to a re-write in a new language such as Rust.

I hope this book provides the necessary
understanding of metadata to support such an undertaking.
...
```

<!-- end_slide -->

# aiaw: fpexif setup

- `fpexif`
  - exiv2 git submodule
  - exiftool git submodule
  - [rawsamples](rawsamples.ch) est. 2007
    - Jakob Rohrbach
    - goal to provide raw samples of every available format
    - ~7G of raw photos

<!-- end_slide -->

# aiaw: start

- build exif image tooling
- add more formats
- add `exiftool` compat.
- add `exiv2` compat.

<!-- end_slide -->

# aiaw: dev loop improvements

### exiftool Comparison

| Format | Files | Match % | ✓ Match | ✗ Mismatch | ⚠ Missing | + Extra |
| ------ | ----- | ------- | ------- | ---------- | --------- | ------- |
| 3FR    | 3     | 95.1%   | 117     | 0          | 6         | 3       |
| ARW    | 31    | 71.1%   | 4653    | 20         | 1870      | 358     |
| CR2    | 54    | 77.2%   | 11385   | 95         | 3272      | 874     |
| CRW    | 18    | 15.6%   | 378     | 9          | 2035      | 85      |
| DCR    | 1     | 8.9%    | 42      | 2          | 427       | 13      |
| DNG    | 13    | 54.0%   | 1026    | 30         | 845       | 156     |
| ERF    | 1     | 80.7%   | 46      | 0          | 11        | 0       |
| KDC    | 5     | 79.9%   | 226     | 4          | 53        | 77      |
| MDC    | 0     | N/A%    | 0       | 0          | 0         | 0       |
| MEF    | 1     | 87.8%   | 43      | 6          | 0         | 0       |
| MOS    | 2     | 10.2%   | 30      | 0          | 263       | 12      |
| MRW    | 8     | 60.4%   | 530     | 13         | 335       | 71      |
| NEF    | 47    | 83.3%   | 7548    | 5          | 1507      | 578     |
| NRW    | 4     | 85.1%   | 496     | 7          | 80        | 31      |
| ORF    | 36    | 82.8%   | 5576    | 3          | 1152      | 128     |
| PEF    | 17    | 37.3%   | 1254    | 20         | 2089      | 199     |
| RAF    | 30    | 90.3%   | 3240    | 0          | 349       | 161     |
| RAW    | 11    | 43.9%   | 415     | 7          | 524       | 255     |
| RW2    | 20    | 59.9%   | 2244    | 4          | 1497      | 110     |
| SR2    | 1     | 75.3%   | 73      | 1          | 23        | 4       |
| SRF    | 1     | 56.8%   | 42      | 1          | 31        | 0       |
| SRW    | 7     | 46.3%   | 333     | 31         | 355       | 85      |
| X3F    | 8     | 46.5%   | 353     | 7          | 399       | 11      |

<!-- end_slide -->

# aiaw: dev loop improvements

### exiv2 Comparison

| Format | Files | Match % | ✓ Match | ✗ Mismatch | ⚠ Missing | + Extra |
| ------ | ----- | ------- | ------- | ---------- | --------- | ------- |
| 3FR    | 3     | 32.0%   | 47      | 52         | 48        | 18      |
| ARW    | 31    | 27.7%   | 1349    | 338        | 3179      | 3268    |
| CR2    | 54    | 22.9%   | 2397    | 3741       | 4308      | 5707    |
| CRW    | 18    | 1.3%    | 18      | 36         | 1346      | 224     |
| DCR    | 1     | 20.2%   | 17      | 15         | 52        | 6       |
| DNG    | 13    | 29.2%   | 432     | 212        | 836       | 3239    |
| ERF    | 1     | 37.3%   | 22      | 18         | 19        | 5       |
| JPG    | 1     | 52.2%   | 12      | 4          | 7         | 2       |
| KDC    | 5     | 46.3%   | 126     | 58         | 88        | 38      |
| MEF    | 1     | 29.5%   | 26      | 11         | 51        | 7       |
| MOS    | 2     | 34.9%   | 22      | 2          | 39        | 2564    |
| MRW    | 8     | 41.6%   | 288     | 85         | 319       | 246     |
| NEF    | 47    | 18.5%   | 1684    | 3389       | 4028      | 2845    |
| NRW    | 4     | 30.1%   | 145     | 123        | 214       | 265     |
| ORF    | 36    | 39.3%   | 2275    | 1980       | 1539      | 1143    |
| PEF    | 17    | 37.1%   | 781     | 431        | 891       | 244     |
| RAF    | 30    | 41.5%   | 1262    | 724        | 1053      | 540     |
| RAW    | 11    | 27.7%   | 134     | 73         | 276       | 305     |
| RW2    | 20    | 20.3%   | 488     | 653        | 1265      | 1081    |
| SR2    | 1     | 36.7%   | 33      | 7          | 50        | 36      |
| SRF    | 1     | 66.7%   | 30      | 5          | 10        | 3       |
| SRW    | 7     | 22.9%   | 151     | 32         | 477       | 232     |
| X3F    | 0     | N/A%    | 0       | 0          | 0         | 0       |

<!-- end_slide -->

# aiaw: dev loop improvements

| Metric          | Count            |
| --------------- | ---------------- |
| Files Tested    | 957 (403 passed) |
| **Match Rate**  | **52.1%**        |
| Matching Tags   | 53738            |
| Mismatched Tags | 12255            |
| Missing Tags    | 37218            |
| Extra Tags      | 25786            |

`Fujifilm - RAF 90.3%`

<!-- end_slide -->

# aiaw: dev loop improvements

- mini server with tmux
- built-in claude code commands
  - `/restart-auto`
  - `/go-on`
  - `/tag-next`
- [raw.pixls.us](raw.pixls.us) successor to rawsamples
  - ~56G of raw photos (vs original 7G)
  - used by darktable, rawspeed, and rawtherapee
- macros - exiftool/exiv2 value mappings
  - the less code ai has to write the less tokens it uses
- mfr-test binary/tooling
- wasm...

<!-- end_slide -->

# aiaw: fpexif wasm demo

- demo?

<!-- end_slide -->

# aiaw: fpexif future

- FOSS
- licensed according to exiftool/exiv2

<!-- end_slide -->

# conclusion:

- AI should write rust and only rust because...
  - 1959: volvo invented the seatbelt
  - rust, to me, is seatbelts for software development
  - AI is self-driving cars (inherently fallible)
  - you don't take your seatbelt off just because your car is self-driving
  - and you wouldn't even if it was perfectly self-driving

<!-- end_slide -->

# conclusion: cont.

- "embarassingly parallel" problems...
  - "embarassingly AI-solveable" problems...
  - high existing input data
  - high existing output date
  - existing impls
- localdev or hosted dev is key
  - claude-code/opencode/etc. need a full test cycle to reliably produce
- token budget is finite so:
  - custom tests with smaller output is helpful
  - different output modes for tests `CI=human` vs `CI=true` ?
- "shift left" is real
  - the more you can do you can shift left the better
  - even go so far left it's into AI's stdin
- trading speed for sureness...
  - you're still on the hook for changes and output

---
