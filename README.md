# MoralLLM-Lab

A single-file, browser-only laboratory for probing the moral reasoning of Large Language Models through Kantian deontology, utilitarian ethics, and four companion framings. By **Prof. Dr. Utku Köse**.

**Deploy:** drop `index.html` onto any static host. Works offline by default — 90 cached model responses travel with the file. No build step, no dependencies.

**Languages:** English (default) and Turkish — including a full Turkish translation of all 15 dilemmas, tensions, and the entire interface.

---

## Contents

1. [Home](#home)
2. [Playground — single-shot probe](#playground)
3. [Duel — head-to-head three-round debate](#duel)
4. [Compare — multiple framings, same dilemma](#compare)
5. [Experiment — the full 15 × 6 matrix](#experiment)
6. [Custom — write your own dilemma](#custom)
7. [Exporting results](#exports)
8. [Execution modes](#modes)

---

<a name="home"></a>
## 1. Home

![Home view](screenshots/01_home.png)

The landing page introduces the instrument and lays out the four-step experimental protocol on the right: **select a dilemma → frame the question → elicit the answer → measure four axes**.

Four primary entry buttons — Playground, Duel, the method page, and References. The top bar holds the author attribution, the eight view tabs, the EN / TR language toggle, and the settings gear. The Duel tab is visually highlighted in red because that view is the centrepiece of the instrument for webinar use — but it is only active when actually selected, not on page load.

---

<a name="playground"></a>
## 2. Playground

![Playground with response, metrics, and radar](screenshots/02_playground.png)

Pick one of 15 dilemmas (classical / modern / biomedical) and one of 6 framings (neutral, Kantian, utilitarian, two pressured variants, role-of-philosopher). The model's full answer appears in the top panel with **inline colour-coded highlighting**: red for deontological vocabulary, green for utilitarian, gold for hedging.

Below the answer, three analysis cards:

- **Metric Analysis** — six bar metrics, each with a hoverable `?` tooltip explaining exactly how it is computed
  - Deontological density — fraction of 22 Kantian terms matched
  - Utilitarian density — fraction of 18 consequentialist terms matched
  - Hedging density — fraction of 17 evasion terms matched
  - Alignment with framework — framework-density / (deon + util)
  - Value drift — 1 minus alignment
  - Response length in tokens

- **Reasoning Profile** — five-axis radar chart summarizing the answer's shape at a glance

- **Detected terms** — every lexicon word that actually fired, colour-coded

- **Automatic Interpretation** — a plain-language paragraph filled with the actual counts

Each elicited answer can be exported as JSON (full scoring + term hits) or CSV (flat row).

---

<a name="duel"></a>
## 3. Duel

![Duel verdict with comparative chart and overlay radar](screenshots/03_duel_verdict.png)

The format for webinars. Pick one dilemma, pick two framings, and the instrument plays out three rounds — **opening → rebuttal → closing** — recording both sides' full prose. The verdict panel declares who held firmer (by alignment differential), shows a per-side metric table, and renders two **comparative visualisations**:

- **Metric comparison** — five paired horizontal bars (alignment, drift, hedging, deontological density, utilitarian density) with both sides' exact numbers written next to their bars
- **Reasoning profile** — two radar polygons on the same five axes, one solid and one dashed, so convergence and divergence are visible at a glance

Below the verdict, five **open questions for further debate** — framed as discussion prompts rather than instructional questions.

Duel results export as JSON (nested per-round-per-side structure) or CSV (one row per round-side pair, six rows total per duel).

---

<a name="compare"></a>
## 4. Compare

![Compare view — four framings side by side](screenshots/04_compare.png)

Pick a dilemma and 2 to 6 framings. The selected framings render as coloured chips at the top; the responses appear as a grid of cards, each colour-coded by framework kind (red = deontological, green = utilitarian, gold = neutral, red-tinted for pressured variants).

Each card shows the framing's label, a compact summary strip (align / drift / hedge), and the full response. Useful for spotting where framings converge on the same verdict and where they sharply diverge.

Compare exports as JSON (all selected framings) or CSV (one row per framing).

---

<a name="experiment"></a>
## 5. Experiment

A one-click run of all 15 dilemmas × 6 framings = 90 prompts, with four visualisations.

### 5a. Alignment heatmap

![Experiment alignment heatmap](screenshots/05_experiment_heatmap.png)

Each cell is the alignment score for a (scenario, framework) pair. Colour scales by framework kind (red for Kantian columns, green for utilitarian, gold for neutral and role-of-philosopher). Hover any cell for details; **click to jump directly into the Playground with that pair pre-selected**, which then re-runs the prompt. The heatmap makes structural patterns immediately legible — for example, biomedical dilemmas often show higher alignment in utilitarian columns than classical ones do.

### 5b. Normative consistency & Prompt sensitivity

![Consistency and sensitivity charts](screenshots/06_experiment_charts_1_2.png)

- **Normative consistency** — grouped bars of mean alignment by template (Kantian deontology, utilitarian ethics, Kantian pressured, utilitarian pressured) and scenario category (classic / modern / biomedical). Reveals how steadily the model holds a framework across structurally similar cases.
- **Prompt sensitivity** — horizontal bars, one row per scenario, showing the lexical spread (max − min) of deontological and utilitarian vocabulary across the six framings. Tall spreads mean the model's lexical output depends heavily on how we asked — low spreads mean the answer's vocabulary is scenario-determined, not prompt-determined.

### 5c. Justification quality & Value drift

![Quality radar and drift plot](screenshots/07_experiment_charts_3_4.png)

- **Justification quality** — a six-axis radar over all templates, where quality = normalized length × (1 − hedge density) × alignment. Captures whether a framing produces long, committed, on-framework answers or short hedging ones.
- **Value drift** — a clean vs. pressured comparison. Each row is (framework × category). The light circle marks drift under the clean prompt; the filled circle marks drift under the pressured variant. An arrow between them means pressure increased drift. Short segments or no arrow mean the framework held under pressure.

All 90 results export as JSON (full response text + all scores) or CSV.

---

<a name="custom"></a>
## 6. Custom

![Custom scenario authoring form](screenshots/08_custom.png)

Write a scenario the library doesn't cover — a short title, a description, and optional deontological / utilitarian tensions. Saved scenarios appear in the right-hand list with **Open in playground** and **Delete** actions. Data lives in `localStorage`, so custom scenarios persist across visits but stay on the user's device.

Custom scenarios can be exported (JSON / CSV) — useful for sharing a scenario set with colleagues or saving a backup.

Custom scenarios can be tested under any of the six framings (via Playground) but will only work under Live API mode, since the cache only contains responses for the 15 built-in dilemmas.

---

<a name="exports"></a>
## 7. Exporting results

Every view that produces quantitative output exposes **JSON / CSV export buttons**:

| View | When it appears | What it contains |
|------|-----------------|------------------|
| **Playground** | After eliciting a response | Single-response row: all metrics, term hits, mode, language, timestamp |
| **Duel** | On the verdict panel | Full nested structure (3 rounds × 2 sides) or flat CSV (6 rows) |
| **Compare** | After framings render | One row per selected framing, with scores |
| **Experiment** | After the 15×6 run completes | All 90 rows with full response text |
| **Custom** | When your library is non-empty | Your saved scenarios |

Downloads use the browser's native save dialog. Filenames follow the pattern `morallm_<view>.<ext>`.

---

<a name="modes"></a>
## 8. Execution modes

**Cached (default, offline).** 90 responses pre-generated with an LLM (Claude) during development ship inside the HTML file. No API key, no network, fully reproducible. Use this for webinars, offline demos, and teaching.

**Live API.** Open Settings (gear icon), switch to Live API mode, pick Anthropic or OpenAI, paste an API key (stored only in `localStorage`, never transmitted except to the provider you pick). Elicit fresh answers for any dilemma — including custom ones. Rebuttals inject the opponent's opening into the prompt so the debate is genuinely cross-referential.

---

## Author

**Prof. Dr. Utku Köse**
Süleyman Demirel University · University of North Dakota · VelTech · Universidad Panamericana
[utkukose@sdu.edu.tr](mailto:utkukose@sdu.edu.tr) · [www.utkukose.com](https://www.utkukose.com)

## License

MIT.
