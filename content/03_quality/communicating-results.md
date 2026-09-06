# Communicating results

Four diagnostic charts come out of a linkage model, and none of them belongs in a
statistical bulletin. They are instruments for the team that built the linkage.
A general audience needs two charts, and they are different charts.

The accompanying notebook,
[Charts and exports](nb11-charts-and-exports.ipynb), produces all of them.

## The four diagnostics

### Match weights — what the model believes

The model in one picture: every comparison level, and what agreement or
disagreement at that level is worth in bits.

**Use it to** sanity-check the model before trusting its output, and to explain
to a technical reviewer what the model does.

**Look for:**

- *A level sitting at zero or a suspiciously round value.* It was probably never
  trained and is at a default. Splink warns about this during training; the chart
  is where you notice you ignored the warning.
- *Non-monotonic levels* — a looser similarity level worth more than a stricter
  one. A genuine red flag, usually meaning too few pairs reached that level for a
  stable estimate.
- *One field dominating.* Check it is not a near-duplicate of another field, which
  would double-count the same evidence — the independence problem from chapter
  2.4.

**When it misleads.** The weights are conditioned on the candidate set. A tall bar
means "strong evidence among the pairs blocking let through", **not** "this field
is accurate in our register". Chapter 2.6 shows how far apart those two can be:
EM estimated an exact-match *m* of 0.92 where the measured value was 0.32.

### m and u separately

The same parameters split into their components. More useful than it sounds,
because *m* and *u* answer different questions and fail in different ways.

**Use it to** diagnose *which* of the two estimates is the problem when a weight
looks wrong.

The *u* panel should reflect your data's frequency structure: common values agree
often by chance, rare ones almost never. The *m* panel should reflect your data
quality. A *u* that contradicts what you know about cardinality means the sampling
went wrong; an *m* near 1.0 at every level means EM converged to something
degenerate, or the training rule was so strict that everything inside it agreed.

### The score distribution

The single most informative chart about whether the linkage worked at all.

**Use it to** decide whether the model separates the two populations, and where a
threshold can sensibly sit.

You want two humps with a thin region between them. A threshold in the thin
region decides few pairs, so the decision is cheap. A **single smooth
distribution** is the warning sign: the model cannot separate matches from
non-matches, and no threshold will fix it. The answer then is better comparisons
or better blocking, not a better cut-off.

**When it misleads.** This chart shows only *candidate* pairs. Matches discarded
by blocking appear nowhere in it. A beautifully bimodal histogram is entirely
compatible with having lost 60% of the true matches upstream — as, on this
toolkit's data, it is.

### The waterfall — one decision at a time

The other three charts are about the model. This one is about a single pair: how
each field moved its score from the prior to where it landed.

**Use it for** clerical review, and for answering "why did this pair score that?"
— including when the person asking is an auditor or a data subject.

This is the chart that makes a linkage explainable. "The model gave 0.62" is not
an explanation. "The surnames agreed strongly, the given name only loosely, and
sex disagreed" is.

```{note}
`waterfall_chart()` requires `retain_intermediate_calculation_columns=True` in
the settings, or it fails with a `ValueError` about missing columns.
```

## The two charts for everyone else

### 1. How much did we link, and did it vary?

A bar chart of linkage rate by some characteristic, with the overall rate as a
reference line.

On this toolkit's data the honest version is uncomfortable: at a threshold of
0.90, 12% of true matches are found for given names of six characters or fewer,
against 40% for names of sixteen or more, around an overall rate of 28%. (At the
more permissive threshold used in chapter 2.8 the rates are higher — 19% and 47%
— but the gradient is the same.)

That chart is a claim about **fitness for purpose**, and it is the one an informed
reader wants. If your analysis compares groups whose linkage rates differ that
much, part of the difference between them is an artefact of the linkage, and the
publication has to say so.

Publishing it is uncomfortable and it is the right thing to do. A reader who
discovers differential linkage error for themselves, after using your data, will
trust nothing else in the publication.

### 2. How certain are the links we published?

A distribution of confidence among the *accepted* links — not the raw score
distribution, which includes everything you rejected.

It lets a reader judge how much of the result rests on marginal decisions. On
this data, 1,189 of 1,267 published links sit above 0.99 and only 31 are between
0.90 and 0.95. That is worth a reader knowing, and it costs one chart.

## Design notes

A few things that make these charts work, in any tool.

**One series needs no legend.** The title names what is plotted. Legends are for
distinguishing series, and a legend box with one entry is clutter.

**Label the bars directly.** A reader should not have to trace a bar back to an
axis to read a number that matters.

**Give a reference line for the overall rate.** Levels and spreads are different
claims, and a reference line lets one chart carry both.

**Never use colour alone to carry meaning.** Roughly one man in twelve has a
colour-vision deficiency. Where two series must be distinguished, check the pair
is separable under simulated deuteranopia and protanopia, and give each series a
direct label as well.

**Recessive grid, prominent data.** Grid lines at a very light grey, top and
right spines removed, and the data in full colour.

## Dashboards

Splink exports self-contained HTML dashboards. The **comparison viewer** shows
the score distribution, lets a reviewer filter to any region of it, and displays
the field-by-field comparison for individual pairs.

These are working tools, not publication outputs. Send them to a reviewer, not to
a bulletin. They are the fastest way for someone who did not build the model to
develop a feel for what it does — and the fastest way to spot a systematic error,
because patterns in the borderline region become obvious when you can page
through them.

## The deliverable

Six artefacts. Anything less and someone will have to guess.

| Artefact | Who needs it |
|:--|:--|
| Linked pairs (crosswalk) | The analyst joining the data back |
| Cluster assignment | Anyone counting people rather than pairs |
| Trained model (JSON) | Anyone re-running or auditing the linkage |
| Preparation and pipeline code, version-controlled | Anyone reproducing it |
| Quality statement | Anyone using or citing the output |
| Diagnostic charts | Reviewers and auditors |

Two of those are easy to omit and expensive to omit.

**The trained model is part of the deliverable.** Without it the linkage cannot be
re-run or audited — and because *u* estimation is unseeded, it cannot even be
reproduced from the same code on the same data.

**The crosswalk carries no identifying fields.** It maps a record identifier on
one side to a record identifier on the other, plus a score. Each institution
joins it back to its own records using the internal key from
[chapter 2.3](../02_producing/anonymisation.md). That is the separation principle
carried through to the output, and it is what lets the crosswalk move between
institutions when the source data cannot.
