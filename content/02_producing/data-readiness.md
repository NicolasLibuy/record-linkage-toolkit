# Data readiness and preparation

You now know what you are trying to produce. This chapter asks whether the
sources can deliver it, and turns them into something a linkage method can
actually compare.

It is the least glamorous part of a linkage project and the part with the
highest return. A well-chosen method applied to badly prepared data will lose
matches that a crude method applied to well-prepared data would have found.

The accompanying notebook,
[Inspecting and preparing two registers](nb01-inspect-and-prepare.ipynb), runs
every check below on the bundled data. The figures quoted here come from it.

## Four questions to ask of every source

### 1. What is actually in it?

Read the data as **text**, always. Letting a CSV reader infer types is the most
common way to silently damage identifying data: identifiers that look numeric
lose their leading zeros, codes become floats, and the string `NA` becomes a
missing value indistinguishable from a genuinely blank field.

Then look at the columns you might compare, and at nothing else yet.

### 2. How complete is each identifying field?

A field you cannot see is a field you cannot compare. But the per-field
percentage is not the number that matters. What matters is how many records
carry **enough** fields to be compared at all.

In the bundled registers, each identifying field is missing for between 2% and
5% of records — unremarkable. But because the missingness is spread across
fields rather than concentrated in the same broken rows, only **80.5%** and
**82.5%** of records carry all five identifying fields. A rule that requires
every field to be present therefore discards about one record in five before it
starts.

Whether that is acceptable depends on **who** those records are. If
incompleteness is random, you lose precision. If the people with incomplete
records differ systematically from the rest — younger, more mobile, more
recently registered, less formally employed — then dropping them biases the
result in exactly the dimension you were probably trying to measure. Section 3
returns to this as differential linkage error; the point here is that the
decision is taken during preparation, often without anyone noticing they took
it.

### 3. How much can each field distinguish one person from another?

Completeness tells you whether a field is there. **Cardinality** tells you
whether it is worth anything.

In the bundled data, given name takes around 20,000 distinct values and each
surname around 10,000. Sex takes two. Nationality takes two, with one value
covering 82% of records.

That ordering is the whole intuition behind probabilistic linkage, visible
before any model exists. Agreement on a given name is meaningful evidence that
two records describe the same person; agreement on sex is barely evidence at
all, since half the population agrees with any given record by chance. Chapter
2.4 turns this into a number — the *u*-probability — but you can rank your
fields today, with a `value_counts()`.

Low-cardinality fields are still worth keeping. Disagreement on sex is
reasonably strong evidence *against* a pair even though agreement is weak
evidence for it, and both fields are cheap. What you must not do is build a rule
that relies on them.

Profiling also surfaces problems that no method will fix for you. Two real
examples from the bundled data:

- The most common first surname is `GONZÁLEZ` in one register and `GONZALEZ` in
  the other. Same surname, and as the data stands the two values will never
  compare as equal. The fix is cleaning, applied identically to both sides.
- The most common *given name* in one register is `DEL` — not a name, but a
  fragment of a compound one. When a token like that reaches the top of a
  frequency table, something upstream is splitting or truncating values.

Neither of these is visible from a schema. They are visible from ten minutes of
profiling.

### 4. How many records does the source hold per person?

If a register contains the same person three times, every match is a match to
three records, and any count you publish is wrong.

With no identifier available, use the identifying fields themselves as a rough
proxy and count how many records share all of them. In the bundled registers the
answer is zero: both files are person-based. In real administrative data the
answer is frequently very different, and it sends you back to the file-type
decision in [chapter 2.1](defining-the-use-case.md).

## Three structural prerequisites

Separately from quality, linkage software needs three things about *shape*.
Splink is explicit about them, and the requirement is not really Splink's — it
is inherent to comparing records.

1. **A unique identifier per record within each source.** Not a person
   identifier — a *row* identifier. It is how the software tells you which pairs
   it matched, and it must be unique and non-missing.
2. **The columns to be compared must have the same names in both sources.** If
   one register calls it `nombre` and the other `first_name`, nothing will
   compare them. Renaming is preparation work, not model configuration.
3. **One row per record, with comparison fields as columns.** Tidy data. A file
   with names spread across a repeated block of columns has to be reshaped
   first.

The second one catches people out, and it is worth checking explicitly rather
than assuming.

## Cleaning and standardisation are different things

The words get used interchangeably. Keeping them apart clarifies what you are
doing and why.

**Cleaning** corrects or removes what is wrong. Trimming whitespace, fixing
inconsistent capitalisation, applying validation rules that reject impossible
values — a date of birth after today, a placeholder like `XXXX` or `999999`,
a name field containing a phone number. Cleaning makes values *more* correct.

**Standardisation** is a deliberate *coarsening*. It makes different values
identical on purpose, so that variants of the same underlying value compare
equal:

- removing accents and punctuation: `O'Malley`, `OMalley`, `O Malley` → `OMALLEY`
- reducing to a substring or an initial: `OMALLEY` → `OMA`
- phonetic encoding, so that names that sound alike encode alike
- collapsing a numeric variable into bands: age in years → age group

Standardisation makes values *less* precise, on purpose. That is not a
side-effect; it is the mechanism.

A third preparation step is worth naming: **coding**. Comparisons are made and
stored in enormous numbers, and numbers are cheaper to store and compare than
strings. Any field with a limited set of categories can be encoded numerically,
and geographic information can be **geocoded** into coordinates or area codes,
which additionally makes distance-based comparison possible — a very effective
comparison field where addresses exist.

## The standardisation trade-off

This is the central judgement of the whole chapter, and it does not have a
default answer.

**Standardising more raises sensitivity and lowers specificity.** You find more
of the true matches, and you also create more false ones. Standardising less
does the reverse.

The classic illustration is the surname `O'Malley`:

- In a **high-quality** source where clerks record apostrophes reliably,
  `O'MALLEY`, `OMALLEY` and `O MALLEY` are probably three different people.
  Collapsing them manufactures false matches.
- In a **low-quality** source they are almost certainly one person entered three
  ways. Not collapsing them loses two matches.

So the right amount of standardisation depends on the error rate in your data —
which you have to measure, not assume.[^1]

You can see the effect directly. In the bundled data, cleaning that only fixes
case and whitespace merges a single surname spelling. Adding accent and
punctuation removal merges **401**. Those 401 are matches that would otherwise
have been missed, and false matches that have just become possible. Which of
those two effects dominates is an empirical question, and [chapter 2.8](evaluation.md) shows how
to answer it by trying both and measuring.

The practical advice: standardise in explicit, separable steps rather than one
opaque function, so you can vary the level and compare. And apply exactly the
same steps to both sources — cleaning the two sides differently is a reliable
way to destroy matches you would otherwise have found.

## What to record

Preparation decisions are model decisions, and they should be documented as
such. At minimum:

- the exact cleaning and standardisation applied to each field, as code;
- completeness and cardinality of each identifying field, per source, measured
  before and after;
- how many values each standardisation step collapsed;
- any records excluded, how many, and on what rule;
- the duplication check and its result.

The notebook produces all of these. Section 3 turns them into a quality
statement that can be published.

With comparable, profiled, standardised data, [chapter 2.3](anonymisation.md)
covers what to do when the extracts have to cross an institutional boundary
before they can be linked — and [chapter 2.4](linkage-approaches.md) starts
comparing records.

[^1]: Randall, S. M., Ferrante, A. M., Boyd, J. H. and Semmens, J. B. (2013).
*The effect of data cleaning on record linkage quality.* BMC Medical Informatics
and Decision Making, 13:64.
