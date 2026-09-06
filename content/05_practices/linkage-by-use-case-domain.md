# Linkage by domain

Record linkage looks different depending on what is being linked. The method is
the same; the identifiers available, the failure modes, and what an error costs
are not.

This chapter surveys the domains where statistical offices most often link, and
what to expect in each. Use it to locate your own case and to anticipate the
problems before they arrive.

## Civil registration and vital statistics

**Typical linkage.** Birth and death records to a population register, to health
records, or to each other. Very often the purpose is measuring the *completeness*
of registration rather than producing a linked dataset.

**Identifiers.** Usually good: names recorded carefully, dates of birth and death
precise, and often a national identifier. Civil registration is one of the few
administrative processes whose whole purpose is identity, so its data quality is
generally better than average.

**Failure modes.** Registration delay creates apparent non-matches that are
really timing differences. Infant deaths are hard: the child may have no
established name, no identifier and only a date. Names change on marriage in some
systems and not others.

**What an error costs.** A missed match understates registration completeness —
which is the number you were trying to estimate, so the error goes straight into
the headline. This is a case where recall matters more than usual and where the
blocking ceiling deserves careful attention.

## Health

**Typical linkage.** Hospital admissions, primary care, disease registries,
insurance or coverage files, deaths. Longitudinal follow-up is common: the same
person across years and facilities.

**Identifiers.** Highly variable. A national health number where one exists makes
this nearly deterministic. Where none exists, health records are often
name-and-date-of-birth only, with names entered under time pressure.

**Failure modes.** Event-based files — one row per admission, not per patient —
so deduplication comes first, and getting it wrong changes every count. Names
recorded by different staff in different systems diverge quickly. Deaths in one
system and not another create apparent survivors.

**What an error costs.** A false match merges two people's clinical histories,
which is both a statistical error and, in some uses, a safety issue. Precision
usually dominates.

## Population and household registers

**Typical linkage.** Building or maintaining a statistical population register
from several administrative sources; deduplicating within it; linking a census to
it.

**Identifiers.** The best case if a national identifier is well populated. The
hard part is rarely the linkage and usually the *rules*: who counts as resident,
at what date, and which source wins when two disagree.

**Failure modes.** Over-coverage — people in the register who have left or died —
and under-coverage of people who interact with no administrative system.
Addresses change constantly. Deduplication errors propagate into everything
downstream.

**What an error costs.** Both directions matter, because the register is an input
to everything else. Uruguay's and Colombia's cases in
[chapter 5.3](other-practices.md) are both of this kind.

## Migration

**Typical linkage.** Entry and exit records to each other and to residence,
employment or health registers, to estimate stocks and durations of stay.

**Identifiers.** Often the weakest of any domain, and the most systematically so.
Names are transliterated, and transliterated differently by different systems.
Name order varies by convention. Documents change. People without regular status
avoid registration.

**Failure modes.** Transliteration is the dominant one, and it defeats exact
matching almost completely — this is where phonetic encoding and string
similarity earn their place. Entry without a matching exit may mean the person
stayed, or that the exit was recorded badly.

**What an error costs.** Differential linkage error is nearly guaranteed here:
whichever nationalities transliterate least well are linked least well, and those
are precisely the groups migration statistics are about. Measure linkage rate by
nationality and by name origin, and publish it.

## Social protection

**Typical linkage.** Benefit or programme records to each other, to income data,
or to a household register, to measure coverage, overlap, or targeting accuracy.

**Identifiers.** Usually reasonable, because entitlement requires identification.
But a national identifier is often *conditional* on status, so exactly the people
of most interest may lack one.

**Failure modes.** The unit is often the household, not the person, and household
composition changes. Provisional identifiers issued to people whose registration
is incomplete cannot be compared across systems — the situation this toolkit's
synthetic data reproduces.

**What an error costs.** This is the domain where a false match can directly harm
an individual, if the linkage informs entitlement rather than statistics. Where
that is the case, precision should dominate and a clerical review stage is
usually warranted. Chile's Registro Social de Hogares
([chapter 5.3](other-practices.md)) is an operational system of this kind.

## Business and economic registers

**Typical linkage.** A statistical business register to tax, social security or
customs records; or two business registers to each other.

**Identifiers.** A tax identifier is usually available and usually good, so
deterministic linkage often suffices for the units that have one.

**Failure modes.** The entity itself is unstable in a way people are not.
Businesses merge, split, change name, change legal form and change activity, and
whether the result is "the same" business is a definitional question before it is
a matching one. Names are far less distinctive than personal names, and shared
addresses are common.

**What an error costs.** Economic aggregates are dominated by large units, so an
error affecting one large enterprise can move a published statistic more than
thousands of errors among small ones. Consider evaluating quality *weighted by
size*, not only by count.

## Education and labour

**Typical linkage.** Enrolment and attainment records across levels; education to
employment or earnings, for transition and outcome statistics.

**Identifiers.** Education systems often have their own good identifiers within a
level and no continuity across levels or providers. That discontinuity is usually
the problem.

**Failure modes.** Names change; young people's records are thin; private and
public providers keep separate systems. Long follow-up periods compound every
error.

**What an error costs.** Missed matches look like people leaving the system, which
in outcome statistics is exactly the signal being measured. A linkage that loses
people will report dropout that did not happen.

## Across all domains

Three patterns recur.

**Event-based files need a decision first.** If a person can appear many times,
decide before linking whether to deduplicate or to link at the event level. A
count of matches is not a count of people.

**The identifier that exists is rarely universal.** The interesting cases are
those where an identifier covers most of the population but not all, and where
its absence is systematic — the foreign-born, the recently arrived, the
informally employed, the very young. Chapter 5.3's Chilean case is explicit about
this: the system has a universal identifier and dedicated handling for people
without one.

**The error you can least afford follows from the domain, not the data.** Health
and social protection lean towards precision; registration completeness and
migration statistics lean towards recall. Decide it in
[chapter 2.1](../02_producing/defining-the-use-case.md) and let it choose your
threshold in [chapter 2.7](../02_producing/thresholds-and-clustering.md).
