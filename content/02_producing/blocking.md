# Blocking: scalable candidate generation

Comparing two files of sizes $n_1$ and $n_2$ means $n_1 \times n_2$ comparisons.
That number grows with the *product* of the file sizes, while the number of true
matches grows only with the smaller one. Bigger data is therefore not merely more
expensive — it is proportionally harder, because the true matches become an ever
smaller fraction of what you are searching through.

| Scenario | Comparisons | Hours at 100,000 per second |
|:--|--:|--:|
| The registers in this toolkit | 810 million | 2.3 |
| Two municipal registers | 30 billion | 83 |
| A national register pair | 15 trillion | 41,667 |
| A census against an administrative source | 216 trillion | 600,000 |

The last row is an ordinary task for a statistical office, and it is 68 years of
compute. **Blocking** is what makes it possible: only compare records that
already agree on something.

It is also the most consequential decision in the pipeline, for a reason worth
stating bluntly at the outset. **A match excluded by blocking is a match no model
will ever see.** It cannot be recovered by a better comparison function, a longer
training run, or a lower threshold. Blocking is the one irreversible step.

The accompanying notebook,
[Blocking: generating candidate pairs](nb05-blocking-strategies.ipynb), builds
and measures every rule discussed here.

## Three metrics

A blocking rule is judged on three quantities, and you cannot maximise all of
them.

**Reduction ratio (RR)** — how much work it removes:
$RR = 1 - \frac{\text{candidate pairs}}{\text{all possible pairs}}$

**Pair completeness (PC)** — how many true matches survive it:
$PC = \frac{\text{true matches among candidates}}{\text{all true matches}}$

**Pairs quality (PQ)** — how concentrated the true matches are among the
candidates:
$PQ = \frac{\text{true matches among candidates}}{\text{candidate pairs}}$

RR and PC pull directly against each other. PC is normally the binding
constraint, because a loss here is permanent.

All three can be computed from block sizes alone, without ever materialising the
pair list — which matters, because on real data you cannot build the thing you
are trying to avoid building.

## Four kinds of rule, measured

Results on the bundled registers, where 810 million pairs contain 4,500 true
matches:

| Rule | Candidate pairs | RR | PC |
|:--|--:|--:|--:|
| Sex | 383,064,445 | 0.5271 | 0.8678 |
| Given name AND first surname | 1,291 | 1.0000 | 0.2540 |
| First surname AND second surname | 3,546 | 1.0000 | 0.2764 |
| Given-name initial AND both surnames | 1,338 | 1.0000 | 0.2607 |
| Both surnames, phonetically encoded | 7,998 | 1.0000 | 0.3836 |
| **Union of the four rules above** | **8,207** | **1.0000** | **0.3944** |

### Simple blocking

One field. Blocking on sex keeps 87% of the true matches and removes 53% of the
work — which sounds acceptable until you read the absolute number. Half of 810
million is still 383 million.

The reduction a blocking key can achieve is governed by how many distinct values
it has and how evenly records spread across them. **A blocking key must be
high-cardinality.** This is the same property that made a field valuable in the
Fellegi–Sunter table, reappearing in a different role — and it is why sex and
nationality, useful as comparison fields, are useless as blocking keys on their
own.

### Conjunctive blocking (AND)

Requiring agreement on several fields multiplies the number of blocks and shrinks
each one. Reduction ratios go to effectively 1: these rules discard over 99.99%
of the work.

And they discard three quarters of the matches. A conjunctive rule **inherits the
weakness of every field in it**: it demands exact agreement on two error-prone
fields, and we know from [chapter 2.4](linkage-approaches.md) that genuine
matches agree exactly on a given name only a third of the time.

This is the central danger. Judged on reduction ratio these rules look superb.
They would silently throw away most of what you were looking for, and nothing in
the output would tell you.

### Relaxing a component

Keeping the conjunction but weakening one part of it — the given-name *initial*
instead of the whole name — buys completeness at almost no cost in reduction. It
survives abbreviation, compound given names recorded differently, and any error
after the first character.

### Phonetic blocking

Names that sound alike are often spelled differently. A phonetic encoding maps
`GONZALEZ` and `GONZALES` to the same code, `MUNOZ` and `MUNIOZ` to the same
code, so blocking on the code catches pairs that blocking on the raw value
misses. Double Metaphone is the usual choice for Latin-script names.

