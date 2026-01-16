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

# fp:

- cloudflare worker wasm binary?
  - was too hard... too much googling
  - even ai help wasn't amazing (pre claude code)
- take advantage of hetzner traffic quotas
  - tiny CPU but tons of network throughput

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
