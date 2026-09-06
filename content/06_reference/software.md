# Software

This toolkit is built on Python and Splink, and says so on the cover. That is a
deliberate narrowing: committing to one stack is what lets it show a complete
working pipeline rather than a survey. But the *methods* transfer, and an office
that cannot use Python has other options.

## What this toolkit uses

| Package | Role | Version pinned here |
|:--|:--|:--|
| [Splink](https://moj-analytical-services.github.io/splink/) | Probabilistic linkage: settings, blocking, comparisons, EM estimation, clustering, evaluation | `>=4.0.17,<5` |
| [DuckDB](https://duckdb.org/) | The SQL engine Splink executes against | `>=1.0` |
| [pandas](https://pandas.pydata.org/) | Data preparation | `>=2.0` |
| [phonetics](https://pypi.org/project/phonetics/) | Double Metaphone encoding | `>=1.0` |
| [recordlinkage](https://recordlinkage.readthedocs.io/) | Sorted neighbourhood indexing, in chapter 2.5 | `>=0.16` |
| matplotlib, altair, vl-convert-python | Charts | — |

```{warning}
Pin `splink>=4.0.17`. Version 4.0.0 declares only `sqlglot>=13.0.0`, and a clean
install today picks up a `sqlglot` release that breaks EM training entirely with
`ValueError: Expected sql condition to refer to one column but got []`. Testing
across versions places the boundary precisely: splink 4.0.0 works with sqlglot
25.9.0 and fails with 30.18.0; splink 4.0.17 works with both.
```

### Splink

Developed and maintained by the UK Ministry of Justice, released under the MIT
licence. It implements the Fellegi–Sunter framework with the parts that make it
usable at national scale: blocking analysis, comparison libraries with graded
levels, term-frequency adjustments, EM estimation, connected-components
clustering and a set of diagnostics.

Its distinguishing feature is that it **compiles to SQL**. The same model
definition runs on DuckDB on a laptop and on Spark or Athena on a cluster, which
means the pipeline you prototype is the pipeline you deploy.

- [Documentation](https://moj-analytical-services.github.io/splink/)
- [Seven-part tutorial](https://moj-analytical-services.github.io/splink/demos/tutorials/00_Tutorial_Introduction.html)
- [Topic guides](https://moj-analytical-services.github.io/splink/topic_guides/topic_guides_index.html) — record linkage theory, model design, data preparation, blocking

### DuckDB

An in-process analytical database. No server, no configuration, and it reads
CSV and Parquet directly. It is what makes it reasonable to work through this
entire toolkit on a laptop.

For larger work Splink also supports Spark, Athena, PostgreSQL and SQLite. The
switch is one line — the `db_api` argument — and nothing else in the model
definition changes.

### recordlinkage

A pure-Python library predating Splink, with a scikit-learn-flavoured API. This
toolkit uses it in [chapter 2.5](../02_producing/blocking.md) for sorted
neighbourhood indexing, which Splink does not provide.

It is a reasonable choice for smaller projects and for exploratory work, and its
indexing module is worth knowing about independently of the rest.

## Alternatives, if Python is not available

### RELAIS (Istat)

[RELAIS](https://www.istat.it/en/classifications-and-tools/methods-and-software-of-the-statistical-process/process-phase/data-integration/relais/)
— *REcord Linkage At IStat* — is an open-source toolkit developed by Italy's
national statistical institute, released under the European Union Public Licence.

Two things make it worth serious consideration for a statistical office:

- it has a **graphical interface**, so a linkage workflow can be built without
  writing code — the single biggest obstacle for offices without programming
  capacity;
- it was **built by a national statistical office for national statistical
  work**, so its vocabulary and its workflow assumptions match official
  statistical production rather than software engineering.

It is built around composing a workflow from techniques chosen per phase, which
maps closely onto the structure of [Section 2](../02_producing/defining-the-use-case.md)
of this toolkit. An office that follows Section 2's *reasoning* and implements it
in RELAIS is doing the same thing by other means.

### fastLink (R)

[fastLink](https://github.com/kosukeimai/fastLink) implements the Fellegi–Sunter
model with EM estimation in R, handling missing data explicitly and allowing
auxiliary information. Based on Enamorado, Fifield and Imai (2019).

The natural choice for an office whose analytical capacity is in R rather than
Python. It is a library rather than a platform: expect to do more of the
surrounding work yourself than Splink requires.

Also in R: `RecordLinkage`, and `reclin2` for a more pipeline-oriented approach.

### PySpark functions (ONS)

The UK Office for National Statistics published seven reusable PySpark functions
for linkage diagnostics, candidate-pair generation and m/u estimation, each
documented with its parameters and outputs, in
[*Developing standard tools for data linkage*](https://www.ons.gov.uk/methodology/methodologicalpublications/generalmethodology/onsworkingpaperseries/developingstandardtoolsfordatalinkagefebruary2021)
(February 2021).

Relevant to offices already working in a Spark environment, and useful reading
regardless: the paper documents the design decisions behind each function, which
is a rare thing to be able to read.

## Choosing

| If your office… | Consider |
|:--|:--|
| Has Python capacity and wants scale | **Splink** |
| Has R capacity | **fastLink** |
| Has no programming capacity | **RELAIS** |
| Already runs Spark | **Splink on Spark**, or the ONS PySpark functions |
| Is doing a small one-off exploration | **recordlinkage**, or Splink on DuckDB |

The methodological content of this toolkit — how to define a use case, assess
readiness, design blocking rules, interpret *m* and *u*, choose a threshold,
evaluate the result — applies whichever you pick. Only the code changes.

## What is not covered here

**Privacy-preserving record linkage (PPRL)** encodes identifiers into structures,
commonly Bloom filters, permitting approximate comparison without revealing the
underlying values. It recovers some of the error tolerance that plain hashing
destroys ([chapter 2.3](../02_producing/anonymisation.md)), at the cost of a more
complex protocol and some residual attack surface. Relevant where identifiers
cannot be shared even in pseudonymised form.

**Machine-learning approaches** to entity resolution — supervised classifiers,
and more recently transformer-based methods — can outperform Fellegi–Sunter where
substantial labelled training data exists. That condition is the difficulty: most
statistical offices have no labels, which is precisely why the unsupervised EM
approach in this toolkit is the standard one.
