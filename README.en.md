# Apple WLOC Location Spoofer

Modify the coordinates returned by Apple's network-based location service (WiFi/cell) to spoof iOS network location. Open the online picker page, choose a spot, and it takes effect — no need to manually enter longitude/latitude.

> This fork removes hard-coded dependencies on the original author repository. All subscription URLs, icons, and script paths point to this repository (`sodardyjiber/wloc`), so the project remains fully functional even if the original upstream repo is deleted.

---

## Subscription links

**Surge:**
https://raw.githubusercontent.com/sodardyjiber/wloc/refs/heads/main/modules/wloc.sgmodule

**Quantumult X:**
https://raw.githubusercontent.com/sodardyjiber/wloc/refs/heads/main/modules/wloc.conf

**Loon:**
https://raw.githubusercontent.com/sodardyjiber/wloc/refs/heads/main/modules/wloc.lpx

**Stash:**
https://raw.githubusercontent.com/sodardyjiber/wloc/refs/heads/main/modules/wloc.stoverride

**Shadowrocket:**
https://raw.githubusercontent.com/sodardyjiber/wloc/refs/heads/main/modules/wloc.module

---

## Self-hosting / maintenance notes

If you want the project to keep working even after the original author removes their repo:

1. Keep this repo (`sodardyjiber/wloc`) as the canonical source.
2. Continue using `dist/wloc.js` and `dist/wloc-settings.js` as the script source.
3. Deploy the `worker/` directory as your own page; do not rely on the original upstream Cloudflare Worker URL.
4. Replace the proxy module's `script-path` and `icon` URLs with your fork's `raw.githubusercontent.com/sodardyjiber/wloc/...` URLs.

The project’s core logic is already stored locally in this repository and does not depend on the original upstream repo staying online.

---

## Shortcuts (recommended, most convenient)

Switch / clear the location straight from the Shortcuts app, without opening the picker page:

- **wloc Set Location**: https://www.icloud.com/shortcuts/182f3a014597468eb1b15b99261cdf22
- **wloc Clear & Restore Location**: https://www.icloud.com/shortcuts/0352d53ed79849d382f50e9adf050662

**Usage**

- **Set location:** pick a spot in a Maps app (long-press the map to drop a pin) → Share → choose "wloc Set Location" to switch.
  - Apple Maps: pick a spot → Share → "wloc Set Location"
  - Amap: pick a spot → Share → **More** → "wloc Set Location"
- **Clear location:** tap "wloc Clear & Restore Location" to restore your real location.

Supports Apple Maps and Amap (including short links, with automatic redirect following + GCJ-02→WGS84 coordinate conversion).

---

### About map link parsing (worker)

To make Apple Maps and Amap go through the same flow, links are parsed by your own deployed worker:

- **Amap**: shares produce a short link, and the real coordinates are hidden only in the `Location` header of the 302 redirect — and they are GCJ-02 offset coordinates.
- **Apple Maps**: the link carries `coordinate=lat,lon` directly, but in mainland China these are also GCJ-02 offset coordinates, so the worker performs the GCJ-02→WGS84 conversion before returning them.

**Privacy:** `/api/parse` is a pure forward-and-parse endpoint — it receives a link → follows redirects → parses the coordinates → returns JSON, without writing any storage, logging, or cache.

**Self-hosting:**

```bash
# 1. Clone this repo
git clone https://github.com/sodardyjiber/wloc.git
cd wloc/worker

# 2. Install dependencies
npm install

# 3. Log in to Cloudflare (first time only)
npx wrangler login

# 4. Deploy
npm run deploy
```

After deployment, use your own Worker URL in place of the original public domain.

---

## Recommended workflow

1. Subscribe to the module and enable MITM
2. Open the online picker page (your own Worker / Pages instance)
3. Pick a location on the map / search a place name / paste a map link
4. Tap "Save to Device"
5. It takes effect automatically the next time Apple location is triggered

---

## Final note

This project is now structured as a self-contained fork: the script source, module config, and deployment instructions all point at `sodardyjiber/wloc`, preventing breakage if the upstream project disappears.
