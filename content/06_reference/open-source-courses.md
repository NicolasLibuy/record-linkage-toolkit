# Building the capability

This toolkit assumes working familiarity with Python and no previous experience
of probabilistic linkage. If the first of those is missing, that is a gap worth
closing deliberately rather than working around — and it can be closed with free
material.

[Chapter 4.1](../04_production/adoption-challenges.md) lists "one person holds the
capability" as one of the six things that stop linkage projects. Training two or
three people is the cheapest available insurance against it, and unlike
consultancy it leaves the capability inside the office.

Everything listed here is free.

## For the linkage method itself

### Splink's own tutorial

[Seven-part tutorial](https://moj-analytical-services.github.io/splink/demos/tutorials/00_Tutorial_Introduction.html)

The best starting point after this toolkit, and a useful cross-check: it works
through the same pipeline on different data, so where the two agree you have
understood the method rather than memorised one example.

Roughly a day's work. Runs in the browser without installing anything.

### Splink's topic guides

[Topic guides](https://moj-analytical-services.github.io/splink/topic_guides/topic_guides_index.html)

Organised as record linkage theory, linkage models in Splink, data preparation
and blocking. More detailed than the tutorial, and the right place to go when you
hit a specific question — how a particular comparison level works, why blocking
behaves as it does, what a setting actually controls.

Reference material rather than a course. Read the pieces you need.

### Robin Linacre's probabilistic linkage articles

[Probabilistic linkage training](https://www.robinlinacre.com/probabilistic_linkage/)

A set of **interactive** articles on the Fellegi–Sunter model by Splink's lead
developer: an introduction to the framework, how it is computed algorithmically,
the derivation of the mathematics, and visualisations of how match weights behave
— including a piece on dependencies between match weights, which is the
independence assumption this toolkit flags in
[chapter 2.4](../02_producing/linkage-approaches.md).

Strongly recommended if the mathematics in chapter 2.4 felt like something to be
taken on trust. Being able to move the parameters and watch the weights respond
does more for intuition than any amount of prose, this toolkit's included.

## For Python

The toolkit needs less Python than people expect: reading a CSV, selecting
columns, cleaning strings, grouping and counting. Anything billed as an
introduction to pandas covers it.

Two things to prioritise over general Python fluency, because they are what
linkage work actually depends on:

- **pandas for tabular data** — reading files with explicit types, selecting,
  filtering, grouping, joining. Chapter 2.2's preparation work is essentially
  this.
- **Jupyter notebooks** — running cells, keeping a notebook reproducible from top
  to bottom, and knowing why out-of-order execution causes trouble.

You do not need object-oriented programming, decorators, async, or web
frameworks.

## For official statistics generally

### UN SIAP e-learning

[SIAP e-learning portal](https://siap-elearning.org/)

The Statistical Institute for Asia and the Pacific runs a free e-learning portal
for official statistics, covering methodology and statistical process,
population and social statistics, economic statistics, SDG indicators and the
principles of official statistics. Some courses are self-paced; others are
facilitated by an expert and open to staff nominated by national statistical
offices.

Registration is free. Relevant here for the methodology and statistical process
material, which is the context linkage sits inside — and useful for colleagues
who need to understand what a linkage project is for without implementing one.

## A suggested route

For an office starting from no linkage capability, roughly two to three weeks of
part-time effort spread across three people:

| Step | Material | Who |
|:--|:--|:--|
| 1 | pandas and Jupyter basics | Whoever will run the pipeline |
| 2 | Sections 1 and 2.1–2.3 of this toolkit | The whole team, including managers |
| 3 | Robin Linacre's interactive articles | Whoever owns the method |
| 4 | Sections 2.4–2.9, running every notebook | The pipeline runners |
| 5 | Splink's own tutorial, on its data | The pipeline runners |
| 6 | Section 3, then draft a quality statement for a pretend linkage | Method owner and manager |
| 7 | Section 4, then complete the readiness check for your real case | The whole team, with legal |

Step 6 is the one most likely to be skipped and most worth keeping. Writing a
quality statement for a linkage that does not exist yet exposes, cheaply, every
question the real project will have to answer.

Step 7 usually reveals that the binding constraint is not technical — which is
the finding [Section 4](../04_production/adoption-challenges.md) reports from
eight national teams, and the reason to reach it early rather than after the
model is built.