On these registers it is the single best rule tried: PC 0.3836 against 0.2764 for
the same fields compared exactly, at rather more candidate pairs. Larger blocks
in exchange for recall — the trade in its usual form.

### Disjunctive blocking (OR)

No single rule is good enough, and every rule has a systematic blind spot: a
record with a mistyped given name is lost by any rule using the given name.

So run several rules and take the **union**. A pair survives if *any* rule keeps
it, so it is lost only if it fails all of them at once — far less likely than
failing one. The union above reaches PC 0.3944, higher than any of its components,
with the reduction ratio still effectively 1.

This is why production pipelines specify several blocking rules rather than one,
and why linkage libraries take a *list* of rules and union the results
automatically.

## The recall ceiling

This is the number to take away from the chapter.

Pair completeness is not a diagnostic. It is a **hard ceiling on the recall of
everything downstream**. On the bundled data, with the four-rule union:

> Of 4,500 true matches, 1,775 can still be found by a model.
> **2,725 are already lost, whatever the model does.**
> Maximum achievable recall from here: **39.4%**.

Two consequences follow.

**When a model reports recall, read it against this ceiling, not against 1.0.**
The shortfall below the ceiling is the model's responsibility; everything above
it is blocking's. Reporting a recall figure without stating the blocking ceiling
is one of the commonest ways a linkage quality statement misleads its reader —
and it usually flatters the model.

**If the ceiling is too low, the answer is not a better model.** It is another
blocking rule that catches the kind of record the current set loses.

Note what was *not* done in choosing that union: it is not the rule with the
highest pair completeness. That was blocking on sex, at PC 0.8678 and 383 million
candidate pairs. **Pair completeness alone does not choose a rule; it chooses a
rule subject to a candidate count you can afford.**

### Raising it

The procedure is mechanical once you see it. All four rules in the union demand
exact agreement on at least one full name field, so they share a failure mode:
records where the given name *and* a surname are both corrupted. Two more rules
built to fail differently — phonetic codes on the given name and first surname,
and the given-name initial with phonetic surnames — raise the ceiling from
**39.4% to 41.8%** at 1.1 times the candidate pairs.

Identify the failure mode the current rules share, add a rule that does not share
it, measure again. Keep going until the ceiling is acceptable or the candidate
count stops being affordable. Those are the only two constraints.

## Sorted neighbourhood

A different idea. Instead of equality — same block or not — sort all records by a
key and compare each record with the *w* records nearest to it in the sort order.
Its appeal is graceful degradation: a record whose key is slightly wrong lands
slightly out of position rather than in a different block, and its partner may
still be inside the window.

On these registers, sorting on the first surname with a window of five reaches
**PC 0.4562** — higher than the union of equality rules — but generates
**1,299,696** candidate pairs instead of 8,207, roughly 160 times as many. It
buys recall with compute at a poor exchange rate here.

That does not make it a bad method, and the two are not mutually exclusive:
sorted neighbourhood can be one more rule inside a union. Its characteristic
strength is keys with a meaningful *ordering* — dates, numeric identifiers,
concatenated multi-field keys — where being slightly wrong means being slightly
out of position. The union of well-chosen equality rules remains the standard
starting point, and it is the approach Splink is built around.

## How to choose

1. **Start from pair completeness, not reduction ratio.** Decide the recall
   ceiling your use case can live with. Everything else is negotiable; this is
   not.
2. **Use several rules and union them.** Rules that fail for different reasons
   cover each other.
3. **Include at least one rule that avoids your weakest field.** If given names
   are unreliable, at least one rule should not need one.
4. **Count candidate pairs before running anything.** A rule that would generate
   a billion pairs is not a rule. Find out before you wait for it.
5. **Never block on a field you would not stake the linkage on.** Blocking is a
   stricter, and irreversible, version of every comparison you make later.

## What to record

Blocking decisions belong in the quality statement, not in a comment in the code:

- every blocking rule, as code;
- candidate pairs generated, per rule and in total;
- pair completeness, where a ground truth or clerical sample allows it —
  and if it does not, say so;
- the resulting recall ceiling, stated explicitly;
- which failure modes the rule set is known not to cover.

With prepared data, a linkage approach and a blocking strategy,
[chapter 2.6](implementing-in-splink.md) assembles all of it in a real library —
and adds the comparison functions that finally let partial agreement count.
