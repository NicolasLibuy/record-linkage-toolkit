# Linkage approaches: deterministic and probabilistic

Two records describe two people. Are they the same person? Everything in record
linkage reduces to answering that question a very large number of times, and
there are two ways to answer it.

This chapter covers both, in the order you should learn them. Deterministic rules
come first because they are simple, defensible, and a benchmark you will want
later. The Fellegi–Sunter framework comes second because it fixes the specific
things deterministic rules cannot do.

Two notebooks accompany this chapter:
[Deterministic linkage rules](nb03-deterministic-rules.ipynb) and
[Fellegi-Sunter from first principles](nb04-fellegi-sunter-by-hand.ipynb). Every
figure quoted below comes from them.

## Part 1 — Deterministic rules

A deterministic rule states in advance which fields must agree. It is a
specification you can write on one line and hand to a lawyer.

### Four kinds of rule

**Strict.** All identifying fields must be present and agree exactly. The most
conservative rule available.

**N-1.** All but one field must agree. Usually the one dropped is the field that
is most often missing or least reliably recorded.

**Non-disagreement clause.** A field is not required to agree, but is required
not to *conflict*: both present and equal is fine, either missing is fine, both
present and different is a rejection. This extracts evidence from a field without
demanding it be populated.

**Match key.** The rule is built from *fragments* of fields rather than whole
ones — the first three letters of a given name, a year of birth rather than a
full date, a phonetic code. It tolerates error in a specific, predictable place.

These can be combined into a **stepwise** design: apply the strictest rule first,
then a looser rule to whatever remains unmatched, and so on. Each pair carries a
label saying which stage produced it, so the strong and weak parts of the linkage
stay distinguishable afterwards.

### What they achieve, measured

Five rules on the bundled registers, scored against the 4,500 known matches:

| Rule | Pairs | Precision | Recall |
|:--|--:|--:|--:|
| Strict: all five fields agree | 1,067 | 1.0000 | 0.2371 |
| N-1: drop second surname from the rule | 1,197 | 0.9298 | 0.2473 |
| N-1 + non-disagreement clause on second surname | 1,090 | 0.9972 | 0.2416 |
| Match key: given-name initial-3 + both surnames + sex | 1,158 | 0.9801 | 0.2522 |
| Stepwise: strict, then N-1 + clause, then match key | 1,177 | 0.9813 | 0.2567 |

Four things are worth drawing out of that table.

**The trade-off is real and visible.** Relaxing the strict rule to N-1 gains a
percentage point of recall and costs seven points of precision. There is no free
relaxation.

**The non-disagreement clause is the best value in deterministic linkage.**
Adding it to the N-1 rule rejected 107 pairs, of which 26 were genuine — three
wrong pairs removed for every right one lost. Precision recovers almost to the
strict rule's level while recall stays above it. It is worth considering for any
field that is often missing but reliable when present.

**Stepwise gives you graded output.** Per-stage precision was 1.0000, 0.8696 and
0.7816. That is operationally valuable: an analyst can be told that stage 1 pairs
are safe unconditionally while stage 3 carries a known error rate, or can drop
the weakest stage for an analysis that needs high precision — without re-running
anything.

**Rules are not simply nested.** The strict rule is contained in both of the
others, as you would expect. But the N-1 rule and the match key are not nested in
each other: the match key found 91 pairs the N-1 rule missed, and the N-1 rule
found 23 the match key missed. They tolerate different errors. That is precisely
why running several rules and combining them is worth doing.

### Where deterministic rules stop

Every rule above shares one structure: a **binary** decision from an **exact**
comparison. Three consequences follow, and they are the reason the rest of this
chapter exists.

*Partial agreement is invisible.* A rule cannot express "these names are 80%
similar". Either the field agrees or it does not.

*Every agreement weighs the same.* Agreement on `GONZALEZ` — the most common
surname in both registers — counts exactly as much as agreement on a surname held
by three people in the country. The second is obviously far stronger evidence,
and no rule can say so.

*The dial is coarse.* You move along the precision/recall trade-off by adding or
removing whole rules, each of which has to be designed, tested and documented by
hand.

## Part 2 — The Fellegi–Sunter framework

Fellegi and Sunter's 1969 insight was that the evidential value of an agreement
on one field depends on two probabilities, and only on those two.[^1]

### The two questions

**The *m*-probability.** *Given that this pair really is the same person, how
often does this field agree?* This is about **data quality**. Accurately recorded
fields have a high *m*; fields full of typing errors and abbreviations have a
low one.

**The *u*-probability.** *Given that this pair is two different people, how often
does this field agree anyway, by coincidence?* This is about **discriminating
power**. Sex agrees by chance about half the time. A given name agrees by chance
almost never.

The ratio $m_j/u_j$ is what an agreement is worth. Taking a base-2 logarithm
turns the ratio into a **match weight** in bits, so that evidence from several
fields can be added rather than multiplied:

$$w_j = \log_2 \frac{m_j}{u_j} \text{ (agreement)} \qquad
w_j = \log_2 \frac{1-m_j}{1-u_j} \text{ (disagreement)} \qquad
w_j = 0 \text{ (not comparable)}$$

