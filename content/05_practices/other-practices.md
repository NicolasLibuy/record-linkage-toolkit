# Documented national practice

Four national cases, each read from the office's own published methodology rather
than from secondary description. They are here because each settles a question
this toolkit raises, using evidence from production rather than from a worked
example.

## United Kingdom — 2021 Census to Coverage Survey matching

**Source.** Office for National Statistics, *Quality Control and Quality
Assurance Strategy for 2021 Census to CCS Person and Household Matching*
(Shipsey and Edwards, May 2021), Methodological Assurance Review Panel paper
EAP163.

Matching the Census Coverage Survey to the census is part of producing the
population estimate, and the office notes that the estimation process "does not
deal well with errors in matching". So it sets targets in advance:

> - Fewer than 0.1% false positives (incorrect matches) i.e. precision is greater
>   than 99.9%
> - Fewer than 0.25% false negatives (missed matches) i.e. recall is greater than
>   99.75%

The paper then does something worth copying: it translates those percentages into
counts. In 2011 they meant fewer than 650 incorrect matches and fewer than 1,629
missed ones. A percentage is hard to argue about; a count of wrong records is not.

**What settles a question here.** The 2021 matching runs in six sequential
stages:

1. automatic deterministic matchkeys
2. automatic probabilistic matching, above a threshold
3. clerical review of the probabilistic **clerical resolution zone**
4. clerical review of matched households containing unmatched persons
5. clerical review of unmatched households containing matched persons
6. clerical review of automatically generated lists of best possible matches

That is the stepwise design of
[chapter 2.4](../02_producing/linkage-approaches.md) in production, at national
scale, on a statistic that matters — deterministic first, probabilistic on the
residue, and clerical review of the uncertain middle rather than a threshold
deciding it silently.

It also confirms the argument in
[chapter 2.7](../02_producing/thresholds-and-clustering.md) that the borderline
band deserves human judgement. ONS does not set a threshold and accept the
consequences; it defines a resolution *zone* and staffs it.

Note finally that precision is estimated by **sampling** the matches produced by
each method, with an estimated global false-positive rate assembled from the
parts. Even here, with a census, there is no full ground truth — the office
estimates precision the way [chapter 2.8](../02_producing/evaluation.md)
describes.

## Uruguay — Combined Census 2023

**Source.** Instituto Nacional de Estadística, methodological documentation of
the *Censo Combinado 2023*.

Uruguay's 2023 census combined field enumeration with a population register
(REPoR). Probabilistic linkage is not a pilot here: it is inside the production
of the census.

The design is stepwise, and in the order this toolkit recommends. First a
**deterministic** linkage on the statistical person identifier and date of birth.
Then a **probabilistic** method based on Fellegi–Sunter models for what remains.

**What settles a question here.** Uruguay uses a probability threshold to make a
population decision. Each person in the register is assigned a probability of
being resident in the country on the census reference date, estimated from their
traces across administrative sources — health, education and others. That
produces a ranking, and a threshold on it determines the resident population from
the register.

This is [chapter 2.7](../02_producing/thresholds-and-clustering.md)'s argument in
its strongest form: a threshold is a policy decision with consequences, taken
deliberately and documented, not a technical default. The number of people the
census counts depends on where that threshold sits, and the office says so.

## Colombia — Statistical Population Base Register (REBP) 2018

**Source.** Departamento Administrativo Nacional de Estadística, report on the
*Registro Estadístico Base de Población* 2018.

DANE built a statistical population register from administrative sources and
compared it against the 2018 census. The report is valuable for its candour about
its own limitations.

On method, in DANE's own words:

> The integration process was carried out deterministically, owing to the
> computational capacity available at the time. This implies that, as greater
> resources and processing capacity become available, it will be necessary to
> update the integration process by applying probabilistic methods; improving the
> quality of the match with respect to duplicates, the inclusion of new records,
> and the possibility of integrating other sources that **do not have the
> identity document variable**.

**What settles a question here.** Two things.

First, an office states publicly that its choice of method was driven by
**available compute**, not by methodological preference — and names what
probabilistic methods would add. That is the honest version of a constraint every
office faces, and it is a useful precedent for writing one's own limitations
section.

Second, the last clause is the argument of
[chapter 1.2](../01_why/limits-of-single-source.md) from a national register
project: probabilistic linkage is what lets you integrate the sources that
*lack* the identifier. Deterministic linkage on a document number can only ever
reach the population that has one.

The report also notes that access to the administrative registers was obtained
through direct requests to each data custodian, requiring additional work to
standardise and harmonise the datasets, and that this could be more efficient
through interoperability arrangements within the national statistical system.
That is barrier 2 of [chapter 4.1](../04_production/adoption-challenges.md) —
access negotiated source by source — reported by an office that lived it.

## Chile — Registro Social de Hogares

**Source.** Ministerio de Desarrollo Social y Familia, documentation of the
*Registro Social de Hogares*.

A contrasting case, and the reason it is included.

The RSH combines household-reported information with administrative records from
the State to classify households for social programme eligibility. Chile has a
universal national identifier, the RUN, and the system is built on it — so the
integration is largely **deterministic**. There is no Fellegi–Sunter model at the
centre of this system, and there does not need to be one.

**What settles a question here.** Probabilistic linkage is an answer to the
absence of a reliable unique identifier, not a goal in itself. Where the
identifier exists and is well populated, use it: a deterministic rule will
outperform anything in this toolkit and be far easier to explain.

But the system also has explicit provision for **foreign persons without a RUN**,
including their incorporation into the register. That detail is the whole of
[chapter 1.2](../01_why/limits-of-single-source.md)'s argument in one line: even a
universal identifier is not universal, and the population it misses is
systematic — here, the foreign-born. An identifier-based linkage that ignores that
group produces statistics that quietly exclude them.

This is also the case that motivates this toolkit's synthetic data, which
reproduces the Chilean situation of a national identifier alongside provisional
identifiers that cannot be compared across systems.

## What these four have in common

**Nobody relies on one method.** Every case that could combine deterministic and
probabilistic did.

**Quality targets are set before building, and translated into counts.**

**The uncertain middle is staffed, not thresholded away.** Where the stakes are
high enough, clerical review is a stage of the pipeline with people assigned to
it.

**Limitations are published.** DANE names its compute constraint and its access
problem; ONS documents that precision is estimated from a sample rather than
known. Neither weakens the work; both make it usable by someone else.

## Proposed: Statistics Norway, business register

Suggested by UNSD during the review of this toolkit's outline as an external
practice worth documenting: the use of record linkage in Statistics Norway's
business register.

It is named here as a placeholder rather than written up, because no primary
source for it has been located. **If you can point to published documentation of
this case, please [open an issue](https://github.com/NicolasLibuy/record-linkage-toolkit/issues).**

The alternative — describing a case from memory or from secondary summaries — is
exactly what the rest of this page avoids.

---

Contributions of further documented cases are welcome. See
[Country case examples](country-case-examples.md) for what qualifies and how to
submit one.
