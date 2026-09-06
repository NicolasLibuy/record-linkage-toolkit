# Evaluating a linkage

Every number in this section so far was scored against a ground truth. Real data
does not come with one, and that is not a detail — it is the central difficulty
of linkage quality assessment.

This chapter covers both halves: what evaluation looks like when labels exist,
and the four practical methods for the ordinary case where nobody knows the right
answer. The accompanying notebook is
[Evaluating a linkage](nb09-evaluation.ipynb).

## Part 1 — When you have labels

Splink evaluates a model directly against a labels column, producing a table of
every threshold with its true positives, false positives, false negatives,
precision, recall and F1, plus ROC and precision–recall curves.

```{warning}
The `truth_threshold` column in that table is in **match weight** units
(log-odds), **not** match probability. Passing it to an argument expecting
`threshold_match_probability` produces a nonsensical threshold and no error
message. Use the `match_probability` column of the same table.
```

**Read the precision–recall curve, not the ROC curve.** With 4,500 matches among
810 million possible pairs, the classes are so unbalanced that an ROC curve looks
flattering: its false-positive rate has an enormous denominator. The
precision–recall curve shows where the cliff is.

### Why "maximise F1" should not be a reflex

Chapter 2.1 recorded that this use case weights the two errors equally, and only
that makes maximising F1 legitimate here. Even so, look at what it selects:

| Threshold | Precision | Recall | Accepted pairs |
|:--|--:|--:|--:|
| 0.90 (chapter 2.7) | 0.9961 | 0.2804 | 1,267 |
| **0.0055 (F1-optimal)** | **0.8256** | **0.3578** | **1,950** |

The F1-optimal threshold is a match probability of **0.0055** — it accepts
anything the model rates above roughly one chance in two hundred, and about one
accepted pair in six is wrong.

F1 went there because on this data recall is scarce and precision abundant, so
the harmonic mean is maximised by trading a lot of precision for a little recall.
That is the correct answer to "what maximises F1" and quite possibly the wrong
answer for your use case. Moving to it buys about 350 extra true matches and
admits about 340 wrong ones.

**The threshold is a policy decision informed by measurement, not an
optimisation.** Pick the row of the accuracy table that matches the error
preference you wrote down, and say which row you picked.

### Read recall against the blocking ceiling

A recall figure alone silently blames the model for matches that blocking
discarded. On the bundled data, at the F1-optimal threshold:

| | |
|:--|--:|
| True matches in the data | 4,500 |
| Reachable after blocking (ceiling 0.4182) | 1,882 |
| Found by the model | 1,610 |
| **Recall against all true matches** | **0.3578** |
| **Recall against what blocking allowed** | **0.8555** |
| Lost to blocking | 2,618 |
| Lost to the model | 272 |

Those last two lines are the ones to act on. **Blocking discards roughly ten
times as many matches as the model misses.** Effort spent on blocking rules will
pay back many times more than effort spent tuning the model — which is not where
attention naturally goes.

Report both recall figures. Giving only the first invites the reader to think the
model is worse than it is; giving only the second hides a real loss.

## Part 2 — Is the error differential?

This question decides whether a linked dataset is safe to publish from, and it
matters more than the headline error rate.

Errors falling **evenly** across the population inflate variance and can often be
corrected for. Errors concentrated in a **particular group** bias every estimate
that compares groups — and administrative data errors are almost never evenly
distributed.

On the bundled data, recall by subgroup at the F1-optimal threshold:

| Group | True matches | Found | Recall |
|:--|--:|--:|--:|
| Nationality: national | 3,351 | 1,212 | 0.3617 |
| Nationality: foreign | 927 | 370 | 0.3991 |
| Nationality: **missing** | 222 | 52 | **0.2342** |
| Sex: female | 2,059 | 780 | 0.3788 |
| Sex: male | 2,307 | 826 | 0.3580 |
| Sex: **missing** | 134 | 28 | **0.2090** |

By nationality and sex the differences are modest. But records where those fields
are **missing** are linked at roughly two thirds the rate of everyone else — and
records with incomplete identifying information belong disproportionately to
people who are least well served by administrative systems in the first place.

The starkest gradient is by the length of the given name:

| Given-name length | True matches | Found | Recall |
|:--|--:|--:|--:|
| 1–6 characters | 684 | 127 | **0.1857** |
| 7–10 | 866 | 211 | 0.2436 |
| 11–15 | 1,907 | 865 | 0.4536 |
| 16+ | 809 | 380 | **0.4697** |

Recall is two and a half times higher for the longest names than the shortest.
Nothing in the model treats short names badly; a short name simply carries less
information, so agreement on it is weaker evidence and the pair scores lower.

