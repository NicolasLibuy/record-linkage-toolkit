# Implementing the model in Splink

Chapter 2.4 built a Fellegi–Sunter model by hand and reached an uncomfortable
conclusion: it barely beat a hand-written deterministic rule. The reason was not
the framework but the comparison — every method so far asked only whether two
values were *identical*.

This chapter hands the work to [Splink](https://moj-analytical-services.github.io/splink/)
and fixes that. Two notebooks accompany it:
[Building a Splink model](nb06-splink-settings-and-blocking.ipynb) and
[Estimating the model parameters](nb07-comparisons-and-training.ipynb).

## Why a library

Three things make hand-rolled code impractical past this point.

**Scale.** Splink compiles its work to SQL and runs it in an analytical database
engine — DuckDB on a laptop, Spark or Athena on a cluster. The same model
definition runs at both sizes.

**Estimating *m* without labels.** Chapter 2.4 computed *m* from 4,500 known
matches. You will not have those. Splink estimates *m* by
expectation-maximisation, which is not something worth implementing yourself.

**Comparisons with levels.** This is the one that matters. A hand-rolled
comparison is binary. A Splink comparison has several ordered levels, and it
estimates a separate weight for each.

## The three ingredients

A Splink model is a `SettingsCreator` holding three things, plus a `Linker` that
binds them to data and a database backend.

```python
settings = SettingsCreator(
    link_type="link_only",
    blocking_rules_to_generate_predictions=blocking_rules,
    comparisons=comparisons,
    unique_id_column_name="unique_id",
    retain_intermediate_calculation_columns=True,
)

linker = Linker(
    input_table_or_tables=[left, right],
    settings=settings,
    db_api=DuckDBAPI(),
    input_table_aliases=["fonasa", "suseso"],
)
```

`link_type` is the decision from [chapter 2.1](defining-the-use-case.md):
`dedupe_only`, `link_only`, or `link_and_dedupe`.

`retain_intermediate_calculation_columns=True` keeps the per-level detail that
diagnostics need. Leave it out and `waterfall_chart()` later fails with a
`ValueError` about missing columns — a common and confusing first encounter with
the library.

## Blocking rules

`block_on()` builds a rule from column names or SQL expressions, and a list of
rules is combined as a union — exactly the disjunctive design of
[chapter 2.5](blocking.md).

```python
blocking_rules = [
    block_on("nombre_clean", "ap1_clean"),
    block_on("ap1_clean", "ap2_clean"),
    block_on("ap1_dm", "ap2_dm"),
    block_on("nombre_dm", "ap1_dm"),
]
```

Each becomes a readable SQL join condition, which is one reason blocking
decisions are easy to document.

**Count before committing.** `count_comparisons_from_blocking_rule()` tells you
how many pairs a rule generates before you run anything, and
`cumulative_comparisons_to_be_scored_from_blocking_rules_data()` shows how the
union accumulates rule by rule.

That cumulative table produced a result worth acting on. Of the six rules carried
over from chapter 2.5, **two contributed zero additional pairs**: by the time the
rules on full names and phonetic codes had run, the two built on the given-name
initial had nothing left to find. They were pure cost. The end-to-end pipeline in
[chapter 2.9](end-to-end-example.md) uses four rules and reaches the identical
candidate set of 8,868 pairs.

A rule that seems sensible in the abstract can be redundant given the others, and
counting is the only way to see it.

## Comparisons

A comparison tells Splink how to compare one field. The library provides
ready-made ones, and the shift from chapter 2.4 is visible immediately in what
`NameComparison` generates:

| Level | Condition |
|:--|:--|
| null | either value missing |
| exact | values identical |
| Jaro-Winkler ≥ 0.92 | very similar |
| Jaro-Winkler ≥ 0.88 | similar |
| Jaro-Winkler ≥ 0.70 | loosely similar |
| else | everything else |

Six ordered levels instead of two, each with its own estimated *m*, *u* and
weight. `GONZALES` and `GONZALEZ` no longer sit in the same bucket as `GONZALES`
and `MARTINEZ`.

### Phonetic encoding

Passing a Double Metaphone column adds a level for names that *sound* alike
without being spelt alike — the idea used for blocking in chapter 2.5, now used
for scoring. It inserts below the loosest string-similarity level, catching pairs
that Jaro-Winkler cannot reach.

```{note}
Double Metaphone returns **two** codes per name, a primary and an alternate.
Blocking needs one comparable value, so store the primary code as a string.
Splink's `dmeta_col_name` argument, by contrast, expects **both** codes as a
list. Passing the string form produces an opaque database error
(`No function matches ... list_distinct(VARCHAR)`) rather than a helpful message,
so keep two columns.
```

### Term-frequency adjustments

Chapter 2.4 identified a defect it could not fix: agreement on `GONZALEZ` — the
most common surname in both registers — counted exactly as much as agreement on a
rare surname.

`.configure(term_frequency_adjustments=True)` fixes it. Splink counts how often
each value occurs and scales the weight for that specific value, so agreeing on a
rare name is worth more than agreeing on a common one. Use it on any
high-cardinality field with an uneven value distribution.

## Estimating the parameters

Three parameters, three different methods, because they are three different kinds
of quantity.

| Parameter | Estimated by | Function |
|:--|:--|:--|
| **λ** | A deterministic rule plus an assumed recall | `estimate_probability_two_random_records_match()` |
| ***u*** | Sampling random pairs, nearly all non-matches | `estimate_u_using_random_sampling()` |
| ***m*** | Expectation-maximisation | `estimate_parameters_using_expectation_maximisation()` |

**λ** rests on an assumption you supply: what share of true matches your strict
rule finds. On this data we passed `recall=0.5`, which chapter 2.4 shows is
optimistic — the strict rules found about a quarter. Justify this number from a
clerical sample where you can, and test how much the result moves if it is wrong.
λ shifts every score by a constant, so it moves the threshold you need, not the
ranking of pairs.

***u*** must be estimated from *randomly drawn* pairs, not from the candidate
pairs. Blocking makes the candidate set unrepresentative by design — a fact
visible in the notebook, where the first surname has no pairs at all in its
lowest comparison levels, because almost every blocking rule requires it to
agree.

***m*** needs EM, because it asks about matches and we have no labelled matches.
EM runs on a subset of pairs blocked on some field; the field used for that
blocking cannot have its own *m* estimated in that session, so you run several
sessions with different training rules and let them cover each other.

```{warning}
`estimate_u_using_random_sampling()` is **not seeded**. Two runs give slightly
different parameters and therefore slightly different scores. This is a genuine
reproducibility problem, treated properly in
[chapter 2.9](end-to-end-example.md) and Section 3.
```

## What the estimates look like, and a warning about reading them

Because chapter 2.4 measured *m* and *u* directly from the known matches, we can
check what EM produced without them.

| Field | *m* by EM | *m* measured | *u* by sampling | *u* measured |
|:--|--:|--:|--:|--:|
| Given name | 0.9162 | 0.3232 | 0.000161 | 0.000149 |
| First surname | 0.9576 | 0.4509 | 0.001468 | 0.001470 |
| Second surname | 0.9407 | 0.4377 | 0.001614 | 0.001770 |
| Sex | 0.9989 | 0.9162 | 0.500873 | 0.500436 |
| Nationality | 1.0000 | 0.9944 | 0.675077 | 0.670525 |

**The *u* estimates are excellent** — every one within a few percent of the
measured value. Random sampling answers "how often do two different people agree
on this?" directly, and little can go wrong.

**The *m* estimates are far from the measured values.** EM puts the exact-match
*m* for the given name at 0.92 where the truth is 0.32 — optimistic by a factor
of three.

This is not a bug, and it will happen to you. EM only ever sees **candidate
pairs**, and blocking selected those precisely because their names agree well.
Among *those* pairs, a true match almost always does agree exactly, so 0.92 is a
correct answer to the question EM was actually asked. It is the wrong answer to
"how often do true matches in the population agree", because the true matches
that disagree on names were filtered out before EM saw them. The same effect
appears in λ: the estimate implies about 2,400 matching pairs where there are
4,500.

Two consequences:

- **Do not quote Splink's estimated parameters as population quantities.** They
  are conditioned on the candidate set. "Our register's given-name accuracy is
  92%" would be wrong.
- **Judge the model by how it ranks pairs, not by whether its parameters look
  plausible.** Ranking depends on relative weights, and a model with biased
  parameters can rank perfectly well. Whether it does is an empirical question —
  [chapter 2.8](evaluation.md).

## Did it work?

The point of this chapter was to move recall. Against everything that came
before:

| Method | Precision | Recall |
|:--|--:|--:|
| Exact match on hashed identifiers (2.3) | 1.0000 | 0.2400 |
| Best deterministic rule (2.4) | 0.9813 | 0.2567 |
| Fellegi–Sunter, exact comparison, by hand (2.4) | 1.0000 | 0.2678 |
| **Splink model, threshold 0.95** | **1.0000** | **0.2740** |
| **Splink model, threshold 0.50** | **0.9842** | **0.3049** |

Be precise about what that shows. At *identical* precision of 1.0000, fuzzy
comparison buys about 0.7 percentage points of recall over the hand-built exact
model. Accepting a 1.6-point precision cost buys about 3.7 points. Both are real
gains on pairs no exact method could see. Neither is a transformation.

The honest summary of chapters 2.3 to 2.6 is that **method improvements moved
recall from 0.24 to 0.30 on this data, and the remaining constraint is not the
model.**

Chapter 2.5 measured that constraint: with these blocking rules only 41.8% of
true matches ever become candidate pairs. The model is capturing roughly three
quarters of what blocking left available. Squeezing the last quarter out of the
model is hard work for a few points; **raising the blocking ceiling is where the
recall actually is.**

That is a common and counter-intuitive finding. Attention goes to the model
because the model is the interesting part. The data usually says the bottleneck
is upstream.

## Saving the model

A trained model is a JSON file of settings and estimated parameters — small,
readable, and reviewable.

```python
linker.misc.save_model_to_json("models/linkage-model.json", overwrite=True)
```

Load it later by passing the path where settings would go:

```python
linker = Linker(tables, settings="models/linkage-model.json", db_api=DuckDBAPI())
```

Estimation is expensive and scoring is cheap, so train once and load thereafter.
Given that *u* estimation is unseeded, saving the model is also the only way to
make a linkage exactly reproducible. This toolkit ships its trained model for
that reason, and the following chapters load it.

[Chapter 2.7](thresholds-and-clustering.md) takes that file and turns scores into
a linked dataset.
