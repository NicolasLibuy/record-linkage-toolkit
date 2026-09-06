# Key readings

A short list. Each entry says what it is for, so you can tell whether it is worth
your time before opening it.

## The foundation

**Fellegi, I. P. and Sunter, A. B. (1969).** *A Theory for Record Linkage.*
Journal of the American Statistical Association, 64(328), 1183–1210.
[doi:10.1080/01621459.1969.10501049](https://doi.org/10.1080/01621459.1969.10501049)

The paper the whole field rests on. It defines *m* and *u*, derives the
likelihood-ratio decision rule, and proves it optimal in a specific sense.

Worth reading once, for the framing rather than the algebra: everything since has
been about making it computable at scale. If you only read one section, read the
statement of the decision problem — three outcomes, link, non-link and *possible
link*, the third of which is what modern practice calls clerical review.

**Linacre, R., Lindsay, S., Manassis, T., Slade, Z., Hepworth, T., Kennedy, R.
and Bond, A. (2022).** *Splink: Free software for probabilistic record linkage at
scale.* International Journal of Population Data Science, 7(3).
[doi:10.23889/ijpds.v7i3.1794](https://doi.org/10.23889/ijpds.v7i3.1794)

The paper describing the software this toolkit uses. Short, and useful for
understanding the design choices — particularly why compiling to SQL matters, and
how the term-frequency adjustments work.

## Reference books

**Christen, P. (2012).** *Data Matching: Concepts and Techniques for Record
Linkage, Entity Resolution, and Duplicate Detection.* Springer.
[doi:10.1007/978-3-642-31164-2](https://doi.org/10.1007/978-3-642-31164-2)

The standard textbook, and the one to own if you own one. Structured much like
Section 2 of this toolkit — pre-processing, indexing, comparison, classification,
evaluation — with considerably more depth on each. Its treatment of **indexing**
(what this toolkit calls blocking) is the best available, covering methods well
beyond the equality rules and sorted neighbourhood shown here.

**Herzog, T. N., Scheuren, F. J. and Winkler, W. E. (2007).** *Data Quality and
Record Linkage Techniques.* Springer.

Written from inside official statistics, by authors from the US statistical
system. Stronger than Christen on the relationship between **data quality** and
linkage, and on the institutional context. The chapters on data preparation
repay reading before you design a cleaning pipeline.

**Harron, K., Goldstein, H. and Dibben, C. (eds, 2015).** *Methodological
Developments in Data Linkage.* Wiley.

An edited collection, uneven as they all are, but the chapters on **linkage error
and its effect on analysis** are the best treatment of the question this toolkit
raises in [chapter 2.8](../02_producing/evaluation.md) and cannot fully answer:
what to do about linkage error once you have measured it.

## On error and bias

**Harron, K., Dibben, C., Boyd, J., Hjern, A., Azimaee, M., Barreto, M. L. and
Goldstein, H. (2017).** *Challenges in administrative data linkage for research.*
Big Data & Society, 4(2).
[doi:10.1177/2053951717745678](https://doi.org/10.1177/2053951717745678)

Open access, and the single most useful paper for someone about to publish from
linked data. It covers the practical and institutional obstacles as well as the
methodological ones, and is direct about **differential linkage error** — the
finding this toolkit reproduces in chapter 2.8, where recall varies by a factor
of two and a half with the length of a name.

If you read one thing on this list beyond the Fellegi–Sunter paper, read this.

**Randall, S. M., Ferrante, A. M., Boyd, J. H. and Semmens, J. B. (2013).** *The
effect of data cleaning on record linkage quality.* BMC Medical Informatics and
Decision Making, 13:64.
[doi:10.1186/1472-6947-13-64](https://doi.org/10.1186/1472-6947-13-64)

Short, empirical, and directly useful. It measures the standardisation trade-off
that [chapter 2.2](../02_producing/data-readiness.md) describes: cleaning more
finds more true matches and creates more false ones, and the right amount depends
on the error rate in your data. Read it before deciding how aggressively to
standardise.

## Method papers worth knowing

**Enamorado, T., Fifield, B. and Imai, K. (2019).** *Using a Probabilistic Model
to Assist Merging of Large-Scale Administrative Records.* American Political
Science Review.

The paper behind `fastLink`. Its treatment of **missing data** within the
Fellegi–Sunter framework is more explicit than most, and worth reading even if
you work in Python.

**Office for National Statistics (2021).** [*Developing standard tools for data
linkage.*](https://www.ons.gov.uk/methodology/methodologicalpublications/generalmethodology/onsworkingpaperseries/developingstandardtoolsfordatalinkagefebruary2021)

A working paper pairing linkage theory with seven documented PySpark functions.
Valuable less for the code than for seeing a national statistical office reason
in public about how to standardise linkage work across an organisation.

## Policy and practice, not method

Worth reading alongside the methodological literature, because
[Section 4](../04_production/adoption-challenges.md) is where projects actually
fail.

**Office for National Statistics.** [*Data linkage and matching
policy.*](https://www.ons.gov.uk/aboutus/transparencyandgovernance/datastrategy/datapolicies/datalinkageandmatchingpolicy)

Two pages, and the most useful two pages on this list for anyone trying to
institutionalise linkage. It sets out a governance structure, a purpose test, and
explicit quality rules — precision and recall must always be reported, and match
rates must not be used as a quality metric.

**Instituto Nacional de Estadísticas (Chile) (2024).** *Guide to quality
indicators for administrative registers*, v1.0.

Nineteen indicators for administrative register quality, two of them about
linkage directly. Useful as a model for measuring register quality as a routine
property rather than per project. In Spanish.

## A note on what is missing

This list is deliberately short and skewed towards official statistics. It does
not cover the substantial computer-science literature on entity resolution,
scalable indexing or machine-learning approaches, for which Christen's
bibliography is the right starting point.

Nor does it cover privacy-preserving record linkage in any depth. If you need
that, start from Christen's chapter on privacy and work forward through the
citations.
