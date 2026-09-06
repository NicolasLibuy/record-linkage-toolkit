# Why linkage projects stall

Section 2 ends with a working pipeline. That is not the same thing as a
statistical output, and the distance between them is where most linkage projects
stop.

The distance is rarely technical. A team that has worked through Section 2 can
build a model. What it usually cannot do on its own is get lawful access to the
data, secure a commitment that the pipeline will run again next year, or obtain
agreement on what "good enough" means. Those are institutional problems, and they
have institutional answers.

This chapter names the barriers. [Chapter 4.2](enabling-environment.md) is about
what has to be in place to remove them.

## An observation worth stating plainly

Across the eight national teams in the training programme this toolkit came out
of, every team was able to design a linkage. Not one was blocked by the
methodology. The projects that advanced furthest were those with a legal basis
already settled and data already in hand; the projects that stalled, stalled on
access, authorisation and ownership.

That pattern is not specific to those countries, and it is the reason this
section exists at all. **If you are choosing what to work on next, the return on
an afternoon spent on the data-sharing agreement is almost always higher than the
return on an afternoon spent on the model.**

## Six barriers

### 1. There is no legal basis, or the one you have does not cover linkage

The most common blocker, and the one most often discovered late.

**The symptom.** The office has a statistical mandate to *receive* a register,
and has been receiving it for years. When linkage is proposed, it turns out the
mandate covers processing that source for its own purposes and says nothing about
combining it with another.

**Why it stops things.** Nobody will authorise a linkage on an ambiguous basis,
and clarifying it can require anything from a legal opinion to an amendment.

**What unblocks it.** Establish the basis for *linkage specifically*, in writing,
before designing anything. Where the statistical law is general, a documented
legal opinion may be enough; where it is silent, this is a conversation to start
early because it is long. Note that the basis to *link* and the basis to
*disseminate the linked result* are separate questions, and the second is often
harder.

### 2. Access is negotiated project by project

**The symptom.** Each new linkage restarts the negotiation with each data
controller. A pilot takes eight months, of which six are correspondence.

**Why it stops things.** The transaction cost falls entirely on the second and
third projects, which never happen. Linkage looks expensive because the first one
was.

**What unblocks it.** A standing data-sharing agreement covering a class of uses,
rather than a bespoke one per project. This is slower to obtain the first time and
transforms everything afterwards.

### 3. The pilot ran on an extract nobody can reproduce

**The symptom.** The linkage worked, on a file someone exported once, with no
recorded query, date or version. Re-running it produces different results and
nobody can say why.

**Why it stops things.** A result that cannot be reproduced cannot be published,
and cannot be defended when questioned.

**What unblocks it.** The reproducibility checklist in
[chapter 3.1](../03_quality/communicating-methods.md) — in particular identifying
the extract, pinning the environment, and versioning the trained model. This is
cheap to do at the start and expensive to retrofit.

### 4. One person holds the capability

**The symptom.** One member of staff learned Python and Splink and built the
pipeline. Nobody else can run it, and the documentation is the notebook.

**Why it stops things.** The linkage has a single point of failure, and a manager
who understands that will be reluctant to build a published statistic on it.

**What unblocks it.** At least two people who can run and modify the pipeline;
code in a shared repository rather than on a laptop; a written runbook that
someone unfamiliar can follow. Where capability is genuinely scarce, the
open-source courses in Reference Materials
are a cheaper route than external consultancy, because they leave the capability
inside the office.

### 5. Nobody owns the pipeline

**The symptom.** The pilot was a project with an end date. When it ended, the
pipeline had no owner, no budget line and no place in any production calendar.

**Why it stops things.** Silently. Nothing breaks; the linkage simply is not run
again, and eighteen months later the code no longer runs against the current
data.

**What unblocks it.** Naming an owning team before the pilot finishes, and
placing the re-run in the production calendar with the outputs that depend on it.

### 6. There is no agreed quality bar

**The symptom.** The model works, and nobody can say whether it is good enough,
because "good enough" was never defined. The project drifts, waiting for a level
of confidence that has no threshold.

**Why it stops things.** Without a stated target, every result invites the
question "could it be better?", and the answer is always yes.

**What unblocks it.** Set the quality objectives **before** building, as the UK's
Office for National Statistics does: its
[data linkage and matching policy](https://www.ons.gov.uk/aboutus/transparencyandgovernance/datastrategy/datapolicies/datalinkageandmatchingpolicy)
states that "the required quality standard should be determined for each data
linkage project", anticipates that different projects will have different success
criteria, and notes that this "may be a key determinant of the most appropriate
matching methodology".

That ordering matters. The quality target chooses the method, not the other way
round.

## The two things that are genuinely technical

Not everything is institutional. Two technical constraints do stop projects, and
both are worth recognising early because they change what is worth attempting.

**The identifying variables are too weak.** Where the only comparable fields are
few, common and error-prone, there is a ceiling on what any method can achieve.
Section 2's example — five fields, no date of birth, heavily corrupted names —
reaches around 30% recall at high precision, and no amount of modelling moves
that much. If your sources are in that position, the honest answer may be to
improve the collection of identifying data rather than to build a better model.

**The volumes exceed the infrastructure.** Blocking makes national-scale linkage
possible, but the result still has to be computed and stored somewhere. An office
without a machine that can hold the candidate pairs will need infrastructure
before it needs methodology.

## A readiness check

Before committing to a linkage project, answer these. A "no" is not a reason to
stop, but it is a task that belongs on the plan alongside the technical work.

- Is there a legal basis for **linking** these sources, in writing?
- Is there a legal basis for **disseminating** what the linkage produces?
- Is there an agreement covering access, or does one have to be negotiated?
- Can you name the extract you will use — source, query, date, version?
- Are there at least two people who can run the pipeline?
- Is there a named owner for it after the pilot ends?
- Is there a stated quality target, and does the use case justify it?
- Do you know roughly what recall the available identifying variables permit?
- Does the infrastructure hold the candidate pairs at full scale?

Offices that get through a first linkage successfully tend to have addressed the
first three of these before writing any code, and to have accepted that doing so
takes longer than the analysis.

[Chapter 4.2](enabling-environment.md) sets out what a settled enabling
environment looks like, using the arrangements that statistical offices have
actually published.
