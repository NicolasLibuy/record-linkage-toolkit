# Record linkage as a solution

## What record linkage is

A working definition:

> Record linkage brings together information from two or more data sources in
> order to consolidate facts about a person or an event that are not available
> in any single record.

The important part is the condition under which it becomes necessary. Linkage is
what you do **in the absence of an error-free key** that identifies each entity
in each file. If such a key exists and is complete, the data are already
effectively linked and a database join is all you need.

So it is worth stating plainly: **record linkage is the process of creating that
key.** Everything in this toolkit — cleaning names, designing blocking rules,
estimating match weights, choosing a threshold — is machinery for constructing a
usable identifier where none was recorded.

## Two families of method

There are two ways to decide whether two records describe the same person, and
this toolkit covers both.

**Deterministic linkage** applies rules. Two records are a match if they agree
on a specified set of fields — exactly, or after some agreed relaxation. A rule
might require agreement on given name, first surname, sex and nationality, and
tolerate a missing second surname but not a conflicting one. Rules can be
stacked in stages, from strict to permissive.

**Probabilistic linkage** scores. Rather than deciding in advance which
agreements count, it asks, for each field, how much evidence agreement provides:
how often do genuine matches agree on this field, and how often do non-matches
agree on it by chance? Those two quantities combine into a weight, the weights
combine into a score for the pair, and you choose a threshold on that score. This
is the Fellegi–Sunter framework, and it is the core of Section 2.

Neither is universally better.

| | Deterministic | Probabilistic |
|:--|:--|:--|
| Best when | Identifiers are good and errors are rare | Identifiers are imperfect and errors are common |
| Decision | A record pair passes the rule or it does not | A record pair receives a score |
| Handles partial agreement | Only if you write a rule for it | Naturally, and weights it by how informative it is |
| Rare vs common values | Treats "Smith" and an unusual surname alike | Can weight a rare surname more than a common one |
| Explaining it | Straightforward: here is the rule | Harder: requires explaining the model |
| Tuning | Add or relax rules by hand | Set a threshold, with the trade-off made explicit |
| Effort to build | Low | Higher |

In practice most offices use both. A deterministic rule on the cases that have
good identifiers is fast, defensible and easy to document. A probabilistic model
picks up the harder residue. Deterministic linkage also makes an excellent
benchmark: if a probabilistic model cannot beat a well-designed rule, that is
worth knowing before you publish anything.

Chapter 2.4 develops both properly, on data, with code.

## What linkage cannot fix

It is as useful to be clear about the limits.

- **A person who is missing from a source stays missing.** Linkage connects
  records; it does not create them. If the population you care about is
  under-registered in one of your sources, linking will not recover it — though
  it may help you measure how large the gap is.
- **A concept your sources do not measure stays unmeasured.** Linking two
  registers that both record the service and not the outcome gives you two views
  of the service.
- **Poor identifying information limits what is achievable.** If the fields you
  can compare are few, common and error-prone, there is a ceiling on how well
  any method can distinguish a true pair from a coincidence. Better methods do
  not remove that ceiling; they get closer to it.
- **Linkage is never error-free.** This is the one to internalise. Every linked
  dataset contains false matches — pairs joined that are different people — and
  missed matches — pairs that should have been joined and were not. Both bias
  analysis, and they bias it in different directions and usually not at random.
  Measuring and reporting them is not an optional refinement; it is what
  separates a statistical output from an assertion. Section 3 is devoted to it.

## The constraints that come first

A linkage project is a legal and institutional undertaking before it is a
technical one, and the technical part is rarely what delays it.

### Privacy and confidentiality

Linkage generally requires personal information — names, dates of birth,
addresses — and the whole point of it is to assemble a richer picture of an
individual than any single source held. That is exactly what data protection
regimes are designed to be careful about, and the increased richness genuinely
does increase the risk of re-identification.

Public opinion research in several countries has found consistent patterns:
people are largely unaware of how their data is already used, are unclear about
what anonymisation means in practice, and judge acceptability mainly by whether
there is a demonstrable public benefit. That last finding is the actionable one.
Being able to state clearly what a linkage is for, and who benefits, is not
public relations. It is a precondition.

### Consent is usually not the mechanism

For large administrative sources, obtaining individual consent is normally
infeasible: the data were collected long ago, from millions of people, for other
purposes. Linkage of this kind proceeds instead under a statistical or legal
mandate, with approvals from each data controller.

Where consent *is* feasible — linking a survey, cohort or trial to
administrative records — it introduces a different problem. People who consent
differ systematically from people who do not, typically by age, education and
economic circumstances, with the most disadvantaged least likely to consent.[^1]
A consent-based linkage can therefore produce a linked subset that is
unrepresentative in precisely the dimension the analysis cares about. This is a
selection bias to measure and report, not a detail.

### The separation principle

The standard institutional answer to "how do we let someone link these files
without letting anyone see too much?" is to split the work:

- the people who perform the **linkage** see identifiers but not the content —
  the clinical records, the incomes, the outcomes;
- the people who perform the **analysis** see the content but not the
  identifiers, receiving only a linked, pseudonymised file.

Often a trusted third party carries out the linkage. This protects
confidentiality effectively and is worth adopting. It has one real cost, which is
worth anticipating: the linkers usually do not know the analytical requirements,
and the analysts cannot inspect the linkage. Quality problems then surface late,
if at all. Good communication across that boundary matters more than it sounds
like it should, and the documentation practices in Section 3 exist partly to
bridge it.

Chapter 2.3 covers the technical side of this — pseudonymisation, hashing, and
how to build extracts that can safely cross an institutional boundary.

### Approvals take longer than the analysis

Access approvals from each data controller, data transfer or sharing agreements,
and any ethical review required in your setting are typically the critical path.
Offices that have done this consistently report the same lesson: start the
approvals in parallel with the technical work, not after it. Section 4 goes into
what has to be in place.

## Are you ready to start?

Before Section 2, it is worth checking that the following are true, or that you
know how you will make them true.

- You can state the **statistical question** the linked data will answer, and
  what would change if you had the answer.
- You have identified **two or more sources** that plausibly cover the same
  people.
- You know which **identifying variables** each source holds, and roughly how
  complete they are. If you do not, that is chapter 2.2's job.
- You know **who controls each source** and what their agreement would require.
- You have a view on whether a **usable common identifier** exists. If one does,
  say so and use it; the value of this toolkit to you is then mostly in the
  quality and documentation chapters.
- You can name a **plausible legal basis**, even if it is not yet secured.

If most of that holds, Section 2 takes it from a use case to a linked dataset, one step at a time, with code that
runs at every step.

[^1]: Mostafa, T. and Wiggins, R. (2015). *How consistent is respondent
behaviour to allow linkage to health administrative data over time?* Centre for
Longitudinal Studies. The finding is drawn from consent to health data linkage in
the UK Millennium Cohort Study.