The consequence is a linked dataset that systematically under-represents people
with short names — which correlates with naming conventions, and therefore with
language, ethnicity and migration history. **This is how a purely technical
property of a linkage becomes a substantive bias in a published statistic.**

### Four questions before publishing

1. **Who is missing?** Compare the characteristics of records that linked with
   those that did not.
2. **Does the linkage rate vary across the groups your analysis compares?** If it
   does, differences between those groups are partly an artefact of linkage.
3. **Which direction does the bias run?** Missed matches usually understate
   overlap; false matches usually attenuate real differences.
4. **Would your conclusion change under a different threshold?** If it would,
   report the sensitivity rather than a single number.

## Part 3 — When you have no labels

The normal case. Four methods, in rough order of cost.

### 1. Compare linked, unlinked and unlinkable records

The cheapest and most informative check, needing no external data at all. On the
bundled data:

| Status | Records | % foreign | % female | Mean given-name length |
|:--|--:|--:|--:|--:|
| Linked | 1,904 | 21.40 | 47.11 | 12.8 |
| Unlinked | 25,333 | 17.51 | 49.59 | 12.0 |
| Unlinkable | 2,763 | 18.11 | 47.58 | 10.1 |

Linked records have noticeably longer given names than unlinkable ones — the same
signal the labelled analysis found, **recovered here without any labels**. That
is the value of this check: it points at the right problem using data you already
hold.

Separating **unlinkable** from merely **unlinked** matters. An unlinkable record
never had a chance, usually because a key field is missing; conflating the two
confuses a data-collection problem with a linkage problem, and they have
different remedies.

One honest limitation: the unlinked group mixes records whose partner was missed
with records that have **no partner at all**. On real data you cannot separate
them, which is why this check tells you where to look rather than what the error
rate is.

### 2. Compare against external reference data

Where an independent source gives a known total — a census count, a published
register size, an administrative total — compare your linked count against it.
This will not identify *which* links are wrong, but it bounds the aggregate
error, which is often what a published statistic needs.

### 3. Build a gold standard on a subset

Sample pairs, have people adjudicate them, and treat the result as truth for that
sample. This is the only method that yields direct estimates of precision and
recall without a full ground truth.

**Sample deliberately, not at random.** A random sample of 8,868 candidate pairs
returns mostly obvious non-matches and teaches you little. Stratify by score and
over-sample where the model is uncertain — the 0.3–0.9 band from chapter 2.7,
where 149 of 188 pairs were genuine matches and every one was a judgement call.

Splink's `labelling_tool_for_specific_record()` writes a self-contained HTML page
showing one record with every candidate match, its score and a field-by-field
comparison, with buttons to record a judgement.

### 4. Plausibility checks

Internal consistency checks that need nothing external and catch a surprising
amount:

- **Structure.** In a 1:1 link no record should match several others. Chapter 2.7
  found no clusters larger than two — real evidence, obtained without labels.
- **Impossible combinations.** A person linked to records implying two dates of
  birth, or a death preceding a birth.
- **Distribution.** If two registers should overlap in roughly 15% of records and
  the linkage finds 3%, something is wrong regardless of what precision says.
- **Positive and negative controls.** Check the model finds records you know
  should link, and does not link records that cannot possibly match — different
  periods, disjoint populations.

### Choosing among them

| Method | Cost | Gives you | Main limitation |
|:--|:--|:--|:--|
| Linked vs unlinked profiling | Very low | Evidence on differential error | Says nothing about false matches |
| External reference totals | Low | An aggregate bound | Cannot identify individual errors |
| Clerical gold standard | High | Direct precision and recall | Sampling design is easy to get wrong |
| Plausibility checks | Low | Structural errors, quickly | Passing them does not mean correct |

Do the first and fourth always — they are nearly free. Do the third when the
output will be published or used to make decisions about individuals. Do the
second when an independent total exists.

```{warning}
**Never report a match rate on its own.** A match rate is not a quality measure.
It mixes true matches, false matches and genuine overlap into one number, and it
can be raised simply by lowering the threshold.
```

## What to record

- the threshold, in both match-probability and match-weight terms;
- precision and recall, with how they were estimated and on what sample;
- recall against the blocking ceiling as well as against all true matches;
- the linked / unlinked / unlinkable profile;
- whether error is differential, across which characteristics, in which
  direction;
- structural checks and their results;
- **everything you could not measure, stated plainly.**

That last item is not a caveat to bury. A quality statement that admits what is
unknown is more useful, and more credible, than one that reports only what
happened to be measurable.

[Chapter 2.9](end-to-end-example.md) puts the whole pipeline into a single
notebook, and Section 3 turns these results into a statement fit for publication.
