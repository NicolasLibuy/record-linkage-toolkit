# Quality metric reference

Every metric this toolkit uses, in one place: what it means, how it is computed,
what it hides, and where it appears.

## The confusion matrix

Almost everything below is built from four counts. For a set of record pairs
where the truth is known:

| | Truly a match | Truly not a match |
|:--|:--|:--|
| **Linked by the method** | *a* — true positive | *b* — false positive |
| **Not linked** | *c* — false negative | *d* — true negative |

Two of these are errors, and they are not symmetric in consequence:

- ***b*, a false match** — two different people joined into one record. Creates a
  person who does not exist, and merges their characteristics.
- ***c*, a missed match** — one person left as two records. Understates overlap,
  and removes that person from any analysis needing both sources.

Which is worse is a property of the use case, not of the data. See
[chapter 2.1](../02_producing/defining-the-use-case.md).

```{warning}
In linkage, *d* is astronomically large: on this toolkit's small registers, 810
million possible pairs contain 4,500 matches. Any metric with *d* in it — accuracy,
specificity, false positive rate — will look excellent no matter how bad the
linkage is. This is why the metrics below are all built from *a*, *b* and *c*.
```

## Accuracy metrics

### Precision (positive predictive value)

$$\text{precision} = \frac{a}{a + b}$$

**Of the links the method made, what share are correct?** The direct measure of
false-match risk. This is what a user of the linked data experiences: if
precision is 0.95, one row in twenty of their dataset joins two different people.

### Recall (sensitivity, true positive rate)

$$\text{recall} = \frac{a}{a + c}$$

**Of the matches that exist, what share did the method find?** The direct measure
of missed-match risk.

```{important}
Recall depends on what you put in the denominator, and this is where linkage
quality statements most often mislead. Recall against *all* true matches includes
matches that blocking discarded before any model ran. Recall against the matches
that survived blocking measures the model alone.

On this toolkit's data those figures are 0.36 and 0.86 at the same threshold.
Report both, and say which is which.
```

### F1 score

$$F_1 = 2 \cdot \frac{\text{precision} \cdot \text{recall}}{\text{precision} + \text{recall}}$$

The harmonic mean of precision and recall: a single number for comparing
operating points **when the two errors cost the same**.

They rarely do. Chapter 2.8 shows what happens when F1 is maximised without
thinking: on this data it selects a match-probability threshold of 0.0055, at
which one accepted pair in six is wrong. Use F1 to compare models, not to choose
an operating threshold, unless you have written down that the errors are equally
costly.

### Specificity and negative predictive value

$$\text{specificity} = \frac{d}{b + d} \qquad
\text{NPV} = \frac{d}{c + d}$$

Standard in diagnostic testing, **misleading in linkage**, because both are
dominated by *d*. Specificity of 0.9999 sounds excellent and is compatible with
tens of thousands of false matches. Named here so you recognise them; not
recommended.

### Match rate

$$\text{match rate} = \frac{\text{records linked}}{\text{records in the source}}$$

**Not a quality measure.** It mixes true matches, false matches and the genuine
overlap between the sources into one number, and it rises whenever you lower the
threshold. A high match rate is consistent with an excellent linkage and with a
terrible one.

Report it, because users ask for it — but never on its own, and never as evidence
of quality.

## Blocking metrics

These evaluate the candidate-generation step, before any scoring.
See [chapter 2.5](../02_producing/blocking.md).

### Reduction ratio

$$RR = 1 - \frac{\text{candidate pairs}}{\text{all possible pairs}}$$

How much work the blocking removed. Usually very close to 1, which makes it
deceptive to read: 0.9999 and 0.99999 differ by a factor of ten in compute. Read
the absolute candidate count alongside it.

### Pair completeness

$$PC = \frac{\text{true matches among candidates}}{\text{all true matches}}$$

**The most important number in a linkage pipeline that nobody reports.** It is a
hard ceiling on recall: a match whose records never share a block is never
scored, and cannot be recovered by any threshold or any model.

On this toolkit's data, PC is 0.4182 — so 58% of the true matches were lost
before the model ran.

Estimating PC requires knowing some true matches, which is the usual difficulty.
A clerical sample, or a subset with a reliable identifier, gives an estimate.

### Pairs quality

$$PQ = \frac{\text{true matches among candidates}}{\text{candidate pairs}}$$

How concentrated the true matches are among the candidates. Mostly useful for
comparing rules: a rule with high PQ generates a candidate set that is cheap to
score and easy for a model to work with.

## Cluster metrics

After clustering pairs into entities
([chapter 2.7](../02_producing/thresholds-and-clustering.md)):

**Cluster size distribution.** In a 1:1 link, every cluster should hold one or
two records. Clusters of three or more mean either the threshold is too
permissive or several genuinely different people are indistinguishable on the
available fields.

**Clusters exceeding expected cardinality.** A count, and a check you can run
**without any ground truth** — which makes it one of the most valuable diagnostics
on real data.

**Singleton share.** The proportion of clusters holding one record. Expected to be
high where the sources overlap little; a sudden change between runs signals a
problem. Remember that a count of clusters is not a count of matched people.

## Differential error

The metric that decides whether a linked dataset is safe to publish from.

**Differential linkage error** means the error rate varies systematically across
groups. Measure it by computing recall — or, without labels, the linkage rate —
separately by each characteristic your analysis will compare.

Errors falling evenly inflate variance and can often be adjusted for. Errors
concentrated in a group **bias** every comparison involving that group.

On this toolkit's data, at the F1-optimal threshold, recall is 0.19 for the
shortest given names against 0.47 for the longest, and around a third lower for
records with a missing sex or nationality. The gradient persists at every
threshold. Nothing in the model treats those groups differently; short names
simply carry less information. That is how a technical property becomes a
substantive bias.

Report: the characteristic, the linkage rate in each group, and the direction the
resulting bias runs.

## Metrics you can compute without a ground truth

Most offices have no answer key. These need none:

| Check | What it detects |
|:--|:--|
| Cluster sizes against expected cardinality | Over-permissive threshold; indistinguishable people |
| Linked vs unlinked vs unlinkable profiles | Differential error, without labels |
| Match rate against an external total | Aggregate over- or under-linkage |
| Impossible combinations in linked records | False matches |
| Positive controls (records known to link) | Missed matches |
| Negative controls (records that cannot link) | False matches |
| Score distribution shape | Whether the model separates at all |

Separating **unlinkable** records — those missing a field required by the method —
from merely **unlinked** ones matters: they indicate a data-collection problem
rather than a linkage problem, and the remedies differ.

## A minimum set to report

If you report nothing else, report these seven:

1. Records in each source, and links produced.
2. Precision, **with how it was estimated**.
3. Recall against all true matches, **and** against the blocking ceiling.
4. The blocking ceiling itself, or a statement that it is unknown.
5. The threshold, in both probability and match-weight terms.
6. Linkage rate by at least one characteristic relevant to the intended analysis.
7. Cluster sizes against the expected cardinality.

Items 2 and 3 with "how estimated" attached are what separate a quality statement
from a set of numbers. "Precision 0.99" means nothing without knowing whether it
came from a full ground truth, a clerical sample of 200 pairs, or an assumption.
