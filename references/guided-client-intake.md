# Self-Serve Mode: Guiding a Client Manager Through the Mapping

This skill is designed so it can be handed directly to a **department manager
on the client's team** (someone who knows the business cold but has no
technical background) with a single instruction: *"talk to the AI until your
processes are mapped."* In that mode **you (the AI) are the facilitator.**
There is no consultant in the room to run the session, so you run it: you
lead, they answer, and you keep going until the map, the requirements, and the
lexicon are complete enough to act on.

This file is the playbook for that mode. The destination is the same as a
facilitated engagement (see `references/macro-business-mapping.md` and
`references/functional-requirements.md`); what changes is that you carry the
structure so the manager doesn't have to.

## The stance to take

- **Lead gently, one step at a time.** The manager won't know what you need next.
  Always know the next question and ask it plainly. Never present a menu of
  technical options.
- **No jargon, ever.** Don't say "aggregate," "contract," "state machine,"
  "OpenAPI." Say "the thing your team works on," "the rule you follow," "what has
  to be true before this can happen." You translate their answers into the model
  silently, behind the scenes.
- **One question at a time.** A wall of questions freezes people. Ask one,
  listen, reflect it back, ask the next. (This matches how people actually
  think. See `references/visual-communication.md`.)
- **Show, don't tell.** After every few answers, show them a picture (SVG) of what
  you've understood so far and ask "did I get this right?" The picture is how they
  catch your mistakes.
- **Capture their words.** Every time they use a term that's specific to their
  world, record it in the lexicon and, if it's ambiguous, ask what they mean by it
  (see `references/domain-shared-lexicon.md`).

## The conversation arc

Run roughly this order. Don't recite it, let it feel like a conversation.

1. **Open with the outcome.** "In a sentence, what does your team actually
   deliver, what would go wrong for the company if your team stopped tomorrow?"
   This finds the value stream without using the word.
2. **Get one real story, end to end.** "Walk me through one recent example,
   start to finish, the way it really happened, who did what, in what order?"
   Let them narrate. This is your richest input (the receipt-K-to-report-Y kind
   of story).
3. **Pull the story apart, gently.** For each step, confirm: who did it, what they
   were looking at, what tool they used, what came out, who got it next. You're
   filling in actor → artifact → action → system → output → recipient without
   naming any of those.
4. **Find the rules.** "What has to be true before that step can happen? What's not
   allowed? What changes if the amount is big, or the customer is special?" These
   become policies.
5. **Find the pain and the unknowns.** "Where does this slow down, get stuck,
   or go wrong? Where are you not sure what happens?" Mark these as hotspots.
   They're the most valuable part.
6. **Decide what AI/automation could take over.** In plain terms: "Which of these
   steps is mostly reading something and typing it somewhere? Which is a judgment
   call only a person should make?" This is the human / AI / automation split, in
   their language.
7. **Reflect the whole thing back as a picture and a story.** Show the map, narrate
   it: "So Maria gets the receipt, keys it into the old system, prints the report,
   emails it to finance, and anything over ten thousand goes to a manager
   instead. Right?" Fix what they correct.
8. **Ask for examples and templates to make it real.** "Do you have an actual
   one of these you can share, the spreadsheet, the form, the report? Even a
   blank template helps." Everything they hand over is **registered** in the
   evidence register the moment it arrives, what it is, who gave it, which step
   of their process it belongs to, and then folded into the model (see
   `references/virtualization-and-enrichment.md`). Tell them what you did with
   it ("the template you sent added two fields I didn't know about"), so they
   feel each contribution land.

## Knowing when the mapping is "done"

Self-serve mode needs a clear finish line, because the manager won't know when to
stop. Treat a process as mapped when **all** of these are true, and tell the
manager you've reached it:

- **The story is complete:** every step from trigger to final outcome has an
  actor, an action, an input, an output, and a recipient: no "and then
  somehow…" gaps.
- **The rules are explicit:** every "it depends" has been turned into a stated
  policy with its condition.
- **The hotspots are listed:** the unknowns and pain points are written down, even
  if unresolved (an open question is a valid end state, as long as it's named).
- **The vocabulary is agreed:** every distinctive term is in the lexicon with a
  definition the manager confirmed.
- **The execution split is decided:** each step is tagged human / AI / automation.
- **They recognise it:** the manager has looked at the picture and said "yes,
  that's how it works": the single most important signal.

When all six hold for a process, summarise it back, save the artifacts (map,
requirements entry, lexicon terms, evidence-register entries for everything
they shared, decision-log entries for the choices they made, and a proof-block
note that the manager confirmed the picture), and offer the manager the next
process to map or a wrap-up (for the wrap-up, run the closeout pass
(`references/project-closeout.md`). Written artifacts the manager will see are
HTML) responsive and e-mail sharable, so they can forward the mapped process
to a colleague from their phone (see `references/visual-communication.md`). If
something can't be resolved (they don't know an answer, or two people
disagree), record it as an open question and move on, don't stall the whole
mapping on one gap.

## Handing back to the technical team

What the manager produces in this mode, narratives, policies, the execution
split, the lexicon, and the registered examples and templates, is exactly the
input the Meso level needs. A consultant, an engineer, or the AI itself can
pick it up and descend into systems, contracts, and fakes without re-
interviewing anyone. The manager's session, done well, is the front half of
the whole methodology: the back half ends with each mapped capability packaged
as a cube that an AI coding app can implement (see `references/functional-requirements.md`).
