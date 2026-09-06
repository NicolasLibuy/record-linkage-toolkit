# The enabling environment

The previous chapter listed what stops linkage projects. This one describes what
a settled arrangement looks like, using structures that statistical offices have
published rather than an idealised model.

Not every office needs all of it. A small office running one linkage a year needs
the same *decisions* taken as a large one, but taken by fewer people and written
down more briefly. What does not scale down is the requirement that the decisions
be taken at all, and by someone with the authority to take them.

## 1. Legal basis and ethics

Two separate permissions, often confused:

- the basis for **linking** the sources;
- the basis for **disseminating or using** what the linkage produces.

An office may have the first and not the second. Establish both in writing before
building, and record them in Section A of the quality statement
([chapter 3.1](../03_quality/communicating-methods.md)).

Ethics is a distinct question from legality and needs its own route. The ONS
requires linkage to comply with the UK Statistics Authority's ethical principles
and its own data ethics policy, with a Data Ethics team that operates a
self-assessment tool and refers high-risk projects to an independent advisory
committee. The specific bodies are British; the pattern generalises: **a routine
self-assessment for ordinary projects, and a named route for escalating the
unusual ones.**

The ONS policy also states the purpose test plainly — linkage is conducted "only
for the purposes of producing statistics or undertaking research that serve the
public good". A statement of that kind is worth having, because it is what makes
the answer to "why are you combining these records?" institutional rather than
personal.

## 2. Governance: who decides, and who is accountable

The most useful thing an office can copy is not a method but an org chart. The
ONS's [data linkage and matching policy](https://www.ons.gov.uk/aboutus/transparencyandgovernance/datastrategy/datapolicies/datalinkageandmatchingpolicy)
assigns these roles:

| Role | Responsible for |
|:--|:--|
| **Data Linkage Hub** | Consistent application of the linkage policy across the office |
| **Data Linkage Lead** | Maintaining and reviewing the policy |
| **Staff carrying out linkage** | Complying with it, and consulting the Hub *before* starting any linkage |
| **Methodology** | Advice on method, developing consistent methods, **training and building capability** |
| **Legal Services** | Advice on legal issues |
| **Senior Information Risk Officer** | Data security |
| **Information Asset Owner** | A specific data asset; accountable to the SIRO |
| **Data Ethics team** | Ethics self-assessment; escalation of high-risk projects |
| **Independent ethics committee** | Independent advice on ethics |
| **Data Governance Committee** | Consistent application; assessing organisational risk from linking |

Three features of that list are worth taking even into a small office.

**A single point of consistency.** Staff consult the Hub *before* commencing
linkage. That one rule is what prevents every project inventing its own method
and its own quality standard.

**Capability-building is somebody's job.** It sits with Methodology, explicitly.
Where it belongs to nobody, chapter 4.1's fourth barrier — one person holds the
capability — is the default outcome.

**Security, ethics and asset ownership are separate and named.** Not because
three committees are better than one, but because the three questions are
different and each needs someone who can answer it.

A small office can compress this to three named people: one who owns the method,
one who owns the legal and ethical questions, and one who owns the data asset.
What it cannot compress to is zero.

## 3. Quality standards set in advance

A linkage without a stated quality objective drifts, because there is no
condition under which it is finished.

The ONS policy is unusually direct about what must be reported, and it is worth
quoting as a floor:

> The quality of each linkage project should be assessed in terms of the errors
> made, with estimates of precision … and recall … always being reported.

and about what must **not** be reported as quality:

> Match rates give no indication of the quality of the linkage, just the number
> of matches made and so should not be used as a quality metric.

That is the same conclusion [chapter 3.3](../03_quality/quality-metric-reference.md)
reaches from the arithmetic, arrived at independently and stated as policy.

Three further points from the same document are worth institutionalising:

- **Every new pair of datasets generates new errors**, even if each has been
  linked to something else before. Quality does not transfer between projects.
- **The quality target should be proportionate**: effort invested should match
  "the potential use and impact of the linked data".
- **Errors chain.** The more datasets linked in sequence, the more scope for
  quality to degrade.

## 4. Measurement built into the register, not the project

The strongest position an office can reach is one where linkage quality is
measured as a routine property of its administrative registers, rather than
re-derived for each project.

Chile's national statistical office publishes a **battery of nineteen quality
indicators for administrative registers**, covering timeliness, metadata
compliance, error and duplication rates, completeness, under- and over-coverage,
editing, imputation and coding rates, and coherence with other sources. Two of
the nineteen are about linkage directly:

