# The demand for linked data

Statistical offices are increasingly asked questions that no single source can
answer.

How many children who spent time in social care go on to complete secondary
school? Are the households receiving a housing subsidy the ones the programme
was designed for? How many people who entered the country last year are still
here, and are they working? Do the deaths recorded by the civil registry match
the deaths recorded by the health system, and if not, which is incomplete?

Each of these questions is about the same person appearing in two places. The
information exists — it is simply held in two systems, built by two
institutions, for two purposes, with no shared identifier between them. Record
linkage is how a statistical office recovers the connection.

## What linkage is used for

There are four distinct reasons an office links data, and they call for
different amounts of care.

**To combine information that is not recorded in one place.** The most common
case. One source holds the exposure and another holds the outcome; neither
holds both. Linking them creates a dataset that answers a question neither
source could.

**To assess and improve data quality.** Two sources that cover the same
population are a check on each other. If a civil register records 40% fewer
deaths than the health system, linking them tells you where the gap is, and
which records are missing from which system. Linkage used this way produces a
coverage estimate, not a new dataset.

**To enrich an existing dataset.** A survey with 4,000 respondents and rich
detail can be linked to an administrative register to add variables that would
have been expensive or unreliable to ask about, or to follow respondents over
time without re-contacting them.

**To answer new questions without new collection.** The most economically
significant reason. A linked dataset built once can support many analyses that
would each otherwise have required a survey.

## Why this matters for the 2030 Agenda

The commitment to leave no one behind is, statistically, a commitment to
disaggregation. Indicators must be reported not only nationally but by sex, age,
income, disability status, migratory status and geography, and reported often
enough to act on.

That is difficult to satisfy from surveys alone. A national sample designed to
produce a reliable national estimate is usually too small to produce reliable
estimates for a small subgroup in a small area, and running it more often than
annually is rarely affordable. Administrative sources have the opposite profile:
they cover the whole population and are updated continuously, but each covers
only its own domain, and none was designed to measure an SDG indicator.

Linkage is what lets an office use the coverage and frequency of administrative
sources while retaining the definitional precision of a statistical one. It is
one of the methods the United Nations Statistics Division supports under the
[Data for Now](https://unstats.un.org/capacity-development/data-for-now) initiative for
exactly this reason: it makes better, more timely, more disaggregated statistics
possible from data that a country already holds.

It is also, in most offices, cheaper than the alternative. The marginal cost of
answering a new question from linked administrative data is analytical time. The
marginal cost of answering it from a new survey is a survey.

## Three systems that already do this

None of what follows is experimental. Offices and agencies have been running
linked-data infrastructure for years, at national scale, in production.

### ECHILD — England

[ECHILD](https://www.echild.ac.uk/) (Education and Child Health Insights from
Linked Data) links health, education and social care records for children in
England.

That combination makes it possible to study questions that had previously been
split across institutional boundaries: how health conditions affect school
attainment, how special educational needs relate to hospital contact, how
children known to social care fare educationally. Access is through a secure
environment, restricted to accredited researchers working on approved
public-benefit projects, under the Five Safes framework.

The point worth noticing is that no new data was collected. The records already
existed in the health system and the education system. What ECHILD added was the
link between them.

### The Integrated Data Infrastructure — New Zealand

Stats NZ's [Integrated Data Infrastructure](https://www.stats.govt.nz/integrated-data/)
is a large research database that brings together administrative data from
across government — education, health, benefits and social services, migration,
justice — together with census and survey data, about people and households.

The IDI is closer to a permanent piece of statistical infrastructure than to a
project. It is maintained, updated and documented as an asset in its own right,
and it supports many analyses rather than one. It has been used for work on child
and youth wellbeing, on identifying need earlier, and on how contact with one
public service predicts contact with another.

### The Registro Social de Hogares — Chile

Chile's [Registro Social de Hogares](https://www.registrosocial.gob.cl/),
maintained by the Ministry of Social Development and Family, combines
information reported by households with administrative records held by the
State: income, household composition, education, health, disability and
pensions.

Its purpose is operational rather than analytical. It classifies households by
socioeconomic circumstances and is used to determine eligibility for subsidies
and social programmes. It is a useful case precisely because the stakes are
concrete: a linkage error here is not a slightly wrong estimate, it is a
household that does or does not receive a benefit.

## What these three have in common

They differ in purpose — research, statistical infrastructure, programme
administration — but they share a structure. Each takes records that institutions
generated for their own operational reasons and connects them at the level of the
individual person or household. Each produces something none of its inputs
contained. And each had to solve, first, the problem this toolkit is about:
deciding which record in one system refers to the same person as a record in
another.

Where a country has a reliable universal identifier, that problem is small. Where
it does not, it is the whole job. The
[next chapter](limits-of-single-source.md) looks at why the sources you already
hold cannot answer these questions on their own, and what changes when you have
a usable identifier and when you do not.
