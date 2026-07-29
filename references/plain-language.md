# Plain Language: One Meaning Per Word

Everything this method produces is read by someone who did not write it: a
department manager, a finance lead, an operator, an AI coding app implementing
a cube from its requirements. Most of them are reading in a second language,
and none of them can ask a clarifying question at 2am when the document is all
they have. Ambiguous prose in a requirement is not a style problem. It is a
defect that ships.

There is a standard built for exactly this situation. **ASD-STE100 (Simplified
Technical English)** was written in 1986 so that an aircraft mechanic anywhere in
the world could read an English maintenance manual and not misunderstand a
procedure. It is 53 rules and a dictionary of about 900 approved words, each word
carrying exactly one meaning. Almost every rule is machine-checkable, which is
what separates it from advice like "write clearly."

## Why a system, and not a list of banned words

The instinct when prose reads like a machine wrote it is to ban the tells: the em
dash, "delve," "in today's fast-paced world." That treats the symptom. A 2026
experiment ran six engineering writing tasks through four conditions on two
models and counted violations per hundred words:

| Condition | Claude Sonnet | GPT-5.5 |
|---|---|---|
| Baseline | 4.36 | 3.54 |
| A list of banned words | 4.21 (−3%) | 2.14 (−40%) |
| Orwell's six rules | 2.48 (−43%) | 1.69 (−52%) |
| **An STE writing system** | **1.12 (−74%)** | **1.76 (−50%)** |

Source: "The cure for AI slop is a 1986 aircraft manual," Ege Chelebi,
chele.bi/videos/the-cure-for-ai-slop (kit, linter and raw data published).
Sample size is small (n=6). Read the direction, not the decimals.

The lesson transfers directly: a writing *system* removes far more slop than a
list of prohibitions, because a prohibition tells a writer what to avoid and a
system tells them what to do. This mirrors what the rest of this skill already
believes: a contract beats a warning, and a guard in a statechart beats a
comment asking a developer to remember.

The same finding carries its own limit, and it is worth stating plainly: STE
fixes the *form* of the writing. It cannot tell you whether you had anything to
say. A perfectly conformant requirement that maps the wrong process is still
wrong. Plain language is a quality floor, not a substitute for the mapping work.

## The em-dash is forbidden

**Never write an em-dash. Not in a deliverable, not in a diagram label, not in a
requirement, not in this skill's own files, not in a commit message, not in a
reply to the person you are working with. There is no mode in which it is
allowed and no context that excuses it.** The character is `—` (U+2014). The
spaced double hyphen ` -- ` is the same offence in a different costume. The
en-dash `–` stays legal for numeric ranges only, as in `P10–P90`.

The reason is not taste. The em-dash is the loudest single signal that a machine
wrote a piece of text, and a consulting deliverable that reads as machine-written
loses the trust the entire engagement depends on. A client who suspects the
requirements were generated rather than understood stops reading them as a
description of their own business. That is the whole product, gone, over
punctuation.

There is always a replacement, and it is usually better:

| Instead of | Write |
|---|---|
| `the map — a shared canvas — is the artifact` | `the map, a shared canvas, is the artifact` or `the map (a shared canvas) is the artifact` |
| `one endpoint — the requirements` | `one endpoint: the requirements` |
| `it is never done — it converges` | `it is never done. It converges` |
| `## Start here — route to the reference` | `## Start here: route to the reference` |
| `- **Facilitated** — a consultant runs it` | `- **Facilitated**: a consultant runs it` |
| `fast — and correct` | `fast, and correct` |

Reaching for an em-dash is a reliable sign that the sentence is carrying two
ideas. Splitting it into two sentences is usually the better fix, and it is what
the 20-word rule below asks for anyway.

The deliverable gate fails on this. It is not a warning and it has no strict
mode, because there is no text in which the character is acceptable.

(The rule has to name what it bans, so the character appears above inside code
spans, and once more as an escaped `—` in the gate's detector. Those are the
only places it may exist in this repository. Do not "clean" them.)

## Two modes: pick by what the text has to do

