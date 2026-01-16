hack for file.photo exif data generation

```
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
...
}
```