The total score for a pair is the sum of its field weights plus a term for the
prior odds that two randomly chosen records match. That total converts back into
a probability with $P = 2^W/(1+2^W)$, which is the number a linkage library
reports.

### What the model says about our data

Estimated directly on the bundled registers — *m* from the 4,500 known matches,
*u* from a random sample of 200,000 pairs:

| Field | *m* | *u* | Agreement weight | Disagreement weight |
|:--|--:|--:|--:|--:|
| Given name | 0.323 | 0.00015 | **+11.08** | −0.56 |
| First surname | 0.451 | 0.00147 | +8.26 | −0.86 |
| Second surname | 0.438 | 0.00177 | +7.95 | −0.83 |
| Sex | 0.916 | 0.50044 | +0.87 | **−2.58** |
| Nationality | 0.994 | 0.67053 | +0.57 | **−5.88** |

Read the columns separately before combining them.

**The *m* column is data quality, and here it is poor.** Among pairs we know are
the same person, the given name agrees exactly only 32% of the time and each
surname less than half the time. That single fact explains everything that came
before: exact matching in [chapter 2.3](anonymisation.md) reached recall 0.24 not
because it was badly implemented but because two thirds of genuine matches simply
do not agree exactly on a given name.

**The *u* column is discriminating power**, spanning four orders of magnitude.

**The weights invert the intuition about which fields matter.** Agreement on a
given name is worth 11 bits — overwhelming evidence — while agreement on sex is
worth less than one. But look at the disagreement column: *disagreement* on
nationality is worth −5.88 bits and on sex −2.58, far stronger than disagreement
on a name. Genuine matches almost always agree on sex, so disagreement there is
real evidence against the pair; genuine matches disagree on names often enough
that it means comparatively little.

That asymmetry is the formal version of the non-disagreement clause invented by
hand in Part 1 — except that here its strength is measured rather than guessed.

### The honest result

Scoring all 4,500 true matches and the sampled non-matches, the best threshold
gives precision 1.0000 and recall 0.2678.

The best deterministic rule gave precision 0.9813 and recall 0.2567.

**The Fellegi–Sunter model, correctly built, barely beat a hand-written rule.**

That result deserves an explanation rather than an excuse, because it is the most
useful thing in this chapter.

The framework has delivered real things: a continuous dial instead of five
discrete rules, weights derived from the data instead of judgement, a principled
treatment of disagreement, and an interpretable score. What it has *not* done is
move the ceiling — because it is still comparing values for **exact equality**.
To this model, `GONZALES` and `GONZALEZ` are as different as `GONZALES` and
`MARTINEZ`.

The scoring framework is necessary but not sufficient. What unlocks it is
comparing values that are *similar* rather than identical: string-similarity
measures with several levels of partial agreement, and phonetic encoding so that
names that sound alike compare alike. That is what
chapter 2.6 adds, and it is where recall on this
data finally moves.

If you take one thing from this chapter, take that. Probabilistic linkage is not
a better scoring rule bolted onto exact matching. Its power comes from being able
to use evidence that exact matching throws away.

### The assumption underneath

Adding field weights together is valid only if the fields are **conditionally
independent**: given that a pair is a match, agreement on one field should tell
you nothing about whether another agrees.

That assumption is essentially always false. In the bundled data, true matches
agree on *both* surnames 1.70 times more often than independence would predict.
The reason is intuitive: a record entered carefully tends to be right in every
field, and a record entered badly tends to be wrong in several. Errors cluster by
record, not by field.

The consequence is that the model **double-counts** correlated evidence and
becomes overconfident: reported probabilities are more extreme than the truth
warrants. This is a known limitation of the framework, not a defect in your
implementation, and it has three practical implications:

- do not include several fields that are near-duplicates of one another;
- use term-frequency adjustments, so agreement on a common value is not weighted
  like agreement on a rare one (chapter 2.6);
- **evaluate the output empirically** rather than trusting the probabilities
  because the mathematics is correct (chapter 2.8).

## Choosing between them

Not an either/or in practice.

| | Deterministic | Probabilistic |
|:--|:--|:--|
| Use when | Identifiers are good, errors rare, explanation must be simple | Identifiers are imperfect and errors common |
| Effort | Low | Higher |
| Explaining it | "Here is the rule" | Requires explaining a model |
| Tuning | Add or relax whole rules | Move a threshold |
| Rare vs common values | Cannot distinguish | Can, with term-frequency adjustment |

Most offices run both: a deterministic pass on the records with good identifiers,
then a probabilistic model on what remains. Even where the final method is
probabilistic, keep the deterministic rule — it is the benchmark that tells you
whether the model is earning its complexity. If a probabilistic model cannot beat
a well-designed rule, that is worth knowing before you publish.

## Before any of this runs at scale

Both notebooks in this chapter quietly compared every pair, or sampled from them.
At 30,000 × 27,000 that is 810 million comparisons — feasible on a laptop. At
national scale it is not feasible at all.

[Chapter 2.5](blocking.md) is about making the comparison finite without
discarding the matches you were looking for.

[^1]: Fellegi, I. P. and Sunter, A. B. (1969). *A Theory for Record Linkage.*
Journal of the American Statistical Association, 64(328), 1183–1210.
