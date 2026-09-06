# Communicating and documenting a linkage

A linked dataset without a quality statement is not a statistical product. It is
a file that somebody will use anyway, drawing conclusions whose reliability
nobody can assess — including them.

This chapter is about the document that goes with the data: what it contains, who
reads it, and how to make the linkage reproducible enough that the document is
true.

## Why document at all

Four reasons, and only the first is the obvious one.

**Users need to know whether the data can answer their question.** A linkage good
enough for a national coverage estimate may be nowhere near good enough for an
analysis of a small subgroup. Only the quality statement lets a user tell.

**You will need to re-run it.** In a year, on new data, probably not by the same
person. Undocumented decisions become archaeology.

**Someone will challenge a result.** A published figure derived from linked data
attracts the question "how do you know these are the same people?", and the
answer has to exist in writing before it is asked.

**It is a condition of most legal bases.** Statistical legislation and
data-sharing agreements generally require that processing be documented and
auditable. This is that documentation.

## Two audiences, two documents

The commonest failure is writing one document that serves neither audience.

**The general reader** — a journalist, a policy analyst, a member of the public
reading the bulletin — needs four things, in plain language:

- what was linked to what, and why;
- roughly what share of records were linked;
- what kinds of people the linkage is more and less likely to find;
- what the result should and should not be used for.

No match weights, no *m* and *u*, no ROC curves. A paragraph and one chart.

**The technical reader** — a methodologist in another office, an auditor,
your own successor — needs everything: the method, the parameters, the code,
the measured error rates, and the things you could not measure.

Write both. The general statement goes in the publication; the technical one is
referenced from it and published alongside.

## A documentation template

Four sections, in this order. It generalises the structure used by the country
projects in this programme, and it maps onto what most quality frameworks ask
for.

### Section A — What was done

- The statistical objective, and what the linked data is for.
- The sources: controller, coverage, reference period, extract date.
- The legal basis and any approvals.
- The unit, the link type, and the expected cardinality.
- The identifying variables used, and any that were available but excluded, with
  the reason.
- Preparation: cleaning and standardisation, as code or as an exact description.
- Whether identifiers were pseudonymised, and how.
- Blocking rules, and the candidate pairs they generated.
- The comparison design and how parameters were estimated.
- The threshold, in both match-probability and match-weight terms, and **why that
  threshold**.

### Section B — How good it is

This is the section that gets read. It should answer, in order:

- **How many links?** Absolute count, and as a share of each source.
- **How accurate?** Precision and recall, *with how they were estimated* — full
  labels, a clerical sample, or not at all. If not at all, say so here rather
  than leaving it to be inferred.
- **How much was unreachable?** The blocking ceiling. Recall stated against it as
  well as against all matches.
- **Is the error differential?** Which groups are linked at lower rates, by how
  much, and in which direction the resulting bias runs.
- **Structural checks.** Cluster sizes against the expected cardinality;
  plausibility checks and their results.
- **Sensitivity.** How the headline number moves across a plausible threshold
  range.
- **What could not be measured.** Explicitly.

### Section C — From links to statistics

The step between a linked file and a published number, which is often
undocumented:

- how linkage error is handled in the analysis: ignored, adjusted for, or
  presented as a sensitivity range;
- whether records that failed to link are excluded, imputed, or weighted;
- what disclosure control was applied to the output;
- known limitations of the resulting statistics.

There are broadly three ways to handle linkage uncertainty downstream: **ignore
it** (defensible only if you have shown the error is small and non-differential),
**restrict to high-confidence links** (raises precision, worsens differential
bias, because the links you drop are not a random subset), or **carry the
uncertainty through** by weighting or by repeating the analysis at several
thresholds. Say which one you did.

### Section D — Sustainability

- Who owns the pipeline, and who runs it next time.
- How often it re-runs, and what triggers a re-estimation of the model.
- Where the code, the model file and the outputs live.
- What would have to change for the linkage to be improved, in priority order.

Section 4 is about making these answers real rather than aspirational.

## A worked example

Section B for the linkage built in this toolkit, written as it would be
published:

