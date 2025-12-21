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

# whoami - experience

- computer engineer
  - devops
  - SRE
  - platform
- mainly worked at small companies or startups
  - series A/B, seed, or immediately post-purchase
  - some consult/contract work (AWS/Linux)
- first language: C (but not a ton)
- linux automation: bash, terraform (HCL), yaml, json, etc
- tools: docker, kubernetes, nomad, GCP, AWS, Digital Ocean, etc
- first work language: Go

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
  - learning a lot of stuff about rust as i went
  - no claude code
- started free claude.ai usage

<!-- end_slide -->

# fp:

- cloudflare worker wasm binary?
  - was too hard... too much googling
  - even ai help wasn't amazing (pre claude code)
- take advantage of hetzner traffic quotas
  - tiny CPU but tons of network throughput

<!-- end_slide -->

# fp: hacks (to go faster but come back and fix later)

- `thumbhack`
  - quickly "hack" thumbnails into place
  - golang process calling exec on a CLI tool
  - generated a jpg file for each raw
  - generate "raw" json blob and parse parts for display
  - self-contained running over NATS (queue/kv/object store)

<!-- end_slide -->

# fp: thumbhack example

```go
func (inst *Instance) RunExif(kv nats.KeyValue, img, tmpimg string) error {
    // setup exec...
    cmd := exec.Command("exiftool", tmpimg, "-json")
    // grab pipe for output
    pipe, err := cmd.StdoutPipe()
    if err != nil {
        return err
    }
    go func() {
        err = cmd.Run()
        lol.Sad(err)
    }()
    // read all stdout
    raw, err := io.ReadAll(pipe)
    if err != nil {
        return err
    }
    // save to kv store
    _, err = inst.obs.Put(&nats.ObjectMeta{Name: Fmtexif(img), Description: img}, bytes.NewBuffer(raw))
    if err != nil {
        return err
    }
    // process exif raw to generate kv
    processed := Wrapper{}
    err = json.Unmarshal(raw, &processed)
    if err != nil {
        return err
    }
// ...
}
```

<!-- end_slide -->

# exiftool, exiv2, and dcraw

- exiftool -- `perl`
  - `exiftool -j $raw_file > raw_file_exif.json`
- dcraw -- `c`
- exiv2 -- `c++`
  - `exiv2`

<!-- end_slide -->

# aiaw: AI aided work

- completions
- prompts
- github PR review bots

<!-- end_slide -->

# aiaw: fpexif

- fpexif
  - exiv2
  - exiftool
  - [raw samples](rawsamples.ch)
- tmpdns
- vibes.bingo

<!-- end_slide -->

# exiftool hack

---

# conclusion:

- trading speed for sureness...
  - you're still on the hook

---
