# Country case examples

A living list of national statistical offices that have implemented record
linkage and are **using it in the production of official statistics**.

```{note}
**This list is currently empty, and that is deliberate.**

The criterion below is implementation, not intention. As of September 2026 no
case has yet met it and been agreed for publication. Entries will be added
individually as they qualify.
```

## What qualifies

**One criterion: the linkage is in production or in use for official
statistics.**

A well-argued project proposal is not a country practice. Neither is a pilot that
produced a result nobody uses, nor a methodological study, however good. The
distinction matters because the purpose of this section is to show other offices
what *worked in an operating statistical system* — including the parts that are
never in a methods paper: how access was obtained, who runs it, what broke.

Concretely, a case qualifies when:

- the linkage has been run on real national data, not only on a sample or a
  synthetic extract;
- its output is used — in a published statistic, an operational register, or a
  decision process;
- the office is willing to have it described publicly.

A case does **not** qualify merely because the linkage worked. It has to be in
use.

## Why the bar is set there

Three reasons, all learned from building this section.

**A proposal teaches the wrong lesson.** A well-designed linkage that never ran
tells a reader what a good design looks like, and nothing about what happens when
it meets a real register, a real legal department and a real deadline. That
second thing is what is scarce.

**Publishing a case implies an endorsement.** An office reading this section will
reasonably assume that a listed case is one to copy. That should only be true of
cases that survived contact with production.

**Nobody benefits from a padded list.** A short list of real cases is more useful
than a long list of aspirations, and it stays credible.

## Publication is incremental

Cases are added **as each reaches a publishable stage**, not held back until a
complete set exists. There is no target number and no schedule. A section with
two real cases and an open invitation is doing its job.

## Open beyond any one cohort

This toolkit came out of a training programme with eight countries, but this
section is not about them. Any national statistical office, or any national
institution producing official statistics, is welcome to contribute a case
meeting the criterion above — whether or not it has any connection to that
programme, and whether or not it used the methods in this toolkit.

A case that reached production with RELAIS, with fastLink, with a deterministic
rule set, or with software written in-house is as welcome as one that used
Splink. What is being documented is the *practice*, not the tool.

## How to contribute a case

[Open an issue](https://github.com/NicolasLibuy/record-linkage-toolkit/issues) or
contact UNSD. A case write-up is short — one to two pages — and should cover the
following. Nothing here requires disclosing anything confidential; if a question
cannot be answered publicly, say so, which is itself informative.

### 1. The use case
What was linked to what, and what statistic or process it feeds. What the office
could not do before.

### 2. Institutional setting
Which institutions were involved and what the legal basis was. How access was
obtained, and roughly how long that took. **This is the part other offices most
want and most rarely get.**

### 3. Data
The sources, their approximate size and coverage, the identifying variables
available, and whether a common identifier existed.

### 4. Method
Deterministic, probabilistic, or both, and in what order. The software. Blocking
strategy. How parameters were estimated. How the threshold was chosen and by
whom.

### 5. Quality
What was measured and how — precision, recall, the basis for estimating them, the
blocking ceiling if known. Whether error was found to be differential, and across
what. What could not be measured.

### 6. Production
Who owns and runs the pipeline. How often it runs. What happens when a source
system changes.

### 7. What you would do differently
The most useful section of any case study, and the one most often omitted.

### 8. References
Links to any published methodology, so a reader can go to the primary source.

## Meanwhile

Externally documented national cases — the UK, Uruguay, Colombia and Chile — are
written up in [Documented national practice](other-practices.md), each read from
the office's own published methodology.

They are in a separate chapter for a reason: they are cases this toolkit's
authors read about, not cases the offices contributed. A contributed case, with
the access negotiation and the "what we would do differently", is worth more.
