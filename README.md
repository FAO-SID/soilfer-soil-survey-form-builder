# Soil Survey Form Builder

A single-page web app that builds KoboToolbox / ODK forms (XLSForm `.xlsx`) for soil survey fieldwork. It runs entirely in the browser, with no server and no build step.

## What it does

- **Survey types:** profile description + sampling, profile description, auger observation, topsoil (composite) sampling, **SoilFER-collect**, or start from scratch.
- **Standards:**
  - **USDA:** the field catalogue follows the *Field Book for Describing and Sampling Soils* v3.0 (Schoeneberger et al., 2012) and the *Keys to Soil Taxonomy*.
  - **SoilFER-collect v1.0 (FAO, 2024):** the official SoilFER form, embedded in full. It covers consent, R0/P1–P3 topsoil and subsoil sampling, soil and site description, the land cover repeat and additional field tests, with Spanish and English labels.
  - FAO Guidelines and WRB are planned next.
- **Customise:** tick fields on or off, rename questions, switch fields between required and optional, add your own fields or sections. Conditions are tracked, so removing a question also removes the questions that depend on it.
- **Import:** upload an existing XLSForm to merge its questions in, or a protocol document (`.docx`, `.txt`, `.md`) for the AI assistant to read.
- **AI assistant (optional):** describe the changes you want in plain words. The assistant proposes edits, and you review them before they are applied. Users bring their own API key (Anthropic, OpenAI or Google Gemini). Keys stay in the browser.
- **Checks + export:** the same rules KoBo applies on upload (unique names, existing option lists, valid references), then a one-click `.xlsx` download.

### SoilFER-collect option
- Every field of the official form is on by default. Untick what a country team does not use, rename questions, or add new ones (also inside nested groups such as *Sampling at R0*).
- **Bilingual:** choose which language the form opens in and which language you edit in. New questions get a fallback label until they are translated, and the AI assistant can write the Spanish text.
- **Group conditions are never lost silently.** If you remove a question that gates a group (e.g. the surveyor signature), the group is kept and its condition is paused. It comes back when you tick the question again.
- **Media files:** the form uses `team.csv`, `provinces.csv`, `all_tsus.csv`, `hue.csv` and 37 pictures. The builder lists them all; upload them under the KoBo project's *Settings → Media* before deploying.
- **Fidelity check:** an unmodified SoilFER export was converted with `pyxform` and compared with the original XLSForm. It has the same 204 data paths in the same order, and the same 148 rules (relevance, constraints, calculations). It has the same 514 body elements and 919 of 922 translated labels match. The 3 that differ had no English text in the original: KoBo would show “-” there, and the builder shows the Spanish text instead.

### Built-in form logic (USDA presets)
- The upper depth of each horizon fills in from the lower depth of the previous one; the lower depth must be greater than the upper depth.
- Munsell notation is assembled from hue, value and chroma (e.g. `10YR 3/4`, `N 2.5/`).
- The suborder list is filtered by the soil order picked.
- Structure type lists only *single grain / massive* when the grade is structureless (0).
- The rock-fragment texture modifier is calculated (e.g. *very gravelly* at 35–60 % gravel).
- Sample codes are generated from the profile ID (`MOZ-023-H2`, `MOZ-023-S1`).

## Deploy on GitHub Pages

1. Create a repository and add `index.html` (and this README).
2. **Settings → Pages → Build and deployment → Deploy from a branch**, choose `main` and `/ (root)`.
3. The site is live at `https://<user>.github.io/<repo>/` within a minute.

## Use the form in KoboToolbox

1. **Projects → New → Upload an XLSForm**, select the downloaded `.xlsx`.
2. **Deploy**, then collect with KoBo Collect (Android) or the web form.

## Validation

Exports from every survey type were converted with `pyxform` 4.5 and passed ODK Validate. That is the same conversion KoboToolbox runs on upload. The `examples/` folder holds validated exports (`soil_profile_sampling.xlsx`, `topsoil_sampling.xlsx`, `soilFER_001.xlsx`).

## Libraries

- [SheetJS](https://sheetjs.com) 0.18.5 (reads and writes `.xlsx`)
- [mammoth.js](https://github.com/mwilliamson/mammoth.js) 1.6.0 (reads `.docx`, loaded only when needed)
