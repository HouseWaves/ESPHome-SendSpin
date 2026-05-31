# housewaves-firmware

Public firmware release repo for HouseWaves smart speakers.

This repo holds compiled firmware binaries (attached to GitHub Releases) and
the two manifest files each model needs. Source YAML and build scripts live in
the private build repo.

The **web installer page** lives at `getHouseWaves.com/install` (private site repo).

---

## Two manifests per model — different consumers, same binary

```
manifests/
  hw1/
    installer-manifest.json   ← used ONCE by the browser (ESP Web Tools, USB first flash)
    ota-manifest.json         ← baked into firmware; device polls this URL over Wi-Fi forever
  hw2/                        ← placeholder, populate when firmware is ready
    installer-manifest.json
    ota-manifest.json
  hw3/
    installer-manifest.json
    ota-manifest.json
```

Both manifests reference the **same firmware binary**. The distinction is who reads them:

| Manifest | Who reads it | When |
|---|---|---|
| `installer-manifest.json` | Browser (ESP Web Tools) | First-time USB flash only |
| `ota-manifest.json` | Device firmware | Every Wi-Fi update check, forever |

The `ota-manifest.json` URL is baked into the firmware at compile time via the
ESPHome `update:` component's `source:` field. Devices that are adopted into ESPHome
will also surface this as a Home Assistant update entity — the user sees "Update available"
in HA and approves it there. Devices not adopted in ESPHome update silently.
Either way, the same OTA manifest URL drives it.

---

## ESPHome YAML snippet (baked into firmware at build time)

```yaml
update:
  - platform: http_request
    name: "Firmware Update"
    source: "https://raw.githubusercontent.com/undotcom/housewaves-firmware/main/manifests/hw1/ota-manifest.json"
    update_interval: 6h
```

---

## Release workflow (manual)

1. Build Stage 2 clean firmware → `hw1-firmware.bin`
2. Create GitHub Release tagged `v1.XX.XX`, attach `hw1-firmware.bin`
3. Update `manifests/hw1/ota-manifest.json` — bump `version`, update `release_url` and binary `path`
4. Update `manifests/hw1/installer-manifest.json` — bump `version` and binary `path`
5. Commit and push manifests — devices detect the update on next poll; installer page gets the new version automatically

---

## Activating HW-Two or HW-Three

In `index.html` (private site repo), find the `MODELS` object and:
1. Set `available: true`
2. Add `installer_manifest` and `ota_manifest` URLs
3. Change the model card `badge-soon` → `badge-available` and remove `coming-soon` class

---

## Models

| Model | Status | Board | Audio |
|---|---|---|---|
| HW-One | ✅ Available | Sonocotta LOUDER ESP32-S3 Plus | SendSpin (IDF) |
| HW-Two | 🔄 In progress | Sonocotta LOUDER S3 Plus | SendSpin (IDF) |
| HW-Three | ⏳ Planned | TBD | TBD |

---

HouseWaves · getHouseWaves.com · tim@getHouseWaves.com