> **Linkage quality assessment**
>
> The health-insurance register (30,000 records) was linked to the
> social-security register (27,000 records) using a probabilistic
> (Fellegi–Sunter) model implemented in Splink 4, at a match-probability
> threshold of 0.90.
>
> **Result.** 1,267 linked pairs, forming 1,267 two-record clusters and 54,466
> single-record clusters. No cluster contained more than two records, consistent
> with the expected one-to-one structure.
>
> **Accuracy.** Because the data is synthetic, true match status is known.
> Precision is 0.9961 and recall 0.2804 against all true matches. These are
> exact, not estimated; on real data they would come from a clerical sample and
> carry sampling error.
>
> **Reachability.** The four blocking rules admit only 41.8% of true matches as
> candidate pairs. Recall against what blocking allowed is therefore 0.67, and
> the shortfall attributable to blocking (2,618 matches) is roughly ten times
> that attributable to the model (272 matches at the F1-optimal threshold).
> Blocking, not the model, is the binding constraint on this linkage.
>
> **Differential error.** Linkage rates vary substantially with the length of the
> given name: 12% of true matches are found for names of six characters or fewer,
> against 40% for names of sixteen or more. Records with a missing sex or
> nationality field are linked at roughly two thirds the rate of complete
> records. Any analysis comparing groups that differ in naming conventions or in
> data completeness should treat part of the observed difference as an artefact
> of linkage.
>
> **Sensitivity.** Between thresholds of 0.10 and 0.99, linked pairs range from
> 1,574 to 1,189 and precision from 0.9371 to 1.0000. The reported figures use
> 0.90.
>
> **Not measured.** No external reference total was available. No clerical review
> was undertaken. The rate at which the linkage joins records belonging to
> different people who share all identifying values cannot be distinguished from
> zero at this sample size.

Note the last paragraph. A quality statement that says what it could not measure
is more credible than one that reports only the convenient numbers, and it is
the paragraph a careful reader looks for first.

## The reproducibility checklist

Everything above is only true if someone else can get the same answer. Eight
items; work through them honestly rather than aspirationally.

| # | Item | |
|:--|:--|:--|
| 1 | The code is version-controlled, and the exact version used is identified | |
| 2 | The input extract is identified: source, query or file, extract date | |
| 3 | Every random seed is fixed | ⚠️ |
| 4 | The trained model is saved and versioned with the code | |
| 5 | The environment is pinned: package versions recorded | ⚠️ |
| 6 | The threshold is recorded, with the reasoning | |
| 7 | Outputs are dated and versioned; nothing is overwritten in place | |
| 8 | The whole pipeline runs end to end from a clean checkout | |

Two of those carry a warning mark, because this toolkit failed them and the
failures are instructive.

### Item 3: a seed we cannot set

`estimate_u_using_random_sampling()` draws a random sample and **is not fixed by
any seed argument**. Two runs of identical code on identical data produce
slightly different parameters, slightly different scores, and — where a threshold
is chosen by optimising something — a different threshold.

Chapter 2.9 measures it: estimating one *u* parameter three times at a sample of
10,000 pairs gave 0.000418, 0.000506 and 0.000169. At a million pairs the spread
narrows by roughly an order of magnitude but never closes.

This is a genuine, unfixable-by-us failure of item 3, and pretending otherwise
would be exactly the kind of documentation this chapter is arguing against. The
mitigation is item 4: **train once, save the model, and version it**. Every
chapter of this toolkit that quotes precise figures loads the saved model rather
than re-training, and the model file ships with the repository for that reason.

### Item 5: a pin we did not know we needed

While building this toolkit, `splink==4.0.0` — the version taught in the training
programme — was found to fail EM training entirely with a recent release of
`sqlglot`, one of its own dependencies, with the message
`ValueError: Expected sql condition to refer to one column but got []`. Splink
4.0.0 declares only `sqlglot>=13.0.0`, so a clean install a year later picks up
an incompatible version.

Testing across versions established the boundary precisely: splink 4.0.0 works
with sqlglot 25.9.0 and fails with 30.18.0; splink 4.0.17 works with both.

That is what item 5 protects against, and it is why "it worked when we ran it" is
not a reproducibility statement. Pin your dependencies, record the versions in
the documentation, and test a clean install before you claim the pipeline runs.

## What to publish, and where

| Artefact | Published with | Audience |
|:--|:--|:--|
| Plain-language quality summary | The statistical output itself | Everyone |
| Full quality statement (Sections A–D) | Alongside, referenced from the output | Technical users, auditors |
| Diagnostic charts | The quality statement | Reviewers |
| Code and pinned environment | A repository | Anyone reproducing |
| Trained model file | The repository | Anyone re-running or auditing |
| Linked crosswalk | Restricted, under the relevant agreement | The analysts entitled to it |

[Chapter 3.2](communicating-results.md) covers the charts and which of them belong
in front of which audience.
[Chapter 3.3](quality-metric-reference.md) defines every metric named above.