- **rate of unlinkable units** — records that cannot participate in linkage at
  all, the distinction [chapter 2.8](../02_producing/evaluation.md) argues matters;
- **linkage rate from one register to a base register**.

The guidance attached to the linkage-rate indicator is careful in exactly the way
this toolkit argues for. It notes that a low rate may mean the registers cover
different populations, *or* that the linkage process is not finding the
connections that exist, *or* that the linkage variables have quality problems
requiring prior editing — three very different diagnoses that a single number
cannot distinguish. And it states that the indicator may need to be analysed for
specific subpopulations, which is differential linkage error, arrived at from the
register-quality side.

An office that measures these routinely knows before a project starts whether its
registers can support the linkage. That is a materially different position from
discovering it during the pilot.

*Source: Instituto Nacional de Estadísticas (Chile), guide to quality indicators
for administrative registers, v1.0, March 2024.*

## 5. Staffing

Linkage capability needs somewhere to live.

Uruguay's national statistical office ran its combined census of 2023 — which
uses probabilistic linkage in production — through a standing **Administrative
Records Area**, with a named coordinator, a team of data analysts, and external
methodological advice. That is the shape that works: a small permanent team with
the method as part of its remit, rather than a task assigned to whoever is
available.

Minimum viable staffing for an office starting out:

- **two people** who can run and modify the pipeline, so it is not a single point
  of failure;
- **one methodologist** accountable for the method and its quality;
- **access to legal advice**, not necessarily dedicated;
- **a named owner** for the pipeline after the pilot ends.

The training programme this toolkit came from recommended country teams of up to
four, deliberately mixing methodological, subject-matter and IT roles. That mix
matters more than the headcount: a linkage project that is only a methods project
fails on access, and one that is only an IT project fails on quality.

## 6. Infrastructure

Modest, and worth stating because it is often assumed to be the obstacle when it
is not.

- **Compute.** A linkage library that compiles to SQL runs the same model on a
  laptop and on a cluster. Start on the laptop; move only when the candidate pair
  count demands it.
- **Storage.** For the candidate pairs and the outputs. Blocking, not hardware,
  is what makes this tractable.
- **A secure environment** matching the sensitivity of the linked data — which,
  as the ONS policy notes, may be *higher* than that of either input, because
  combining sources creates a new asset that reveals more than either did. Reassess
  the sensitivity of the linked file rather than inheriting it.
- **Version control**, for the code and the trained model.

## 7. From a pilot to a repeatable process

The final step, and the one with the least written about it.

**Schedule the re-run.** Put it in the production calendar alongside the outputs
that depend on it. A linkage with no scheduled re-run is a project, not a process.

**Version the model, and decide when to re-estimate.** Parameters estimated on
last year's data may not fit this year's — after a source system changes, after a
coverage expansion, after a change in how a field is collected. Re-estimating
every run is not obviously right either: it makes results incomparable across
years for reasons unrelated to the population. Decide the rule, write it down, and
record which model version produced which output.

**Monitor for drift.** A small set of numbers, checked every run, catches most
problems: candidate pairs generated, links produced, the score distribution's
shape, the cluster-size distribution, and the linkage rate for the subgroups you
know are vulnerable. A sharp move in any of them means something upstream changed.

**Keep the quality statement current.** It describes a specific run. When the
model or the data changes, it is out of date, and an out-of-date quality statement
is worse than none because it is believed.

## A maturity ladder

Useful for locating where an office is, and what the next step is — rather than
as a target to reach in one move.

| | Access | Method | People | Repeatability |
|:--|:--|:--|:--|:--|
| **1 — Exploratory** | Ad hoc, per project | A notebook | One person | Not reproducible |
| **2 — Piloted** | Negotiated per project | Documented method, quality measured | Two people | Reproducible from a clean checkout |
| **3 — Established** | Standing agreement | Method and quality targets set by policy | A team with a named owner | Scheduled re-runs; model versioned |
| **4 — Institutionalised** | Routine access under a standing basis | Register quality measured continuously | Capability-building is someone's job | Monitored for drift; quality published |

Most offices beginning linkage are at level 1, and the useful question is not how
to reach level 4 but what single change moves them to level 2. Judging by where
projects actually stall, that change is usually the access agreement and the
reproducibility checklist — not the model.

[Section 5](../05_practices/linkage-by-use-case-domain.md) turns to what offices have done with linkage once these arrangements are in place.
