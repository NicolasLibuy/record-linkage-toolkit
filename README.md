# Deterministic and Probabilistic Record Linkage

**A toolkit for the production of official statistics**

Many national statistical offices hold two or more administrative or statistical
sources that describe the same people, and no reliable identifier that connects
them. This toolkit is about closing that gap: how to decide whether your sources
can be linked, how to link them, how to tell whether the result is good enough
to publish, and how to document it so that someone else can defend and repeat it.

It is written for the people who would actually do the work — methodologists,
subject-matter statisticians, and data processing and IT staff. It assumes no
previous experience of probabilistic linkage and only working familiarity with
Python. Every method it describes comes with code that runs, and a synthetic
dataset to run it on.

---

## Background and purpose

Between May and August 2026 the United Nations Statistics Division delivered a
13-session training on deterministic and probabilistic record linkage under the
[Data for Now](https://unstats.un.org/capacity-development/data-for-now) initiative, to 26
participants from eight countries: Bhutan, the Dominican Republic, Jamaica,
Kenya, Namibia, Rwanda, Somalia and the State of Palestine. It covered Python
data management, anonymisation, deterministic linkage, the Fellegi–Sunter
framework, blocking at scale, and a full implementation pipeline in Splink. Each
country team carried a linkage project of its own through the course.

That produced a large body of tested material — lectures, notebooks, working
code and national case studies — whose usefulness plainly did not stop at the 26
people in the room. This toolkit is that material rewritten as a public
resource: not the course, but the guidance the course was built on, made
standalone so that any office can work through it without having attended
anything.

## What this toolkit adds

The *Data Linking Toolkit* published by UN Women, UNECA and UNSD in 2025 already
surveys five linkage methodologies and pairs each with a case study and R-based
guidance. This toolkit is deliberately narrower and deeper, and is meant to
complement that resource rather than duplicate it. What it adds:

- **Depth on one family of methods rather than a survey of five.**
  Deterministic and probabilistic (Fellegi–Sunter) linkage are worked through in
  full, from data preparation to the clustering of predictions, at the level of
  detail an office needs to *implement* them rather than to appraise them.
- **A complete, runnable implementation.** Open-source Python and Splink code
  sits in each chapter where it applies, so the guidance can be executed,
  adapted and re-run, not only read.
- **The production end of the pipeline.** Blocking at scale, parameter
  estimation, threshold selection, clustering, linkage error and bias, quality
  evaluation and reproducible documentation are the steps that decide whether a
  pilot becomes a statistical output, and each is covered explicitly.
- **Learning without data access.** A synthetic dataset with a known answer
  ships with the toolkit, so the whole pipeline, including its error rates, can
  be run and evaluated before touching national data.
- **Country practices drawn from implementation.** The country cases it
  publishes are those where linkage is actually in production or in use for
  official statistics, contributed by the offices that built them.
- **Alignment with GSBPM v5.2**, so the guidance can be located inside the
  process framework an office already uses.

In short: if the question is *which* kind of linkage suits a problem, start with
the Data Linking Toolkit; if the decision is made and individual record linkage
has to be implemented, this toolkit takes it from there. The relationship is set
out in full under [Related toolkits](content/06_reference/related-toolkits.md).

## What to expect

By the end of this toolkit you should be able to:

- judge whether two sources are ready to be linked, and what to fix first;
- prepare and pseudonymise identifying data so it can be handled safely;
- choose between a deterministic and a probabilistic approach for your case, and
  know why;
- design blocking rules that make the comparison feasible without silently
  discarding the matches you are looking for;
- build, train and evaluate a probabilistic model in Splink;
- choose a threshold, cluster the results and produce a linked dataset;
- measure and communicate the quality of that dataset, including its errors; and
- document the whole thing so it can be reproduced.

Each step has a chapter of narrative and a notebook that performs it on the
[bundled synthetic data](data/README.md), so you can run every result you read
about before applying any of it to your own records.

## What not to expect

- **Not a literature review.** The methods are covered to the depth needed to
  implement them, not to survey the field. Pointers for going further are in
  Reference Materials.
- **Not a survey of every linkage method.** Deterministic and probabilistic
  (Fellegi–Sunter) linkage are covered in full. Machine-learning approaches
  and privacy-preserving linkage protocols are named where relevant but not
  developed here. Other kinds of linkage, such as aligning a census with its
  post-enumeration survey or linking people to service locations, are covered
  by the companion *Data Linking Toolkit* (see below).
- **Not legal advice.** Whether you may link two registers, and under what
  authority, is a question for your own legal framework. The toolkit says what
  usually has to be in place; it cannot tell you whether it is.
- **Not a substitute for knowing your data.** No amount of method compensates
  for not understanding how your source systems record a name.

## How to approach this toolkit

A few things worth knowing before you start.

**It is Python- and Splink-centric, and that is a choice.** The implementation
chapters use [Splink](https://moj-analytical-services.github.io/splink/), an
open-source library that is free, runs at national scale, and is used in
production by government agencies, including the UK's Office for National
Statistics (see [Splink's list of users](https://moj-analytical-services.github.io/splink/#use-cases)). Committing to one tool is
what lets the toolkit show a complete working pipeline instead of a sketch. If
your office cannot use Python, the *methods* still transfer, and alternative
software is listed under Reference Materials.

**Read it in order the first time, then use it as a reference.** The chapters
build on each other, and the running example carries through all of them. Once
you have been through it, each chapter stands on its own.

**The examples run on synthetic data with a known answer.** The bundled dataset
knows which records belong to the same person, so you can see not just what a
model predicts but how often it is right. Your own data will not come with that
answer, which is precisely why the evaluation chapters matter.

**It is published in English for now.**

**It is a living resource.** Country Practices and the FAQ grow over time, and
contributions from offices beyond the original eight countries are welcome. See
[Contributors](content/00_front/contributors.md).

---

## Where the toolkit sits in the statistical process

Record linkage sits principally in sub-process **5.1 Integrate data** of the
[GSBPM v5.2](https://unece.github.io/GSBPM-5.2/), but a linkage project reaches
from the specification of needs through to evaluation. The mapping from each
section of this toolkit to the GSBPM phases it touches is in
[How to use this toolkit](content/00_front/how-to-use.md).

## Citing and reusing

Narrative and data are released under CC BY 4.0; code under the MIT licence. See
[`LICENSE`](https://github.com/NicolasLibuy/record-linkage-toolkit/blob/main/LICENSE)
and [`CITATION.cff`](https://github.com/NicolasLibuy/record-linkage-toolkit/blob/main/CITATION.cff).

Prepared by the United Nations Statistics Division under the Data for Now
initiative, with Nicolas Libuy as lead drafter.
