# Documenting a Finished Project: the Closeout

A project that finishes without a closeout doesn't end — it just stops. This
file covers the two situations where you document something that is *done*:

1. **A cubemap engagement (or one cube, or one phase) finishes** — run the
   closeout pass below and produce the closeout dossier.
2. **A project is already built — possibly without this method — and needs
   documenting after the fact** — run the retro-documentation variant at the
   end of this file. It produces the same dossier.

Run a closeout whenever a cube goes live (a small, per-cube closeout), a phase
or the whole engagement wraps (the full pass), or the client simply asks
"document what we built."

## "Final" documentation in a living-model method

The method insists the model is never "done then built" — it is continuously
sharpened. That is not a contradiction with closeout; it tells you what
closeout *is*. You are not writing a tombstone. You are doing two things:
**freezing an honest as-built snapshot** of where everything landed, and
**handing over the living model** with the rules for changing it. Final means
"true as of handover," not "dead."

## The as-built truing pass

Before writing anything new, make the existing artifacts true. Documentation
assembled from untrue artifacts is worse than none — it reads as authoritative
and lies.

1. **Contracts vs production.** Run the drift suite
   (`references/contract-drift-and-testing.md`). Where the spec and reality
   disagree, fix the spec — and log the divergence as a decision, because
   someone chose it.
2. **Statecharts vs behaviour.** Spot-check that the guards and transitions
   still match what the real code does (`references/round-trip-and-ddd-guardrails.md`).
3. **Requirements.** Annotate every acceptance criterion with its final proof
   status — *verified in production* where it is, and honestly not where it
   isn't (see the proof ladder in `references/living-documentation.md`). The
   evidence for "verified in production" is whatever was actually observed —
   production logs, monitoring, or the client's attestation — recorded with
   the date and the source.
4. **Evidence register.** Every entry complete; every "what it changed" field
   filled. An open register entry at closeout is an unfinished thought.
5. **Decision log.** Every policy in the model traces to a logged decision or
   a mapping session. An orphan policy gets its provenance reconstructed now,
   while someone still remembers.
6. **Lexicon.** Final pass with the client: every term still means what the
   definition says.
7. **Per-cube stage declared.** For each capability, state plainly where it
   landed: live in production (Stage 3), running on a stateful simulation
   (Stage 2), example-seeded fake (Stage 1), or paused. No euphemisms — a cube
   still on fakes is a fine, honest end state.

If documentation moved with the work all along
(`references/living-documentation.md`), this pass is an afternoon. If it
didn't, it's reconstruction — do it anyway, and treat the pain as the argument
for the discipline next time.

## The closeout dossier

The deliverable: `business-models/closeout.html` — a scrollytelling explainer
in the house style (see `references/visual-communication.md`; start from
`assets/scrollytelling-template.html`), self-contained, responsive,
e-mail-sharable like every deliverable. One dossier per engagement: a per-cube
closeout adds or refreshes that cube's scenes in the same file rather than
spawning a new document. Date-stamp the dossier and tag the repo at each
closeout — that pair is the frozen as-built snapshot. It is the one document a stakeholder
forwards to a colleague to answer "so what did we actually get?" Scenes, in
story order:

1. **The story.** A named actor, before → after. "Maria used to key every
   receipt into the old system by hand; today the agent reads them and she
   approves the exceptions."
2. **What was mapped and what was built.** Per capability: the stage it
   reached, live vs simulated, with the map picture.
3. **The split as delivered.** The human / AI agent / automation allocation as
   it actually ended up — this is the picture of how the organisation changed.
4. **The proof.** The acceptance criteria and their final status — a digest
   of the proof blocks, not a re-telling.
5. **The decisions that shaped it.** A digest of the decision log: the
   handful of choices a future reader needs to not undo by accident.
6. **The evidence trail.** The register, summarised: what the client gave,
   what each piece changed.
7. **What remains.** Open hotspots, criteria not yet production-verified,
   paused cubes — named and owned, not hidden.
8. **How to change it from here.** The handover (below), told as the closing
   scene.

## Handover: changing the system after you leave

The dossier's last job is to keep the method alive without you:

- **Each cube has an owner** — a name on the client side per capability.
- **The standing rule, restated in client terms:** changes enter at the model
  — a policy, a requirement, a contract — never code-first. The repo's
  governance (contracts reviewed like production code, conformance gating
  deploys — `references/project-scaffold.md`) is what enforces it.
- **Where everything lives:** one picture mapping the scaffold — "the rules
  are here, the agreements are here, the proof is here."
- **How to hand a cube to the next implementer:** the cube package is the
  spec (`references/functional-requirements.md`); the dossier points at it
  rather than duplicating it.

## The closeout checklist

The engagement-level "done" — the complement of the per-process criteria in
`references/guided-client-intake.md`. Closeout is complete when:

- Every cube has a declared stage; the drift suite is green or every
  divergence is a logged decision.
- Every acceptance criterion has a proof status.
- The decision log accounts for every policy in the model.
- The evidence register is complete — no open "what it changed" fields.
- The lexicon is confirmed final by the client.
- Open questions are named and have owners.
- The dossier exists, passes the phone-width and self-containment checks, and
  **the client has walked it** — same sign-off rule as everything else: the
  walkthrough is the acceptance, and it gets recorded *in the dossier itself*
  (date, who walked it, what they confirmed). Remote or async is fine — a
  short call, or an annotated reply to the e-mailed dossier.

## Documenting a project after the fact (retro-documentation)

Sometimes the project is finished but was never mapped — a system already in
production, built by someone else, and the client wants it documented. The
method runs in reverse, and the built system itself is the evidence:

- **Register what exists.** Screenshots of real screens, exports of real
  data, actual outputs (reports, emails, files), the code and its contracts if
  readable — each one an entry in the evidence register, same as any client
  artifact.
- **Interview the operators.** Run the intake arc
  (`references/guided-client-intake.md`) against *how it works today* — one
  real story end to end, the rules, the hotspots, who does what.
- **Reconstruct the model.** Capabilities and policies from the interviews
  (L1); contracts from observed payloads and integrations (L2); statecharts
  from observed behaviour (L3). Mark inferred parts as inferred — an honest
  "we believe this is the rule, unconfirmed" is a hotspot, not a failure.
- **Then run the truing pass and produce the dossier** exactly as above.

The output doubles as something better than documentation: it is the starting
map if the client ever wants to redeploy the legacy system as cubes.
Retro-documentation of a finished project is the first half of its
redeployment as an AI-native one.
