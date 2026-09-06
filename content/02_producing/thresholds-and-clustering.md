# Thresholds and clustering

The model scores pairs. It does not decide anything. This chapter is about the
three steps that turn scores into a dataset someone can use: choosing a
threshold, grouping pairs into entities, and exporting the result.

The accompanying notebook is
[Thresholds and clustering](nb08-thresholds-and-clustering.ipynb). It loads the
model saved in [chapter 2.6](implementing-in-splink.md) rather than re-training —
which is both faster and, given that *u* estimation is unseeded, the only way to
get reproducible numbers.

## Read the distribution before choosing anything

Score bands on the bundled data, with the share of pairs in each band that are
genuine matches:

| Match probability | Pairs | True matches | Share correct |
|:--|--:|--:|--:|
| < 0.001 | 6,612 | 214 | 0.032 |
| 0.001 – 0.01 | 393 | 85 | 0.216 |
| 0.01 – 0.1 | 289 | 108 | 0.374 |
| 0.1 – 0.5 | 180 | 103 | 0.572 |
| 0.5 – 0.9 | 127 | 110 | 0.866 |
| 0.9 – 0.99 | 78 | 73 | 0.936 |
| > 0.99 | 1,189 | 1,189 | 1.000 |

The distribution is strongly **bimodal**: 6,612 pairs the model is confident are
not matches, 1,189 it is certain are, and about a thousand in between. That is
what a working model looks like.

A distribution that is *not* bimodal — a smooth spread across the range — is
telling you the model cannot separate the two populations, and no threshold will
rescue it. Look at this before you look at anything else.

One line in that table deserves attention: the lowest band contains **214 genuine
matches**. Those are pairs the model scored below one chance in a thousand and
that are, nonetheless, the same person. No threshold recovers them, because the
identifying fields genuinely disagree. They are part of the irreducible error,
and they are why a linkage quality statement should describe what it cannot find
as well as what it can.

## Threshold sensitivity

Sweeping the threshold shows what each setting costs:

| Threshold | Pairs accepted | True | False | Precision | Recall |
|:--|--:|--:|--:|--:|--:|
| 0.10 | 1,574 | 1,475 | 99 | 0.9371 | 0.3278 |
| 0.30 | 1,455 | 1,411 | 44 | 0.9698 | 0.3136 |
| 0.50 | 1,394 | 1,372 | 22 | 0.9842 | 0.3049 |
| 0.90 | 1,267 | 1,262 | 5 | 0.9961 | 0.2804 |
| 0.95 | 1,236 | 1,236 | 0 | 1.0000 | 0.2747 |
| 0.99 | 1,189 | 1,189 | 0 | 1.0000 | 0.2642 |

Across the whole range the choice buys 213 extra true matches at the cost of 99
false ones — the entire decision, in two numbers.

**Which row you want is not a statistical question.** It is the decision recorded
in [chapter 2.1](defining-the-use-case.md):

- A false match that harms a specific person — a benefit wrongly granted or
  denied — argues for a high threshold and accepting the missed matches.
- A coverage estimate that will be corrected for error statistically may be
  better served by a lower threshold with a *measured* false-match rate.
- Only where the two errors genuinely cost the same is maximising F1 defensible.
  [Chapter 2.8](evaluation.md) shows how far that can take you, and why it is not
  a safe default.

Whatever you choose, **report the threshold with its measured error rates**. A
match count without a threshold is not a result.

## Look at the pairs the threshold is deciding

The threshold's whole effect is on the pairs near it, so look at them. From the
0.3–0.9 band on the bundled data:

| Left | Right | Probability | True match? |
|:--|:--|--:|:--|
| `LUIS LEONCIO` / `PULGAR` / `ORTEGA` | `LUIS LEUNCIU` / `PULGAR` / `ROTEG` | 0.304 | yes |
| `VIVIANADEL CARMEN` / `RAMIREZ` | `VIVIANA DEL CARMEN` / `RAMIREZ` | 0.317 | yes |
| `ALBERTINA DE LAS NIE` / `CISTERNAS` | *(missing)* / `CISTERNES` | 0.331 | yes |
| `MAXIMILIANO ALEJANDR` / `VALENZUELA` | *(missing)* / `VALENZUELA` | 0.332 | **no** |

Two things stand out.

These are not exotic cases. A missing space in `VIVIANADEL CARMEN`, a truncated
name, a handful of wrong vowels — ordinary administrative data damage. Notice
that cleaning could not fix the missing space: our standardisation collapses
extra whitespace but cannot insert any.

And of the 188 pairs in that band, **149 are genuine matches**. The model is not
confused about a random selection of pairs; it is unsure about pairs a careful
human would mostly accept. That is a strong argument for routing this band to
clerical review rather than letting a threshold decide it silently.

Splink's **waterfall chart** decomposes an individual pair's score into each
field's contribution. It is the right tool for review, because it turns "the
model said 0.62" into "the surnames agreed strongly, the given name only loosely,
and sex disagreed" — something a human can actually judge.

## From pairs to entities

A threshold gives you accepted **pairs**. Most uses need **people**.

That is not merely a matter of collecting pairs, because pairwise decisions can
be inconsistent: if A links to B and B links to C but A does not link to C, are
those one person or two? Clustering resolves it by building a graph of accepted
pairs and taking connected components, so A, B and C become one cluster.

```python
clusters = linker.clustering.cluster_pairwise_predictions_at_threshold(
    df_predict, threshold_match_probability=0.9
)
```

On the bundled data at threshold 0.9: 57,000 records fall into 55,733 clusters —
54,466 of size one and 1,267 of size two.

Two things to read there.

**Most clusters hold a single record.** A person present in only one register, or
one whose match was missed. That is expected here, where the registers overlap in
4,500 people out of 57,000 records, but it does mean **a count of clusters is not
a count of matched people.**

**Clusters larger than two should not exist in a 1:1 link.** There were none,
which is a genuine quality signal — and one obtainable *without any ground
truth*, which makes it valuable on real data. Where oversized clusters do appear,
raise the threshold, add a field that separates the people involved, or route
those clusters to clerical review.

## The deliverable

The linked output is a crosswalk: one row per accepted link, carrying the record
identifier from each side and the score that produced it.

Note what it does **not** contain — no names, no other identifying fields. Each
institution joins it back to its own records using the internal key from
[chapter 2.3](anonymisation.md). That is the separation principle carried through
to the deliverable.

**Keep the `match_probability` column.** It lets a downstream analyst apply a
stricter threshold without re-running the linkage, and it is what an
uncertainty-aware analysis needs.

Alongside the crosswalk, ship the cluster assignment, the model JSON, and the
quality statement that Section 3 develops.

## What to record

- the threshold, in both probability and match-weight terms, and why it was
  chosen;
- the score distribution, at least as bands;
- pairs accepted, and error rates at that threshold with how they were estimated;
- the cluster size distribution, and the count of clusters larger than expected;
- the sensitivity of your headline number to the threshold — if the conclusion
  changes across a plausible range, that belongs in the publication.

The threshold above was chosen by hand and the error rates measured against a
ground truth that real data does not have.
[Chapter 2.8](evaluation.md) addresses both.
