# SoilFER Soil Survey Form Builder

**Live tool:** https://soilfer-collect.yigini.net/ . https://fao-sid.github.io/soilfer-soil-survey-form-builder/

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
- **Six languages:** the interface and the USDA field catalogue (questions, hints, error messages and every option list) are available in English, French, Portuguese, Russian, Chinese and Spanish. See *Languages* below.
- **Checks + export:** the same rules KoBo applies on upload (unique names, existing option lists, valid references), then a one-click `.xlsx` download.

### SoilFER-collect option
- Every field of the official form is on by default. Untick what a country team does not use, rename questions, or add new ones (also inside nested groups such as *Sampling at R0*).
- **Bilingual:** choose which language the form opens in and which language you edit in. New questions get a fallback label until they are translated, and the AI assistant can write the Spanish text.
- **Group conditions are never lost silently.** If you remove a question that gates a group (e.g. the surveyor signature), the group is kept and its condition is paused. It comes back when you tick the question again.
- **Media files:** the form uses `team.csv`, `provinces.csv`, `all_tsus.csv`, `hue.csv` and 37 pictures. The builder lists them all; upload them under the KoBo project's *Settings → Media* before deploying.
- **Fidelity check:** an unmodified SoilFER export was converted with `pyxform` and compared with the original XLSForm. It has the same 204 data paths in the same order, and the same 148 rules (relevance, constraints, calculations). It has the same 514 body elements and 919 of 922 translated labels match. The 3 that differ had no English text in the original: KoBo would show “-” there, and the builder shows the Spanish text instead.

### Languages
- **Interface:** pick English, Français, Português, Русский, 中文 or Español in the top-right corner. The page opens in the browser's language when it is one of these.
- **Exported forms:** tick any combination of the six languages under *Form languages*. The XLSForm gets one `label::Language (code)` column per language (for example `label::French (fr)`), and KoBo Collect and the web form show a language switcher.
- **Form opens in** sets KoBo's `default_language`. **Edit labels in** sets which language you type in when renaming questions.
- Field Book codes stay in brackets in every language (for example *Franco arcilloso (CL)*, *黏壤土（CL）*), so records remain comparable across countries.
- Soil Taxonomy names (orders, suborders, diagnostic horizons, regimes) keep their English form in Russian. In Chinese they get the common Chinese term with the English name in brackets.
- The SoilFER-collect form ships in Spanish and English. You can add other languages; the checks count the labels still to translate, and the AI assistant can fill them in.
- Translations were prepared for this tool. Please have them reviewed by native-speaking soil scientists before large field campaigns, especially Russian and Chinese.

### Built-in form logic (USDA presets)
- The upper depth of each horizon fills in from the lower depth of the previous one; the lower depth must be greater than the upper depth.
- Munsell notation is assembled from hue, value and chroma (e.g. `10YR 3/4`, `N 2.5/`).
- The suborder list is filtered by the soil order picked.
- Structure type lists only *single grain / massive* when the grade is structureless (0).
- The rock-fragment texture modifier is calculated (e.g. *very gravelly* at 35–60 % gravel).
- Sample codes are generated from the profile ID (`MOZ-023-H2`, `MOZ-023-S1`).

## Run it

- **Online:** open the live tool link above. Nothing to install.
- **Offline / locally:** download the repository and open `index.html` in a browser. An internet connection is still needed the first time to load the spreadsheet library.

### Publishing (maintainers)

The site is served by GitHub Pages from the `main` branch, root folder (**Settings → Pages → Deploy from a branch**). In the FAO-SID organization, Pages must be allowed for members (**Organization settings → Member privileges → Pages creation**). Push to `main` and the site updates within a few minutes.

## Use the form in KoboToolbox

1. **Projects → New → Upload an XLSForm**, select the downloaded `.xlsx`.
2. **Deploy**, then collect with KoBo Collect (Android) or the web form.

## Validation

Exports from every survey type were converted with `pyxform` 4.5 and passed ODK Validate. That is the same conversion KoboToolbox runs on upload. The `examples/` folder holds validated exports (`soil_profile_sampling.xlsx`, `topsoil_sampling.xlsx`, `soilFER_001.xlsx`).

## Acknowledgement

This tool was developed under the Soil Mapping for Resilient Agrifood Systems (SoilFER) project, implemented by the Food and Agriculture Organization of the United Nations (FAO) with the support of the Government of Japan and the United States Department of State.

Logos are in `www/logos/`. The page shows them at their original colours and proportions on a white band, in both light and dark mode.

## Licence

- **Code** (`index.html`): [GNU AGPL-3.0](LICENSE).
- **Content** (documentation, example forms, the embedded SoilFER-collect form and the field catalogue): [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
- **Logos** in `www/logos/` are **not licensed** and remain the property of their owners.

See [NOTICE.md](NOTICE.md) for details and the suggested citation:

> FAO. 2026. *SoilFER Soil Survey Form Builder*. Rome. https://github.com/FAO-SID/soilfer-soil-survey-form-builder. Licence: CC BY 4.0.

## Libraries

- [SheetJS](https://sheetjs.com) 0.18.5, Apache-2.0 (reads and writes `.xlsx`)
- [mammoth.js](https://github.com/mwilliamson/mammoth.js) 1.6.0, BSD-2-Clause (reads `.docx`, loaded only when needed)
