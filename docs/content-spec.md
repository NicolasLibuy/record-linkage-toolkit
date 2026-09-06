# Content specification

Working document, not part of the published book. It records what each page of
the toolkit contains, what is deliberately out of its scope, and what source
material it is written from, so that scope stays settled while the toolkit is
built out section by section.

The structure follows the outline agreed with UNSD (Output 8, v2, delivered
5 September 2026), which answers the fourteen review comments returned on
26 August: the scoped title, a cover page and contributors section, a statement
of purpose and value added, code in every relevant sub-section rather than an
annex, alignment with GSBPM v5.2, Country Practices as a living section
restricted to cases in production, publication on GitHub as a Jupyter Book, and
English.

**Status legend:** ✅ written · 🚧 in progress · ⬜ not started

## Front matter

| Page | Status | Scope | Written from |
|:--|:--|:--|:--|
| `README.md` (cover) | ✅ | Background and purpose including how the toolkit came about; what to expect; what not to expect; how to approach it, with the key considerations (Splink- and Python-centric, alternatives in Reference Materials, English for now, synthetic data included) | Outline v2; Concept Note |
| `00_front/how-to-use.md` | ✅ | How the book is organised; three reading paths; installing and running the notebooks; the pre-executed-notebook policy; the GSBPM v5.2 mapping | Outline v2 |
| `00_front/contributors.md` | ✅ | Author, UNSD, the eight participating offices, guest contributions, data provenance, how to contribute a country practice | New |

## Section 1 — Why record linkage matters

No code. This section is the case for doing linkage at all; method detail belongs to Section 2.

| Page | Status | Scope | Written from |
|:--|:--|:--|:--|
| `demand-for-linked-data.md` | ✅ | Demand for integrated data; SDG monitoring and disaggregation; the Data for Now rationale; what linkage makes possible that one source cannot | Lecture 1; Concept Note |
| `limits-of-single-source.md` | ✅ | Coverage, timeliness and cost limits of survey-only production; ad-hoc linkage vs. permanent linkage systems; population registers; established national examples | Lecture 1 |
| `linkage-as-a-solution.md` | ✅ | Deterministic vs. probabilistic at a glance and when each applies; what linkage cannot fix; privacy, confidentiality, consent and public acceptability as design constraints | Lecture 1 |

## Section 2 — Producing linked data

The core of the toolkit. One narrative page per sub-section of the agreed
outline; a notebook accompanies every page that has code.

| # | Page | Notebook | Status | Scope | Written from |
|:--|:--|:--|:--|:--|:--|
| 2.1 | `defining-the-use-case.md` | — | ✅ | Turning a policy question into a linkage specification: statistical objective, unit of analysis, link type, what counts as a match, what the output must support | Lecture 1; country project template §1–3 |
| 2.2 | `data-readiness.md` | `nb01-inspect-and-prepare` | ✅ | Source-system review; identifiers available; completeness and cardinality profiling; the three Splink prerequisites; cleaning and standardising names, sex and nationality; the standardisation trade-off | Lectures 2–3, 8; Tutorial 1 |
| 2.3 | `anonymisation.md` | `nb02-pseudonymisation` | ✅ | Anonymisation vs. pseudonymisation; threat model; peppered SHA-256 keys; the separation principle; what hashing does not protect against | Lecture 4; Tutorial 2 |
| 2.4 | `linkage-approaches.md` | `nb03-deterministic-rules`, `nb04-fellegi-sunter-by-hand` | ✅ | Deterministic rules (strict, N-1, stepwise, match-key), non-disagreement clauses, false vs. missed matches; then Fellegi–Sunter from first principles: m and u probabilities, likelihood ratios, match weights, the independence assumption | Lectures 5–6; Tutorials 3–4 |
| 2.5 | `blocking.md` | `nb05-blocking-strategies` | ✅ | Why all-pairs comparison is impossible; simple, conjunctive, disjunctive and phonetic rules; reduction ratio, pair completeness, pairs quality; sorted neighbourhood; the recall ceiling a rule set imposes | Lecture 7; Tutorial 5 |
| 2.6 | `implementing-in-splink.md` | `nb06-splink-settings-and-blocking`, `nb07-comparisons-and-training` | ✅ | Settings and link types; `block_on()`; the comparison library; phonetic encoding and term-frequency adjustments; estimating λ, u and m by EM; training rules; saving the model | Lectures 8–10; Tutorials 6–8 |
| 2.7 | `thresholds-and-clustering.md` | `nb08-thresholds-and-clustering` | ✅ | Reloading a saved model; reading the score distribution; threshold sensitivity and the precision/recall trade-off; clustering pairs into entities; exporting a linked dataset | Lecture 11; Tutorial 9 |
| 2.8 | `evaluation.md` | `nb09-evaluation` | ✅ | Evaluating against a ground truth where one exists, and the four practical methods where none does; accuracy tables, ROC, precision–recall; clerical review; choosing the operating threshold | Lecture 12a; Tutorial 10 |
| 2.9 | `end-to-end-example.md` | `nb10-end-to-end` | ✅ | The whole pipeline in one uninterrupted notebook on the bundled data, as the reference implementation to copy | Condensed from 2.2–2.8 |

