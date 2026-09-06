# Related toolkits and projects

Four resources that sit alongside this one. For each, what it covers, and how it
relates to what you have just read — including where it does the job better.

## Data Linking Toolkit — UN Women, UNECA and UNSD (2025)

[Publication page](https://africa.unwomen.org/en/digital-library/publications/2026/01/data-linking-toolkit)

The closest companion to this toolkit, and the one to read alongside it.

It is organised around **five linkage methodologies** rather than one: individual
record linkage (deterministic and probabilistic), aligning census with
post-enumeration survey data, linking individuals or households to service
delivery points including geospatially, integrating survey with administrative
data to enhance indicators, and enriching survey data with aggregate
administrative data. Each comes with a concept explanation, a real case study,
R-based implementation guidance, and a discussion of prerequisites — legal
frameworks, data-sharing agreements, metadata standards, secure handling. Its
framing is gender data and intersectional disaggregation.

**How the two differ.** That toolkit surveys five methodologies; this one goes
deep on one. If your question is *which* kind of linkage suits your problem,
start there. If you have decided on individual record linkage and need to
implement it, this toolkit takes you further into that one method: blocking at
scale, EM estimation, threshold selection, clustering, error measurement and
reproducible documentation, with runnable code at each step and a dataset to run
it on.

They are complements, and the boundary is clean: **appraise there, implement
here.** Three of that toolkit's five methodologies — census–PES alignment,
service-location linking, aggregate enrichment — are not covered here at all.

## SAE4SDG — UNSD

[SAE4SDG wiki](https://unstats.un.org/wiki/spaces/SAE4SDG/pages/66060512/SAE4SDG)

UNSD's toolkit on **small area estimation**, and the structural model this one
follows: why the method matters, producing estimates, communicating them, moving
from experiment to production, country practices, reference materials, FAQ.

Worth knowing about for two reasons beyond the structure.

**The problems are adjacent.** SAE and record linkage are two answers to the same
pressure — the demand for disaggregated statistics that a single survey cannot
support. An office facing that demand should know which tool fits: linkage when
the information exists in another source and needs connecting; small area
estimation when it does not exist anywhere and has to be modelled.

**They combine.** A linked dataset can be the input that makes a small area model
possible, by supplying auxiliary variables at unit level.

Its country practices section, which has grown over time and continues to add
contributors, is the model for
Section 5 of this toolkit.

## Developing standard tools for data linkage — ONS (2021)

[Working paper](https://www.ons.gov.uk/methodology/methodologicalpublications/generalmethodology/onsworkingpaperseries/developingstandardtoolsfordatalinkagefebruary2021)

A working paper pairing linkage theory — deterministic versus probabilistic, the
EM algorithm — with seven reusable PySpark functions for diagnostics,
candidate-pair generation and m/u estimation, each documented with its parameters
and outputs.

Read it if you work in Spark. Read it anyway for something rarer: a national
statistical office reasoning **in public** about how to standardise linkage
across an organisation, including why each function exists and what it is meant
to prevent.

## Using Alternative Data to Assess Immediate Economic Impacts of Crises — World Bank

[Repository](https://github.com/worldbank/alternative-data-for-crisis)

Not about record linkage at all — it covers alternative data sources for crisis
impact assessment.

It appears here because it is the **format** this toolkit imitates: a Jupyter
Book published through GitHub Pages, where executable notebooks are chapters
alongside the narrative rather than attachments to it. That is what allows code
to sit in the sub-section that explains it instead of in an annex.

If you are building a technical resource for your own office and want a working
model of the publication mechanics — repository layout, table of contents,
continuous deployment — that repository is a good one to copy, as this one did.

## How this toolkit positions itself

Stated plainly, since it is a fair question.

**Narrower and deeper.** One family of methods, worked through to the point of
implementation rather than appraisal.

**Runnable, not illustrative.** Open-source Python and Splink code in each
relevant sub-section, executed against data that ships with the toolkit, with
every quoted figure produced by the accompanying notebook.

**The production end.** Blocking at scale, EM estimation, threshold selection,
clustering, linkage error and bias, evaluation and reproducible documentation —
the steps that decide whether a pilot becomes a statistical output.

**Reproducible without data access.** A synthetic dataset with known ground truth
ships with it, so the entire pipeline including its error rates can be run before
touching national data.

**Honest about its own failures.** The reproducibility checklist in
[chapter 3.1](../03_quality/communicating-methods.md) marks two items as failed,
because this toolkit fails them.

Where another resource does something better, this one says so. That is what the
page you are reading is for.
