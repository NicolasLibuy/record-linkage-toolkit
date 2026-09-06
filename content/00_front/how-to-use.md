# How to use this toolkit

## How the book is organised

The toolkit has seven sections, and they answer seven different questions.

| Section | The question it answers |
|:--|:--|
| **1. Why record linkage matters** | Why would a statistical office do this at all? |
| **2. Producing linked data** | How is it actually done, step by step? |
| **3. Communicating and assessing linkage quality** | How good is the result, and how do I say so? |
| **4. From pilot to production** | What has to be true for this to run every year? |
| **5. Country practices** | Who has done it, and what did they learn? |
| **6. Reference materials** | Where do I go to learn more? |
| **7. FAQ** | The questions that keep coming up. |

Section 2 is the longest by some distance, because it carries the method. Each
of its chapters pairs a narrative page with a notebook that performs that step
on the bundled data.

## Reading paths

**If you will build the pipeline**, read everything in order. Section 2 assumes
you are running the notebooks as you go, not only reading them.

**If you are responsible for the data rather than the code**, Sections 1, 3 and
4 are the ones written for you, plus chapters 2.1 (defining the use case), 2.3
(anonymisation) and 2.8 (evaluation). You can skip the Splink chapters and still
follow what the pipeline does and what its output means.

**If you are deciding whether to start a linkage project at all**, Section 1
makes the case and names the preconditions; Section 4 is a fair account of what
stops projects that have already started.

## Running the code

You need Python 3.10 or later. From a clone of the
[repository](https://github.com/NicolasLibuy/record-linkage-toolkit):

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

That installs pandas, Splink, DuckDB and the handful of other packages the
notebooks use. Splink runs on DuckDB in memory here, which needs no database
server and no configuration; at national scale it also runs on Spark and other
backends.

Then open any notebook and run it top to bottom. Each one is self-contained: it
reads the data in `data/`, does its work, and does not depend on you having run
a previous notebook first.

Notebooks in this book are published with their outputs already in place and are
not re-executed when the site is built. That is deliberate. Splink estimates one
of its parameters by random sampling that is not fixed by a seed, so the exact
figures move slightly between runs. The published numbers come from a specific
run, and if you re-run a notebook yourself you should expect small differences
in the last decimal places, and occasionally in a chosen threshold. This is a
real property of the method, and chapter 3.1 treats it as one.

## The data

Everything runs on two synthetic files, 30,000 and 27,000 records, with 4,500
people known to appear in both. They are raw and messy on purpose. Provenance,
schema and licence are in [`data/README.md`](../../data/README.md).

## Where this sits in the GSBPM

The [Generic Statistical Business Process Model v5.2](https://unece.github.io/GSBPM-5.2/),
endorsed by the Conference of European Statisticians, is the process framework
most statistical offices already use to describe their production. Record
linkage sits principally in sub-process **5.1 Integrate data**, but a linkage
project touches the model from the specification of needs through to evaluation.

| Toolkit section | GSBPM phase | Principal sub-processes |
|:--|:--|:--|
| 1. Why record linkage matters | 1. Specify needs | 1.1 Identify needs; 1.2 Consult and confirm needs; 1.3 Establish output objectives |
| 2. Producing linked data | 1, 2, 3 and 5 | 1.5 Check data availability and suitability; 2.2 Design variables; 2.5 Design processing and analysis; 3.2 Reuse or build processing and analysis components; **5.1 Integrate data**; 5.3 Review and validate; 5.5 Derive new variables and units; 5.8 Finalise data files |
| 3. Communicating and assessing linkage quality | 6, 7 and 8 | 6.2 Validate outputs; 6.3 Interpret and explain outputs; 6.4 Apply disclosure control; 7.5 Provide user support; 8.1 Gather evaluation inputs; 8.2 Conduct evaluation |
| 4. From pilot to production | 2, 3 and 8 | 2.6 Design production systems and workflows; 3.4 Configure workflows; 3.7 Finalise production systems; 8.3 Agree an action plan |
| 5. Country practices | End to end | Complete applications of the process in national settings |
| 6. Reference materials | Over-arching | Supports quality, metadata and methods management across phases |
| 7. FAQ | Over-arching | Supports quality, metadata and methods management across phases |

## Contributing and reporting problems

Corrections, questions and country cases are welcome. Open an issue in the
[repository](https://github.com/NicolasLibuy/record-linkage-toolkit/issues), or
see [Contributors](contributors.md) for how country practices are added.
