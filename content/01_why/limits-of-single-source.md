# What a single source can and cannot tell you

Before deciding to link, it is worth being precise about what you are trying to
escape. The two kinds of data a statistical office works with fail in opposite
directions, and linkage is useful mainly because those failures do not overlap.

## Administrative data

Administrative data is generated as a by-product of running something: enrolling
a student, admitting a patient, registering a birth, paying a benefit,
collecting a contribution. The record exists because a transaction happened.

**What it gives you.** Coverage of the whole population that interacts with the
service, not a sample. Continuous updating, so the data reflects last month
rather than last year. Precise dates and events, recorded at the moment they
occurred rather than recalled later. And it already exists, so the marginal cost
of using it is processing rather than collection.

**What it costs you.** The record was created to run a service, not to measure
anything, and that shows up in four ways:

- *Quality varies and drifts.* Fields that matter operationally are accurate;
  fields that do not are entered inconsistently, left blank, or filled with
  placeholders. The same field can change meaning when a system is upgraded.
- *It measures the service, not the concept.* A hospital admissions file tells
  you about admissions, which is not the same as illness. A benefit register
  tells you about benefit receipt, which is not the same as poverty. The gap
  between the two is a coverage problem you have to reason about.
- *The unit is often not the person.* Many administrative files are event-based:
  one person may appear ten times, or once under two slightly different names.
  Deciding how many people a file contains is itself a linkage problem.
- *Errors in identifying fields are the norm.* Names are misspelt, abbreviated,
  transliterated, entered in the wrong field, or missing. This is precisely the
  material linkage has to work with.

## Survey and other primary data

Primary data is collected deliberately, to answer a question someone specified
in advance.

**What it gives you.** Concepts measured the way you defined them, with
documented instruments. Depth: variables that no administrative system would
ever record. Known statistical properties, because the sample was designed.

**What it costs you.** Coverage is a sample, so estimates for small groups or
small areas are imprecise or impossible. Collection is expensive, which limits
frequency. Answers are subject to recall error and to what respondents are
willing to report. And in longitudinal designs, people drop out — and rarely at
random.

## The complementarity

Set the two side by side and the argument for linking is almost mechanical.

| | Administrative | Survey / primary |
|:--|:--|:--|
| Coverage | Whole population served | Sample |
| Frequency | Continuous | Periodic |
| Cost of an additional variable | Usually impossible | High |
| Cost of an additional year | Low | High |
| Concept measured | The service's concept | Your concept |
| Data quality in identifying fields | Variable | Generally better |
| Attrition | Not applicable | A real problem |

Linking a survey to administrative records adds long-term follow-up without
re-contacting anyone, and avoids attrition. Linking administrative records to a
survey provides a validation sample against which to estimate how accurate the
administrative measurement actually is. Linking two administrative sources
extends coverage or measures how incomplete each one is. In each case the linked
dataset inherits the strengths of one source in the dimension where the other is
weak.

## Two ways of organising linkage

There is a real distinction between linking data once and building something
that links data continuously, and it has consequences for how much of this
toolkit applies to you.

**Ad-hoc linkage** answers a specific question. A defined set of files, a defined
population, a defined period. The link is created, the analysis is done, and the
linked dataset is often deleted afterwards — the "link and destroy" model. Most
first linkage projects are ad-hoc, and that is the right way to start: it keeps
the approvals narrow and the risk contained.

**Linkage systems** are maintained. They are updated as new data arrives,
documented, versioned, and used by many projects over time. New Zealand's IDI is
one. The
[SAIL Databank](https://saildatabank.com/) in Wales, based at Swansea
University's Medical School, is another: a population databank of anonymised,
linkable person-based health and social care records, made available to
accredited researchers in a secure environment.

The methods are the same. What differs is everything around them — governance,
funding, versioning, documentation, and the expectation that the pipeline will
run again next year with someone else operating it. Section 4 of this toolkit is
about that transition, and it is where most projects stall.

## When you already have an identifier

Some countries do not have this problem, or have a much smaller version of it.

Sweden has assigned a personal identity number for decades, recorded across
public registers, which makes individual-level linkage across health, education,
employment and income data a routine operation. Scotland's health system uses
the Community Health Index number for the same purpose. Where such an identifier
is present, well populated and accurate, linkage is close to a database join.

It is worth being clear about what this means for the rest of the toolkit.
**Probabilistic linkage is an answer to the absence of a reliable unique
identifier. It is not a goal in itself.** If your registers carry a national
identity number that is complete and correct, use it — a deterministic rule on a
good identifier will outperform anything in this book, and it will be far easier
to explain.

The situation this toolkit is written for is the more common one, and it has
several variants:

- there is no national identifier at all;
- there is one, but it is not recorded in the sources you want to link;
- there is one, but coverage is partial — often systematically so, with the most
  vulnerable people the least likely to have it;
- there is one, but the sources use provisional or system-specific identifiers
  alongside it, and those cannot be compared across systems;
- there is one, and it is recorded, but it contains errors you cannot detect.

The last three are the treacherous cases. A partially populated identifier
invites a linkage that quietly excludes the population you most needed to
measure. Chile's situation is a good example of the mixed case: the country has
a universal identity number, the RUN, and where it is present linkage is largely
deterministic — but administrative systems assign provisional identifiers to
people whose registration is still in progress, and those provisional numbers
cannot be used to follow a person between systems. That is what creates the need
for probabilistic linkage in an otherwise well-identified country, and it is the
situation the synthetic data in this toolkit reproduces.

The [next chapter](linkage-as-a-solution.md) sets out what record linkage
actually is, the two families of methods, and the constraints — legal, ethical
and practical — that shape a linkage project before any code is written.
