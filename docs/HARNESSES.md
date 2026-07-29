# Harness portability & install: maintainer notes

How cubemap stays installable across AI coding harnesses, what its portable
floor is, and the authoring rules that keep it that way. Maintainer-facing; not
a stakeholder deliverable, so plain Markdown is fine here.

**cubemap is portable prose.** `SKILL.md`, `references/*.md`, `assets/*.html`
and `docs/*.svg` are plain Markdown/HTML/SVG any harness can load. The only
thing that is harness-specific is the *install location* and the install lines
in `README.md`. There is deliberately **no build pipeline**, porting is a
copy, not a compile (see "Porting" below). Don't add one until a real non-
Claude user exists; the method's own law is *don't adopt tooling before the
level calls for it.*

Last verified: 2026-06-22. The cross-harness facts below are derived from the
[Agent Skills spec](https://agentskills.io/specification) and from the
`impeccable` project's harness research (its `docs/HARNESSES.md`,
self-reported, last verified 2026-04-28). **Re-verify against current harness
docs before relying on them for an actual port.**

## The portable floor: only `name` + `description` are load-bearing

Every harness honours `name` and `description`. Most honour *nothing else* in
frontmatter, Gemini and Codex validate only these two; unknown fields are
silently ignored everywhere. So:

- **cubemap's activation surface is its `description`, on every harness.** Keep
  it keyword-dense, front-loaded with trigger verbs/nouns, ended with a negative
  scope clause, and **≤ 1024 characters** (the spec's cap; an over-length
  description is truncated in some skill pickers).
- **Never make the method depend on a Claude-only frontmatter field**
  (`allowed-tools`, `user-invocable`, `argument-hint`, `model`, `effort`,
  `context`, `hooks`, …). cubemap's frontmatter is `name` + `description`
  only, keep it that way and the port stays mechanical.

## Where each harness reads skills from

The key fact for cubemap: a single `.agents/skills/cubemap/` tree is read by
**five** harnesses (Codex natively; Gemini, Cursor, GitHub Copilot, OpenCode
as a documented fallback). Add `.claude/skills/cubemap/` for Claude and you
cover six harnesses with **two directories**: no per-harness build.

| Harness | Native dir | Also reads |
|---------|-----------|------------|
| Claude Code / Cowork | `.claude/skills/` | none |
| Codex (OpenAI) | `.agents/skills/` (primary) | none |
| Gemini (Google) | `.gemini/skills/` | `.agents/skills/` |
| Cursor | `.cursor/skills/` | `.agents/skills/`, `.claude/skills/` |
| GitHub Copilot | `.github/skills/` | `.agents/skills/`, `.claude/skills/` |
| OpenCode | `.opencode/skills/` | `.agents/skills/`, `.claude/skills/` |
| Kiro / Qoder / Trae / Rovo Dev | own `.kiro|.qoder|.trae|.rovodev/skills/` | none (need their own copy) |

Note: `impeccable` ships its reference subfolder as `reference/` (singular);
cubemap uses `references/` (plural). This is fine because `SKILL.md` points at
each `references/X.md` by explicit relative path and the model reads that
path: the dir name is not a harness-enforced convention for the interactive
CLIs. If a future harness auto-indexes a fixed `reference/` dir, re-check
before relying on the plural.

## Porting (do this only when a real Gemini/Codex/… user appears)

The port is a copy plus a README edit. There is nothing to compile:

1. **Copy the tree** `SKILL.md`, `references/`, `assets/` into the target dir:
   - Gemini / Codex / Cursor / Copilot / OpenCode → `.agents/skills/cubemap/`
     (one copy serves all five). Codex needs no extra dir, it has no hook to
     install.
   - Claude → `.claude/skills/cubemap/` (or the `.skill` bundle in Cowork).
   - Kiro / Qoder / Trae / Rovo Dev → their own `…/skills/cubemap/`.
2. **No frontmatter changes needed**: `name` + `description` are the only
   load-bearing fields and they are universal.
3. **Gemini only:** the user enables Skills in `/settings`.
4. **Edit `README.md`** install section for the target harness (the only place
   harness-specific paths live).
5. **Carry the `dsl` companion** the same way (see the blocker below).

## Blocker for any multi-harness port: the `dsl` dependency

cubemap **hard-depends** on the companion `dsl` skill
(github.com/p-vbordei/dsl), which owns `LEXICON.md`. A cubemap install on any
harness **without** dsl alongside it produces a silently degraded skill: the
AI substitutes generic terms for the client's words: the exact failure cubemap
exists to prevent. Any port must install dsl in the same harness dir
(`.agents/skills/dsl/`, `.claude/skills/dsl/`, …) and the README must say so.
Treat "how dsl ships alongside cubemap" as a precondition, not a footnote.

## Authoring rules (keep the skill portable and clean)

- **`name` + `description` are the only load-bearing fields.** Don't build a
  dependence on any Claude-only frontmatter field.
- **Keep `SKILL.md` and `references/` harness-agnostic prose.** No
  `~/.claude`, `.claude/skills`, `CLAUDE.md`, `GEMINI.md`, `AGENTS.md`
  literals in the body, install paths live **only** in `README.md`. ("Claude
  Code, Lovable, v0, Cursor…" as an *illustrative list of downstream AI coding
  apps* is content, not a harness reference, leave it.)
- **Description ≤ 1024 chars**, trigger-loaded, negative scope at the end.
- **One home per concept; cross-link, don't restate.** Each idea has a single
  owning reference; others link to it (e.g. the drift-detection suite lives only
  in `contract-drift-and-testing.md`; `tooling-matrix.md` indexes and links).
- **Just-in-time reference loading.** `SKILL.md` is the map and the router; the
  *how* lives in the reference, loaded the moment the situation matches (see the
  "Start here" table in `SKILL.md`).
- **Deliverable invariants are content rules, harness-independent:** self-
  contained HTML, inline SVG, e-mail-sharable, responsive. They hold on every
  harness because they are about the artifact, not the tool.

## What we deliberately do NOT build

cubemap is a private consulting methodology with a known audience, not a public
product. So, unlike `impeccable`, cubemap does **not** ship: a build/transform
pipeline, per-harness committed copies, an npm CLI, edit-time hooks, a browser
extension, a website download API, a plugin-marketplace public listing, or a
`skills-lock.json`. Distribution is `git clone` / submodule from
github.com/p-vbordei/cubemap plus the `cubemap.skill` bundle on a release. Add a
channel only when a concrete need forces it.