Not every sentence in a deliverable does the same job, so the rules do not apply
at one strength everywhere. Judge by consequence: if a reader acting on the
sentence could do the wrong thing, the sentence is normative.

**Strict, normative text.** Functional requirements, acceptance criteria,
policies and business rules, statechart guards, procedures and runbooks,
contract field descriptions, error messages, anything with MUST or MUST NOT in
it. Every rule below applies without exception. Mark these regions in the HTML
with `data-ste="strict"` so the deliverable gate checks them:

```html
<section data-ste="strict">
  <h3>FR-014 · Refund approval</h3>
  <p>The system holds a refund over 10,000 EUR. A manager approves the refund.
     The system then issues the payment.</p>
</section>
```

**Flavored, narrative text.** The story spine of a scrollytelling explainer,
the lede, a figure caption, a "what's at stake" paragraph. Keep the discipline
(active voice, short sentences, one name per thing, no marketing vocabulary),
and keep the voice. This method sells its ideas with stories about named
people, and a story written to a 20-word ceiling stops being a story. The gate
warns here instead of failing.

## The strict rules

**Words**

- One name per thing, everywhere, in every deliverable. If the client calls it
  *receipt K*, it is *receipt K* in the requirement, in the contract field name,
  in the fake's seed data, and in the diagram label. This rule is the reason the
  Domain-Shared Lexicon exists: STE says *use one name*, the lexicon says *which
  name*. See `references/domain-shared-lexicon.md` and the companion `dsl` skill.
- One meaning per word. If *order* means the customer's purchase, it never also
  means the sequence of steps. Rename one of them and record it in the lexicon.
- Short, common words. *start* not *commence*. *use* not *leverage*. *help* not
  *facilitate*. *about* not *approximately*.
- No marketing vocabulary: *seamless*, *robust*, *cutting-edge*, *unlock*,
  *empower*, *best-in-class*, *game-changing*. These words carry no information
  and cost the document its credibility with the person paying for it.

**Verbs**

- Active voice with a named actor. "The system holds the refund," not "the refund
  is held." Passive voice hides who is responsible, and *who is responsible* is
  half of what a functional requirement exists to say.
- Direct verbs, not nominalizations. "approves the refund," not "performs an
  approval of the refund."
- One tense, no stacked auxiliaries. Delete "it is important to note that this may
  help to improve" and write the claim.

**Sentences**

- 20 words maximum for an instruction or a requirement, 25 for a description.
- One instruction per sentence. Two actions means two sentences, or a numbered
  list.
- The condition comes before the command: "If the spread is below the wear cost,
  do not discharge." A reader who stops after the first clause must not have
  started doing the wrong thing.
- No semicolons. Use a period.
- No contractions. Write *do not*, not *don't*.
- Keep the articles. "The system reads the meter," not "System reads meter."

**Paragraphs and lists**

- One topic per paragraph, six sentences maximum.
- Any procedure becomes a numbered vertical list in the imperative. Prose is the
  wrong shape for a sequence of steps.

## How this is enforced

Prose rules that live only in a document decay. These are checked by the
deliverable gate that ships in every HTML deliverable
(`assets/deliverable-gate.html`): open the file with `#audit` on the URL, or run
it headless. The gate fails on strict-region violations and warns on narrative
ones. What it checks mechanically:

- sentence length, against the mode's ceiling
- semicolons and contractions in strict regions
- marketing vocabulary everywhere

What it cannot check, and you must: one name per thing (cross-read against
`LEXICON.md`), condition-before-command, and whether the sentence says anything.

## A worked correction

Before, 41 words, passive, two nominalizations, a name collision between *ticket*
and *request*:

> It is important to note that in cases where a refund request has been submitted
> by a customer and the value of the ticket exceeds the threshold, an approval
> action must be performed by a manager prior to the issuance of the payment.

After, strict mode, three sentences, none over 13 words, one name per thing:

> A customer submits a refund request. If the request is more than 10,000 EUR, a
> manager approves it. The system then issues the payment.
