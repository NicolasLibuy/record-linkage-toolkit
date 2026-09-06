# The whole pipeline, end to end

Chapters 2.2 to 2.8 took one step at a time, stopping to explain each decision.
This chapter runs all of them in one pass, so you can see the shape of a complete
linkage and copy it as a starting point.

The notebook is [The whole pipeline, end to end](nb10-end-to-end.ipynb). It is
deliberately short. A working linkage pipeline is not a large amount of code;
what takes the time is the judgement about what goes in it, which is what the
preceding chapters were for.

## The seven steps

| Step | What happens | Chapter |
|:--|:--|:--|
| 1. Load | Read both sources as text, never letting a CSV reader infer types | 2.2 |
| 2. Prepare | Clean, standardise and phonetically encode — identically on both sides | 2.2 |
| 3. Define | Blocking rules, comparison functions, settings, `Linker` | 2.5, 2.6 |
| 4. Train | λ, then *u* by sampling, then *m* by EM | 2.6 |
| 5. Predict | Score candidate pairs, apply a threshold, cluster into entities | 2.7 |
| 6. Evaluate | Precision, recall, structural checks | 2.8 |
| 7. Export | The crosswalk, the clusters, the model, the summary | 2.7 |

That sequence does not change between projects. Everything else does.

## The run, summarised

| Item | Value |
|:--|:--|
| Sources | Synthetic health-insurance register (30,000) × social-security register (27,000) |
| Link type | `link_only`, 1:1 expected |
| Identifying variables | Given name, first surname, second surname, sex, nationality |
| Cleaning | Uppercase, strip accents and punctuation, collapse whitespace |
| Phonetic encoding | Double Metaphone on all three name fields |
| Blocking | 4 rules, unioned → **8,868 candidate pairs** (1.1 × 10⁻⁵ of all possible pairs) |
| Comparisons | Fuzzy name comparison with a phonetic level; term-frequency adjusted |
| Estimation | λ from deterministic rules (recall = 0.5); *u* by random sampling; *m* by EM over 3 training rules |
| Threshold | 0.9 match probability |
| Result | **1,267 linked pairs**, 55,733 clusters, **0** clusters larger than two |
| Precision | 0.9961 |
| Recall | 0.2804 against all true matches; 0.67 against the blocking ceiling |
| Blocking ceiling | 0.4182 |

Note that this uses **four** blocking rules where chapter 2.6 used six. The
cumulative analysis there showed two of them contributed no additional pairs, so
they were dropped: identical candidate set, less work. That is what counting
before committing is for.

## A live demonstration of the reproducibility problem

This notebook re-trains rather than loading the saved model, which makes it a
useful test of something the earlier chapters only asserted.

Running the same pipeline again produces figures that differ from chapter 2.7's
in the third or fourth decimal place — and on some runs do not differ at all. The
cause is single and specific: `estimate_u_using_random_sampling()` draws a fresh
random sample each time, and nothing seeds it.

How much it matters depends on the sample size. Estimating the same *u* parameter
three times at each of two sample sizes:

| Sample size | Estimates of one *u* parameter | Spread |
|:--|:--|--:|
| 10,000 pairs | 0.000418, 0.000506, 0.000169 | 0.000337 |
| 1,000,000 pairs | 0.000164, 0.000126, 0.000117 | 0.000047 |

At the small size the estimate varies by a factor of three between runs. A
hundred times more pairs tightens it by roughly an order of magnitude — but it
still varies, and it never settles.

Why, then, do precision and recall barely move? Because the model's **ranking**
of pairs is robust to small parameter changes: a pair scoring far above the
threshold keeps scoring far above it. The instability surfaces where the score
distribution is thin — which is exactly around a threshold chosen by optimising
something, as chapter 2.8's F1-optimal threshold was.

The lesson is not "use a big sample and stop worrying". It is that **an unseeded
estimation step makes results irreproducible by default**, and the reliable fix
is to train once, save the model, and version it alongside the code. Section 3
treats this as a requirement rather than a nicety.

## Adapting this to your own data

Five things to change, in order of how much thought each needs.

1. **The columns.** Replace the five identifying fields with yours. **If you have
   a date of birth, add it** — it is usually the single most valuable comparison
   field available, and its absence is what makes this a deliberately hard
   example. Expect substantially better results than the ones here.
2. **The cleaning.** These functions suit Latin-script names with two surnames.
   Your setting may need transliteration, different punctuation handling, or no
   accent stripping at all.
3. **The blocking rules.** Do not copy these. Design them against your own data
   and measure pair completeness, as [chapter 2.5](blocking.md) sets out. This is
   where most of the achievable recall is decided, and on this data it was the
   binding constraint by a factor of ten.
4. **The `recall=0.5` assumption** in the λ estimate. Justify it from a clerical
   sample, or test how much the result moves if it is wrong.
5. **The threshold.** Choose it from your error preference and your measured error
   rates, not from this notebook.

What should not need to change is the shape: prepare, block, compare, train,
predict, threshold, cluster, evaluate, document.

## What Section 2 established

Worth stating plainly, because the numbers in this section are modest and the
reasons are instructive.

**Exact methods cap out early.** Hashed identifiers reached recall 0.24 and the
best deterministic rule 0.26, because genuine matches agree exactly on a given
name only a third of the time.

**The scoring framework alone does not fix that.** Fellegi–Sunter with exact
comparison reached 0.27 — barely better than a hand-written rule.

**Fuzzy comparison helps, but modestly.** The trained Splink model reached 0.27
at identical precision, or 0.30 accepting a small precision cost.

**Blocking was the binding constraint all along.** With these rules only 41.8% of
true matches ever became candidate pairs. At the F1-optimal threshold the model
found 86% of what was reachable, and blocking discarded ten times more matches
than the model missed (2,618 against 272). At the stricter 0.9 threshold used
above the model is more conservative — it finds 67% of what was reachable — but
blocking is still the larger loss by a factor of four.

**And the error is differential.** Recall is two and a half times higher for long
given names than short ones, and a third lower for records with missing fields.
The headline rate conceals it.

Those five findings are specific to this dataset — one with no date of birth and
heavily corrupted names. What generalises is the method that produced them:
measure at every stage, attribute losses to the stage that caused them, and check
whether the losses fall evenly.

Section 3 turns those measurements into something publishable.
