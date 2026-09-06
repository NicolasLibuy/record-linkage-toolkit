# Defining the use case

Linkage projects fail for two reasons. The second is technical. The first, and
much more common, is that nobody wrote down precisely what was being linked to
what, for whom, and to what standard — so the decisions that follow have nothing
to be judged against.

This chapter is about producing that specification. It has no code, and it takes
an afternoon. Skipping it costs weeks.

## Start from the question, not the data

The instinct is to start from the two files you happen to have. Resist it long
enough to answer: **what would you be able to say that you cannot say today?**

A usable statement of purpose is specific enough to be wrong. Compare:

> *We will link the civil registry with the health facility register to improve
> data integration.*

with:

> *We will link death records from the civil registry to deaths recorded in the
> health facility register in order to estimate the completeness of death
> registration by region and sex, to be published annually as an input to
> mortality statistics.*

The second tells you what the output is, who uses it, at what frequency, and at
what level of disaggregation. Every design decision later — which fields to
compare, how aggressive to be about blocking, where to set the threshold — has
an answer that follows from it. The first tells you nothing, and so every
decision becomes a matter of taste.

## The nine things to specify

### 1. The statistical objective

One or two sentences. What will exist afterwards that does not exist now, and
what decision or publication does it feed?

### 2. The population and the unit

Which people, in which period, and what is one row of the answer? A person? A
person-year? An event? Getting this wrong is expensive, because it determines
whether duplicates in your sources are a problem to be solved before linking or
a feature to be preserved.

### 3. The sources

For each: who controls it, what period it covers, what population it is
supposed to cover, and what it actually covers. The gap between the last two is
usually the most important thing in the specification.

### 4. The file type

Two kinds:

- **Person-based** — each person appears at most once. A death register.
- **Event-based** — each person may appear many times. A hospital admissions
  file, a benefit-payment file, a school enrolment file.

An event-based file has to be handled deliberately. Either you deduplicate it
first, which is itself a linkage problem applied within a single file, or you
link at the event level and aggregate afterwards. What you cannot do is ignore
the question, because then the number of matches you report is a count of events
dressed up as a count of people.

### 5. The link type

Three cases, and every linkage library asks you which one you are in.

| Link type | What it does | Typical use |
|:--|:--|:--|
| **Deduplication** | Finds records within *one* file that refer to the same entity | Building a statistical register from a messy administrative file |
| **Link only** | Finds records across *two* files, assuming each is already clean | Joining two person-based registers |
| **Link and deduplicate** | Both at once, across and within | Two event-based files with internal duplication |

Choosing "link only" when your sources contain internal duplicates does not
produce an error. It produces a linked dataset in which some people appear
several times, and nothing warns you.

### 6. The expected cardinality of the link

How many records on the right should match one record on the left?

- **1:1** — one person, one record on each side. A death should link to one death.
- **1:many** — one person on the left, several records on the right. A person in
  a population register linking to their several hospital admissions.
- **many:many** — both sides event-based.

This is worth stating in advance because it is one of the few checks you can
apply to your results without a ground truth. If you specified a 1:1 link and
the output contains records that matched to eleven others, something is wrong,
and you know it without needing to know the right answer.

### 7. What counts as a match

Define the entity, not the record. Is a person who changed name the same person?
Is a person recorded with a different sex in the two systems the same person? Is
a business that merged the same business?

These are not edge cases; they are a meaningful share of the hard pairs, and
someone will have to decide. Better to decide now, in writing, than at the point
of clerical review when the deadline is close.

### 8. Which error you can least afford

Every linkage trades false matches against missed matches, and the trade is
explicit: it is where you set the threshold. Which way you should lean is a
property of the use case, not of the data.

- Linking to determine **individual entitlement** — a benefit, a payment, a
  service — makes a false match a serious harm to a specific person. Lean
  towards precision.
- Estimating **coverage of a register** makes missed matches directly
  understate the thing you are measuring. Lean towards recall, and measure the
  residual error.
- Building a **research dataset** usually tolerates a known, quantified error
  rate in both directions, provided it is documented and roughly non-differential.

Write down which one you are in. Chapter 2.7 turns this into a number.

### 9. What the output has to support

A file of matched pairs, a deduplicated register, a single indicator, a
published table with a quality statement? This determines how much of Section 3
applies to you, and whether the pipeline needs to run once or every year.

## Worked example: the running case in this toolkit

The synthetic registers used throughout are specified like this.

| Item | Specification |
|:--|:--|
| **Objective** | Estimate the overlap between a health-insurance register and a social-security register, and produce a linked person-level dataset for analysis of coverage across the two systems |
| **Population and unit** | Individuals appearing in either register; one row per person |
| **Sources** | Health insurance register (30,000 records); social security register (27,000 records) |
| **File type** | Both person-based: no person appears twice in either file |
| **Link type** | Link only |
| **Expected cardinality** | 1:1 |
| **What counts as a match** | The same natural person, allowing for variation in the spelling of names |
| **Identifying variables** | Given name, first surname, second surname, sex, nationality. No date of birth, no address, no national identifier |
| **Error preference** | Balanced: this is a statistical estimate, not an entitlement decision, so precision and recall are weighted equally and both are reported |
| **Output** | Linked pairs above a stated threshold, clustered into persons, with a quality statement |

That specification is doing real work, and two entries in it are load-bearing.

**There is no date of birth.** Date of birth is normally one of the most useful
identifying variables available: it is highly discriminating, reasonably stable,
and errors in it are often detectable. Its absence here means everything rests
on names plus two low-cardinality fields, which is a genuinely hard setting.
If your sources do have a date of birth, expect substantially better results
than the ones in this toolkit, and treat these as a demanding case rather than a
typical one.

**The error preference is balanced.** That single choice is what makes it
legitimate, later, to select a threshold by maximising the F1 score. Under a
different preference the same model would be operated at a different threshold,
and the linked dataset would be a different dataset. The model does not change;
the specification decides how it is used.

## A template

Copy this into your project documentation and fill it in before writing code.

```text
1. Statistical objective
2. Population, period, and unit of analysis
3. Sources: controller, coverage, period, known limitations
4. File type of each source (person-based / event-based)
5. Link type (dedupe / link only / link and dedupe)
6. Expected cardinality (1:1, 1:many, many:many)
7. Definition of a match
8. Identifying variables available in each source
9. Which error is more costly, and why
10. Required output and its intended use
11. Legal basis and approvals required
12. Who runs this, and how often
```

Items 11 and 12 are not technical, and they are the ones that determine whether
a working pipeline ever becomes a statistical output. Section 4 is about them.

With the specification written, [chapter 2.2](data-readiness.md) checks whether
the sources can actually deliver it.
