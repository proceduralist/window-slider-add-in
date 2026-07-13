# Window Slider — PowerPoint add-in (Mac setup)

An interactive image-comparison slider that runs **live inside your PowerPoint slide, including during Slide Show**. Load two versions of the same image (e.g. the same CT slice at two window/level settings) and drag to reveal one over the other. Works side-by-side (↔) or top/bottom (↕).

**Files in this folder**

| File | What it is |
|---|---|
| `index.html` | The slider itself (the web app the add-in shows). **Gets hosted.** |
| `icon-32.png`, `icon-64.png`, `icon-80.png` | Add-in icons. **Get hosted.** |
| `manifest.xml` | Tells PowerPoint about the add-in. **Goes in a local PowerPoint folder — NOT hosted.** |

---

## Try it first (30 seconds, no setup)

Double-click `index.html` to open it in your browser. Load two images, drag to compare, toggle orientation. This is exactly what appears inside PowerPoint — a good way to confirm it does what you want before installing.

---

## Step 1 — Put the web files online (HTTPS required)

Office add-ins must load over **https**. Easiest free option is **Netlify Drop** (no account or git needed):

1. Go to **https://app.netlify.com/drop**
2. Drag this whole **`window-slider` folder** onto the page.
3. You'll get a URL like `https://calm-otter-8421.netlify.app`. The part after `https://` (e.g. `calm-otter-8421.netlify.app`) is your **host**.
4. **Recommended:** click **Claim / Sign up** (free) so the site stays permanent. Anonymous drops expire after ~1 hour.

Any static host with HTTPS works (GitHub Pages, Cloudflare Pages, your own web server). If you use GitHub Pages, your files live under a `/repo-name/` subpath — include that subpath everywhere you see `YOUR-HOST` below.

Confirm it's live by visiting `https://YOUR-HOST/index.html` — you should see the slider.

## Step 2 — Point the manifest at your host

Open `manifest.xml` in any text editor and **find-and-replace every `YOUR-HOST`** (there are 5) with the host from Step 1. Save.

Example: `https://YOUR-HOST/index.html` → `https://calm-otter-8421.netlify.app/index.html`

(The GUID in the file is already filled in — leave it.)

## Step 3 — Sideload the add-in on PowerPoint for Mac

Put the edited `manifest.xml` in PowerPoint's add-in folder. Open **Terminal** and run:

```bash
mkdir -p ~/Library/Containers/com.microsoft.Powerpoint/Data/Documents/wef
cp ~/Downloads/window-slider/manifest.xml ~/Library/Containers/com.microsoft.Powerpoint/Data/Documents/wef/
```

(Adjust the first path if the folder isn't in Downloads.)

Then **quit and reopen PowerPoint**.

## Step 4 — Insert it on a slide

1. Go to the slide where you want the comparison.
2. **Insert** tab → **My Add-ins** (the add-ins button) → the dialog opens.
3. Under **Developer Add-ins**, double-click **Window Slider**. A box appears on the slide.
4. Resize/position it like any object.
5. Click **Image A** and **Image B** (or drag images in, or paste with ⌘V) to load your two pictures.
6. Optional: click the ⚙️ gear to add labels (e.g. "Lung window" / "Mediastinal window").
7. **File → Save.** This embeds the images inside the `.pptx` so they're there when you present.

## Step 5 — Present

Start **Slide Show**. On that slide, **drag anywhere** in the image (or drag the round handle) to reveal more of one image or the other. Double-click to re-center. It's fully interactive while presenting.

Keyboard while presenting: **← → ↑ ↓** nudge the divider · **O** toggles orientation · **S** swaps the two images.

---

## Multiple comparisons in one deck

Document settings are shared across the whole presentation, so if you add **more than one** slider to the same deck, give each one a different **Slot** (⚙️ gear → Slot 1, 2, 3…). Each slot stores its own image pair. If you only have one slider, ignore this.

> Note: after fully closing and reopening the deck, a multi-slot deck may need you to re-select each slider's slot in edit mode. A single slider (Slot 1) always restores automatically. This is a limitation of how PowerPoint scopes add-in storage, not a bug.

## Good to know

- **Alignment:** use two images of the **same pixel dimensions** (same series/slice, just re-windowed) so anatomy lines up exactly. Both images are fit identically inside the box.
- **Image size:** images are automatically downscaled to 1920 px and embedded in the deck. Keep originals reasonable so the `.pptx` doesn't bloat.
- **Internet at show time:** the slider *code* loads from your host, so the presenting machine needs internet. The *images* are embedded in the deck and travel with the file.
- **Presenting on a different Mac?** Sideload `manifest.xml` on that machine too (repeat Step 3), or have your M365 admin deploy it centrally. The add-in is registered per computer.
- **Updated `index.html` but PowerPoint shows the old version?** Quit PowerPoint and clear its cache:
  `rm -rf ~/Library/Containers/com.microsoft.Powerpoint/Data/Library/Caches/*` then reopen.
- **Remove the add-in:** delete `manifest.xml` from the `wef` folder and restart PowerPoint.

## Troubleshooting

- *Add-in doesn't appear in My Add-ins:* confirm `manifest.xml` is in the `wef` path above and you fully quit/reopened PowerPoint. Make sure you're signed into Office with a Microsoft account.
- *Blank / won't load:* visit `https://YOUR-HOST/index.html` in Safari — if it doesn't load there, the hosting/URL is the issue. Check every `YOUR-HOST` was replaced and the URL is `https`.
- *"Not saved" indicator:* the images may be too large for add-in storage — use smaller/downscaled source images. The slider still works for the current session.