## Section 3 — Communicating and assessing linkage quality

| Page | Notebook | Status | Scope | Written from |
|:--|:--|:--|:--|:--|
| `communicating-methods.md` | — | ✅ | What to publish about a linkage, for a general and for a technical audience; a documentation template; an eight-item reproducibility checklist | Lecture 12b; Tutorial 11 |
| `communicating-results.md` | `nb11-charts-and-exports` | ✅ | Match-weight chart, waterfall chart, m/u parameter chart, comparison viewer dashboard, cluster outputs: what each is for, how to read it, how to export | Lectures 11, 12b; Tutorials 9, 11 |
| `quality-metric-reference.md` | — | ✅ | Single reference page for every metric the toolkit uses: match rate; RR/PC/PQ; the confusion matrix; sensitivity, specificity, PPV, NPV, F-measure; clerical review; plausibility checks; linkage bias | Lectures 7, 10, 12a |

## Section 4 — From pilot to production

| Page | Status | Scope | Written from |
|:--|:--|:--|:--|
| `adoption-challenges.md` | ⬜ | The non-technical barriers that stop pilots: no legal basis, no data-sharing agreement, scarce Python capacity, no owner for the pipeline, no re-run schedule | Guest sessions; country templates; Output 9 findings |
| `enabling-environment.md` | ⬜ | Legal frameworks and authorisations; data-sharing agreements; governance and disclosure control; staffing and skills; infrastructure; model versioning, re-running and drift | Chile MDSF/RIS guest session; the Splink/UK MoJ adoption story; verified NSO reports |

## Section 5 — Country practices

A living section, open to offices beyond the original cohort.

| Page | Status | Scope |
|:--|:--|:--|
| `linkage-by-use-case-domain.md` | ⬜ | Organised by domain: civil registration and vital statistics, health, migration, social protection, business and population registers, with the identifiers and failure modes typical of each |
| `country-case-examples.md` | ⬜ | The living list, plus the publication criterion and a submission outline. Only linkage **in production or in use for official statistics** is published; a proposal is not yet a practice. Launches without cohort cases: as of September 2026 none of the country projects had reached production. Populated case by case in agreement with UNSD |
| `other-practices.md` | ⬜ | Externally documented national cases, each verified against the primary document: UK (ONS/UKSA), Uruguay (INE, Censo Combinado 2023), Colombia (DANE REBP; DANE/JEP/CEV/HRDAG deduplication), Chile (Registro Social de Hogares, as a contrasting deterministic case). Statistics Norway's business register remains a named placeholder pending a reference from UNSD |

## Section 6 — Reference materials

| Page | Status | Scope |
|:--|:--|:--|
| `software.md` | ⬜ | By platform. Python: Splink, recordlinkage, DuckDB, phonetics. PySpark: the ONS diagnostic and EM functions. Other: RELAIS, fastLink |
| `key-readings.md` | ⬜ | Fellegi & Sunter (1969); Linacre et al. (2022); Harron et al. (2016); Herzog, Scheuren & Winkler; the ONS working paper. Backed by `docs/bibliography.bib` |
| `related-toolkits.md` | ⬜ | SAE4SDG; ONS *Developing standard tools for data linkage* (2021); UN Women/UNECA/UNSD *Data Linking Toolkit* (2025); the World Bank alternative-data book |
| `open-source-courses.md` | ⬜ | Splink's own tutorial and topic guides; Robin Linacre's probabilistic linkage training; UN SIAP e-learning; Python foundations |

## Section 7 — FAQ

| Page | Status | Scope |
|:--|:--|:--|
| `faq.md` | ⬜ | A running list, grouped: legal and privacy; data preparation; deterministic vs. probabilistic; Splink specifics and error messages; quality and thresholds; scale and infrastructure |

Seeded with API behaviour verified against `splink==4.0.0`:

- `waterfall_chart()` fails unless `retain_intermediate_calculation_columns=True` is set on `SettingsCreator`.
- `accuracy_analysis_from_labels_column(output_type="table")` returns `truth_threshold` in **match-weight** units, not probability; passing it straight to `threshold_match_probability` is silently wrong.
- `estimate_u_using_random_sampling()` is not fixed by `random_state`, so the chosen threshold moves between runs.
- `count_comparisons_from_blocking_rule` is a top-level function in `splink.blocking_analysis`, not a `linker` method.

## Conventions

- **Narrative, not slides.** Pages are written afresh. Nothing carries over course framing: no milestones, no deadlines, no cohort references, no transcribed slides.
- **Code where the step is explained**, not in an annex.
- **One running example.** Every notebook uses the bundled synthetic registers, presented as one illustrative case rather than the only frame of reference.
- **Notebooks are committed pre-executed** and `execute_notebooks` is `off`, because Splink's u estimation is not seeded and CI execution would silently change published figures.
- **Numbers are real.** Every figure quoted in the narrative comes from the committed run of the accompanying notebook.
- **The TOC grows with the content**, so the published site never shows placeholder pages.

## Open items

- Licence choice (currently CC BY 4.0 for text, MIT for code) to confirm with UNSD.
- Reference for the Statistics Norway business register case, requested from UNSD.
- Which country cases, if any, meet the production criterion, following the bilateral sessions of 14–15 September 2026.
