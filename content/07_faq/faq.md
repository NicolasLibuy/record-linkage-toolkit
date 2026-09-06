# Frequently asked questions

A running list, maintained rather than published once. Questions that recur are
added; answers are updated when the software or the guidance changes.

If your question is not here, [open an issue](https://github.com/NicolasLibuy/record-linkage-toolkit/issues)
— that is how this page grows.

---

## Getting started

### Do I need to be a programmer?

No, but someone on the team does. The Python this toolkit uses is reading files,
cleaning strings, grouping and counting. If nobody in the office can do that,
[Building the capability](../06_reference/open-source-courses.md) is a two-week
route, and RELAIS in [Software](../06_reference/software.md) is a route that needs
no programming at all.

### Can I work through this without any data?

Yes — that is why the toolkit ships a
[synthetic dataset](../../data/README.md) with a known ground truth. You can run
every notebook, including the evaluation, before touching national data.

### How long does a first linkage project take?

The technical work is weeks. The access and authorisation work is months, and it
is the critical path. See [chapter 4.1](../04_production/adoption-challenges.md).

### Our sources have a national identity number. Do we need any of this?

Probably not most of it. If the identifier is present, complete and accurate, a
deterministic join will beat anything in this toolkit and be far easier to
explain.

Check three things first: how completely it is populated, whether the gaps are
systematic (they usually are, and usually affect the people you most want to
measure), and whether the sources use *provisional* identifiers alongside the
real one. Chapter 1.2 covers the mixed case, which is more common than either
extreme.

---

## Legal, privacy and governance

### Do we need consent from the people in the registers?

For large administrative sources, individual consent is normally infeasible and
linkage proceeds under a statistical or legal mandate instead. Where consent *is*
feasible — linking a survey or cohort to administrative records — it introduces a
selection problem: people who consent differ systematically from those who do
not. See [chapter 1.3](../01_why/linkage-as-a-solution.md).

### Is hashing the names enough to make the data anonymous?

No. Hashed identifiers are **pseudonymous**, not anonymous: each row still refers
to one identifiable person, and anyone with the secret and a candidate list can
test whether a given person is present. Handle it under the same governance as
personal data. [Chapter 2.3](../02_producing/anonymisation.md).

### Who should be allowed to see what?

The separation principle: whoever performs the linkage sees identifiers but not
the substantive content; whoever performs the analysis sees the content but not
the identifiers. In practice that means two extracts from each source — a
shareable one keyed on a pseudonym, and an internal key that never leaves.

### Do we need a legal basis for the linkage itself, or is the one for the data enough?

The basis to *receive and process* a source is not the same as the basis to
*link* it, and neither is the same as the basis to *disseminate the result*. All
three are separate questions. This is the most common blocker and the one found
latest. [Chapter 4.2](../04_production/enabling-environment.md).

---

## Data preparation

### How aggressively should we clean and standardise?

There is no neutral amount. Standardising more finds more true matches and
creates more false ones; the right level depends on the error rate in your data,
which you have to measure rather than assume. Standardise in separable steps so
you can vary the level and compare results.
[Chapter 2.2](../02_producing/data-readiness.md).

### Should we drop records with missing identifying fields?

Be careful: that decision is usually taken by accident. In the toolkit's data,
requiring four fields to be present excludes about 15% of records — and those
records belong disproportionately to people with incomplete administrative
histories, whose linkage rate is already a third lower than everyone else's.
Dropping them is a decision about *who your statistics represent*, and it should
be recorded as one.

### Our two registers use different column names. Does that matter?

Yes, and no software will fix it for you. Aligning column names is preparation
work, not model configuration. It is one of the three structural prerequisites in
chapter 2.2.

### The most common "first name" in our register is a fragment like "DEL". What does that mean?

Something upstream is splitting or truncating values. It is exactly the kind of
problem that ten minutes of frequency profiling surfaces and that no schema
reveals. Fix it if you can; work around it if you cannot; document it either way.

---

## Method

### Deterministic or probabilistic?

Deterministic where identifiers are good and errors are rare, or where the method
must be simple to explain. Probabilistic where identifiers are imperfect and
errors common. Most offices use both — a deterministic pass on the clean cases, a
probabilistic model on the residue.

Keep the deterministic rule even if you end up probabilistic: it is the benchmark
that tells you whether the model is earning its complexity.

### We built a Fellegi–Sunter model and it barely beat our deterministic rule. Is it broken?

Probably not. This toolkit's own model did exactly that — recall 0.2678 against
0.2567 — and the reason is instructive.

The scoring framework alone does not move the ceiling if you are still comparing
values for **exact equality**. What moves it is comparing values that are
*similar*: string-similarity levels and phonetic encoding, so that `GONZALES` and
`GONZALEZ` are not treated like `GONZALES` and `MARTINEZ`.
[Chapter 2.4](../02_producing/linkage-approaches.md) shows the result;
[chapter 2.6](../02_producing/implementing-in-splink.md) shows the fix.

### What is a non-disagreement clause, and should we use one?

A field that is not required to *agree*, only required not to *conflict*: equal is
fine, missing is fine, different is a rejection. It is the best-value trick in
deterministic linkage — in the toolkit's data it removed three wrong pairs for
every right one it cost. Consider it for any field that is often missing but
reliable when present.

### Our fields are not independent. Does that invalidate the model?

No, but it makes it overconfident. Errors cluster by record — a carelessly
entered record tends to be wrong in several fields at once — so correlated
evidence gets double-counted and reported probabilities are more extreme than the
truth warrants. In the toolkit's data, true matches agree on both surnames 1.7
times more often than independence predicts.

Mitigations: avoid including near-duplicate fields, use term-frequency
adjustments, and **evaluate the output empirically** rather than trusting the
probabilities.

---

## Blocking

### How do I know if my blocking rules are good?

By measuring **pair completeness**: the share of true matches whose records end up
in the same block. It is a hard ceiling on recall, and everything below it is
unrecoverable by any model or threshold.
[Chapter 2.5](../02_producing/blocking.md).

### My recall is low. Should I improve the model?

Check the blocking first. In this toolkit's data, blocking discarded roughly ten
times as many true matches as the model missed. Attention naturally goes to the
model because the model is the interesting part; the data usually says the
bottleneck is upstream.

### How many blocking rules should I use?

More than one. Every rule has a systematic blind spot, and rules that fail for
*different* reasons cover each other. Then check what each actually contributes:
in this toolkit two of six rules added **zero** new candidate pairs and were pure
cost. Splink's cumulative comparison analysis shows this in one table.

### Can I block on the field I am least sure about?

No. Blocking is an irreversible filter and stricter than any comparison you make
later. Never block on a field you would not stake the linkage on.

---

## Splink specifics

### `ValueError: Expected sql condition to refer to one column but got []`

A version incompatibility, not a mistake in your code. Splink 4.0.0 declares only
`sqlglot>=13.0.0`, and recent `sqlglot` releases break its EM training. Pin
`splink>=4.0.17`, which works with both old and new `sqlglot`.

### `waterfall_chart()` fails with a ValueError about missing columns

Set `retain_intermediate_calculation_columns=True` in `SettingsCreator`. Without
it, the per-level detail the chart needs is not kept. This is the most common
first encounter with Splink's settings.

### `No function matches the given name and argument types 'list_distinct(VARCHAR)'`

You passed a string to `dmeta_col_name`. Double Metaphone returns **two** codes,
and Splink expects both as a list. Keep two columns: the primary code as a string
for blocking, and the full list for the comparison.

### The accuracy table's threshold gives nonsensical results

The `truth_threshold` column of `accuracy_analysis_from_labels_column(output_type="table")`
is in **match weight** units (log-odds), not match probability. Use the
`match_probability` column of the same table where a probability is expected.
There is no error message — the linkage just comes out wrong.

### Why do I get slightly different results every time I train?

`estimate_u_using_random_sampling()` draws a random sample and takes **no seed**.
Two runs of identical code on identical data give slightly different parameters.
The fix is to train once, save the model with `save_model_to_json()`, and load it
thereafter — which is what this toolkit does and why it ships its trained model.
[Chapter 2.9](../02_producing/end-to-end-example.md) measures the spread.

### `count_comparisons_from_blocking_rule` is not a method on my linker

It is a top-level function in `splink.blocking_analysis`, not a `linker` method.

### Splink says my model is "not fully trained". Can I ignore it?

No. It means some comparison levels have no estimate and are using defaults. Read
which ones, and either add a training rule that covers them or accept the default
knowingly and record that you did.

---

## Quality and thresholds

### What threshold should I use?

There is no general answer, and that is not evasion — it is a decision about
which error your use case can least afford, which you should have recorded before
building. A false match that harms a specific person argues for a high threshold;
a coverage estimate that will be error-corrected may prefer a lower one with a
measured error rate.

### Can I just maximise F1?

Only if you have written down that the two errors cost the same. In this
toolkit's data the F1-optimal threshold is a match probability of **0.0055**,
where one accepted pair in six is wrong. F1 is for comparing models, not for
choosing an operating point.
[Chapter 2.8](../02_producing/evaluation.md).

### We have no ground truth. Can we measure quality at all?

Yes, partly, and four methods need none: profiling linked against unlinked and
unlinkable records; comparing totals against an external reference; a clerical
gold standard on a stratified sample; and plausibility checks including cluster
sizes against the expected cardinality. See chapter 2.8.

### Our match rate is 85%. Is that good?

Unanswerable as asked. A match rate mixes true matches, false matches and genuine
overlap into one number, and it rises whenever you lower the threshold. The ONS
puts it plainly in policy: match rates "should not be used as a quality metric".
Report precision and recall, with how they were estimated.

### What does "differential linkage error" mean and why does everyone insist on it?

The error rate varies systematically across groups. Errors falling evenly inflate
variance and can often be corrected for; errors concentrated in a group **bias**
every comparison involving that group.

It is insisted on because it is common and easy to miss. In this toolkit's data
recall is 0.19 for the shortest given names and 0.47 for the longest — nothing in
the model treats them differently, short names simply carry less information.
That is a technical property becoming a substantive bias in a published statistic.

---

## Scale and infrastructure

### Will this work on our 15 million records?

The method, yes. Splink compiles to SQL and runs on Spark or Athena for data that
size, with the same model definition. What decides feasibility is the number of
**candidate pairs** your blocking generates, not the number of records — which is
why chapter 2.5 insists on counting before committing.

### Do we need a cluster?

Start without one. DuckDB on a laptop handles a great deal, and blocking is what
makes the problem tractable. Move to a cluster when the candidate pair count
demands it, not before — the switch is one argument.

### How often should we re-run the model, and should we re-estimate the parameters?

Re-run on whatever schedule the outputs need. Re-estimating every run is not
obviously right: it makes results incomparable across periods for reasons
unrelated to the population. Decide a rule — re-estimate when a source system
changes, when coverage changes materially, or on a fixed cycle — write it down,
and record which model version produced which output.

---

## About this toolkit

### Why does it use Chilean data?

Because it was available as a realistic synthetic dataset with a known ground
truth, which is rare. The registers are presented as one illustrative example,
not as a template. The methods are country-neutral; the *cleaning* is not, and
chapter 2.9 lists what to change for your own setting.

### The results here are not very good. Is that on purpose?

The example is deliberately hard: five identifying fields, **no date of birth**,
and heavily corrupted names. Genuine matches agree exactly on the given name only
a third of the time. If your sources include a date of birth, expect
substantially better results than these.

Showing a hard case honestly seemed more useful than showing an easy one. A
toolkit whose worked example reaches 98% recall teaches nothing about what to do
when yours reaches 30%.

### Can I reuse this material?

Yes. Narrative and data are CC BY 4.0, code is MIT. Please credit the INE of
Chile as the source of the frequency structure the synthetic data reproduces.

### How do I contribute a country case?

The criterion is implementation: linkage in production or in use for official
statistics, not a proposal. See
[Contributors](../00_front/contributors.md).
