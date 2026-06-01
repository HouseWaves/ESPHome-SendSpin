# housewaves-firmware

ESPHome with SendSpin firmware repo for HouseWaves Home Assistant speakers.

This repo holds compiled firmware binaries (attached to GitHub Releases) and
the two manifest files each model needs. Source YAML and build scripts live in
the private build repo.

```
manifests/
  hw1/
    ota-manifest.json         ← baked into firmware; device polls this URL over Wi-Fi forever
  hw2/                        ← placeholder, populate when firmware is ready
    ota-manifest.json
  hw3/
    ota-manifest.json
```

Both manifests reference the **same firmware binary**. The distinction is who reads them:

| Manifest | Who reads it | When |
|---|---|---|
| `ota-manifest.json` | Device firmware | Every Wi-Fi update check, forever |

The `ota-manifest.json` URL is baked into the firmware at compile time via the
ESPHome `update:` component's `source:` field. Devices that are adopted into ESPHome
will also surface this as a Home Assistant update entity — the user sees "Update available"
in HA and approves it there. Devices not adopted in ESPHome update silently.
Either way, the same OTA manifest URL drives it.


