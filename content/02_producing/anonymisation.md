# Anonymisation and safe data preparation

Linkage needs identifying information. Data protection exists to limit who sees
identifying information. This chapter is about the standard way of holding both
of those at once, what it achieves, and — the part usually left out — what it
costs you in matches.

The accompanying notebook,
[Building pseudonymous identifiers](nb02-pseudonymisation.ipynb), implements
everything here and measures the cost on the bundled data.

## Anonymisation and pseudonymisation are not the same

The words are used loosely and the distinction matters legally.

**Anonymisation** removes the possibility of identifying an individual. Done
properly, the result is no longer personal data and falls outside most data
protection regimes. It is also, by construction, no longer linkable at the
individual level, because linkage *is* identification of a sort.

**Pseudonymisation** replaces direct identifiers with a substitute value. The
data remains personal data: with the right additional information, individuals
can still be singled out. It reduces risk; it does not remove it.

Every technique in this chapter is pseudonymisation. If someone tells you a
hashed name field is "anonymised", they are mistaken, and the mistake has
consequences for how the file must be handled.

## The threat model

Be specific about what you are protecting against. Three distinct risks:

- **Accidental disclosure.** A file with names in it is emailed, lost, or copied
  to the wrong place. Pseudonymisation helps a great deal.
- **The recipient looking at things they should not.** An analyst who only needs
  outcomes should not receive names. Pseudonymisation, combined with the
  separation principle below, addresses this directly.
- **A determined attacker with side knowledge.** Someone who knows a person is
  probably in the file and wants to confirm it, or who holds a public list of
  names to test against. Pseudonymisation helps only if it is done correctly, and
  a naive implementation helps not at all.

The third is the one that determines the design.

## The separation principle

The institutional pattern that makes linkage acceptable in most jurisdictions:

- the people who perform the **linkage** see identifiers but not the substantive
  content — no incomes, no diagnoses, no outcomes;
- the people who perform the **analysis** see the content but not the
  identifiers — they receive a linked file keyed on a pseudonym.

Sometimes a trusted third party performs the linkage precisely so that neither
data controller ever sees the other's identifiers.

In practice this means producing **two files from each source**, with different
audiences:

| File | Contains | Who holds it |
|:--|:--|:--|
| **Shareable extract** | Pseudonymous key plus non-identifying attributes | Can cross an institutional boundary |
| **Internal linkage key** | The mapping from pseudonym back to the record and its identifiers | Never leaves the institution that made it |

The internal key is what lets you attach results back to your own records once
the linkage returns. Losing it means the linked output cannot be used.

## Hashing, and how to do it properly

A cryptographic hash function turns any input into a fixed-length string. It is
deterministic — the same input always yields the same output — and it cannot
practically be run backwards. Two institutions can therefore hash the same
person's details independently and get the same value, without either sending
the other a name.

Two properties matter here, and they pull in opposite directions.

**The avalanche effect.** Changing one character changes the entire output.
Good for security. Fatal for tolerance: two records for the same person that
differ by a single typographical error produce two unrelated hashes. There is no
such thing as an approximate hash match.

**Determinism.** The same input always gives the same output — which means that
if the space of possible inputs is small, an attacker simply hashes all of them.
Hashing the value `M` or `F` yields two hashes that anyone can reproduce in a
second. Hashing a name is not much harder: national name frequency lists are
public, so an attacker with a candidate list can hash every entry and compare.
This is a dictionary attack, and it works.

### Salt and pepper

The defence is to mix a secret into the input before hashing, so that an
attacker without the secret cannot precompute anything.

- A **salt** varies per record. Excellent for stored passwords. Useless for
  linkage, because the same person in two systems would get two different
  hashes.
- A **pepper** is a single secret constant, shared by the institutions doing the
  linkage. This is what linkage requires.

The pepper is the entire security of the scheme, so:

- it is **never** in code, in a notebook, or in version control — read it from
  an environment variable or a secrets vault;
- it is shared through a channel separate from the data;
- if it leaks, every identifier ever produced with it is compromised and must be
  re-issued.

### The key string

What you hash matters as much as how. The input is a **canonical key string**:
cleaned identifying fields, in a fixed order, with a fixed separator. Two
decisions have to be taken explicitly, and both change your results:

- **Which fields are required.** A record missing a required field gets no
  identifier and cannot be linked at all.
- **How a missing optional field is represented.** An empty string is not the
  same as an absent field, and both institutions must make the identical choice
  — otherwise their hashes will never agree.

The second decision is quietly consequential. Treating a missing second surname
as an empty string keeps the record linkable, but a person whose second surname
is recorded in one system and not the other will produce two different
identifiers. They are the same person, and the method will not find them.

## What it costs: the numbers

This is the part that is usually asserted rather than measured. The bundled data
is synthetic, so we know that exactly 4,500 people appear in both registers, and
we can score the method.

Requiring given name, first surname, sex and nationality:

| | Result |
|:--|--:|
| Records that received an identifier (health register) | 25,278 of 30,000 (84.3%) |
| Records that received an identifier (social security) | 23,415 of 27,000 (86.7%) |
| Pairs proposed | 1,080 |
| Pairs that were correct | 1,080 |
| **Precision** | **1.0000** |
| **Recall** | **0.2400** |
| True matches never found | 3,420 |

Read those two numbers together.

**Precision is 1.0000.** Every proposed pair was right. Five cleaned identifying
fields agreeing exactly is strong evidence, and in a file this size coincidental
agreement essentially does not happen. (In a register of tens of millions it
would happen occasionally, so expect very high precision rather than exactly
one.)

**Recall is 0.2400.** Three quarters of the real matches were never found, for
two structural reasons:

1. About 15% of records never received an identifier at all, because a required
   field was missing.
2. Any difference in any field — one letter, one accent that survived cleaning,
   an abbreviated given name, a second surname present in one system only —
   produces two unrelated hashes.

No amount of care in the hashing improves this. The method cannot tolerate
error, and administrative data contains error. That is a ceiling, not a bug.

## Two things to take from this

**First, a privacy mechanism has excluded a subpopulation.** The 15% of records
with a missing required field were removed from the analysis by a decision that
looked purely technical. Whether those people differ systematically from the rest
— and in administrative data they usually do — is now a question you have to
answer and report. This is where differential linkage error enters a project,
long before anyone computes a match weight.

**Second, exact matching on pseudonyms is a floor, not a ceiling.** It is an
excellent first pass: fast, defensible, and its precision means anything it finds
can be trusted. Many offices run it as stage one and apply something more
tolerant to what remains. What it cannot do is deliver a complete linkage, and
publishing a match rate from it without saying so would overstate the overlap
between two registers by a factor of four.

## When you cannot exchange identifiers at all

Some settings forbid sharing even pseudonymised identifying data. Two
approaches exist beyond the scope of this toolkit, both worth knowing by name:

- **Privacy-preserving record linkage (PPRL)** encodes identifiers into
  structures — commonly Bloom filters — that permit *approximate* comparison
  without revealing the underlying values, recovering some of the tolerance that
  plain hashing destroys, at the cost of a more complex protocol and some
  residual attack surface.
- **Trusted third-party models**, where a designated body receives identifiers
  from both sides, performs the linkage, and returns only linked pseudonyms.

Both are referenced under Reference Materials.
This toolkit assumes the common case: an office that can hold identifying data
from its sources under an appropriate legal basis, and wants to link it well.

[Chapter 2.4](linkage-approaches.md) takes up the problem this chapter exposed —
how to compare records that nearly agree, instead of demanding that they agree
exactly.
