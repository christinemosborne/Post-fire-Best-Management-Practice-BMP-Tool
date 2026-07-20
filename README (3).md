# Utah Post-Fire BMP Selection Tool

A standalone, static web app that helps identify best management practices (BMPs)
for stabilizing **hillslopes**, **surface drainages**, and **stream channels**
after wildfire. Rebuilt from the underlying dashboard data (BMP score sheets,
BMP details, and classification thresholds) as a fully independent app — no
Power BI, no server, no build step.

## How it works

1. Pick a treatment area (hillslope, surface drainage, or stream channel).
2. Set as many known site conditions as you have (burn severity, slope,
   precipitation, infrastructure at risk, etc.). Anything left blank is
   skipped in scoring.
3. The app scores every BMP for that area on a 0–20 scale, averaged only
   across the site conditions you provided, and groups them into:
   - **Very Good** (15–20)
   - **Good** (9–14)
   - **Not Recommended** (0–8)
4. Expand any practice to see implementation guidance, maintenance and
   monitoring notes, required skills/materials/equipment, and its original
   reference sources (see below).

All scoring data lives in `data.js` (generated from the original score
spreadsheets) — there's no backend and nothing to configure.

## Sources

Each BMP cites its original reference material (Source A, B, C...). Two kinds:

- **External links** (🔗) — the reference is a live web page, so the app
  links straight to it.
- **Local PDFs** (📄) — the reference is a standalone document (agency
  manual, fact sheet, journal article) with no clean permanent URL, so a
  copy is bundled in this repo under `sources/<category>/` and the app
  links to that local copy instead. These PDFs were recompressed (via
  Ghostscript, `/screen` quality) from the original files to keep the repo
  size reasonable — text and diagrams stay legible, but they're not
  print-resolution.

This means the repo includes about **270 MB of reference PDFs** in
`sources/`. That's within GitHub's per-file limit (nothing here exceeds a
few MB after compression) but is a genuinely large repo. A few things
worth knowing:

- Initial clone/push will be slower than a typical static site.
- If you'd rather not carry these in git history at all, consider
  [Git LFS](https://git-lfs.com) for the `sources/` folder, or host the
  PDFs elsewhere (e.g. a cloud storage bucket) and update the `value`
  field for `"type": "pdf"` entries in `data.js` to point there instead.
- If a repo this size is a problem for your GitHub plan, you can also
  drop `sources/` entirely — the app degrades gracefully and just won't
  show a link for those particular BMPs' local-only references.

## Hosting on GitHub Pages

1. Create a new GitHub repository (or use an existing one) and add these
   files/folders to the repo root: `index.html`, `style.css`, `app.js`,
   `data.js`, and the `sources/` folder.
2. Commit and push them to the `main` branch.
3. In the repo, go to **Settings → Pages**.
4. Under **Build and deployment → Source**, choose **Deploy from a branch**.
5. Under **Branch**, choose `main` and folder `/ (root)`, then **Save**.
6. GitHub will publish the site at:
   `https://<your-username>.github.io/<repo-name>/`
   (it can take a minute or two to go live after the first save).

No further configuration, dependencies, or build tools are required — this is
a plain HTML/CSS/JS static site.

## Updating the underlying data

`data.js` was generated from these source workbooks:

- `UT_BMP_Info.xlsx` / `UT_BMP_Lists___Details_FINAL05June2024.xlsx` — BMP
  names, categories, and descriptive fields (implementation, maintenance,
  monitoring, sources).
- `UT_Hillslope_BMP_Scores.xlsx`, `UT_SurfDrain_BMP_Scores.xlsx`,
  `UT_Stream_BMP_Scores.xlsx` — per-parameter suitability scores (0–20) for
  each BMP.
- `Score_Classifications_06242024.xlsx` — the Very Good / Good / Not
  Recommended thresholds.
- The `Hillslope_BMPs`, `Surface_Drainage_BMPs`, and `Stream_Channel_BMPs`
  reference folders — the underlying Source A–E documents/links per BMP,
  used to populate the sources shown in the app (see "Sources" above).

If the source data changes, re-run the extraction against the updated
workbooks and replace `data.js` — the app code (`app.js`) doesn't need to
change unless parameter names or category structure change.

## Local preview

Since this is a static site, you can preview it locally with any simple
HTTP server, e.g.:

```bash
python3 -m http.server 8000
```

then open `http://localhost:8000` in a browser. (Opening `index.html`
directly via `file://` may work in some browsers but a local server is more
reliable.)
