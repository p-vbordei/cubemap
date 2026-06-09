# Micro (L3): Statecharts and Taming State Explosion

At the Micro level you model how a single component *behaves over time* — and you
wire a live UI (or service logic) to that behaviour, calling the L2 fakes. The
tool is **XState v5**, which implements Harel statecharts and the actor model.
Behaviour modelled this way is executable: it runs in the canvas, drives the UI,
and makes the business rules from Macro structurally enforced rather than
remembered.

## Why statecharts, not scattered conditionals

The policies you captured at Macro ("no refund before inspection," ">€10,000 needs
a manager") are state-dependent rules. Implemented as `if` statements sprinkled
through a codebase, they rot: someone forgets a check, two flags contradict, an
impossible state slips through. A statechart makes the rule part of the model's
structure — the transition simply doesn't exist unless its guard passes — so the
illegal path is unrepresentable. This is the "make illegal states impossible"
commitment realised in code.

XState v5 treats every machine as an **actor**: a unit that holds state, receives
events, and can spawn and message other actors. That scales from a single UI
flow up to orchestrating many backend services, using the same formalism at both
ends of the system.

## A statechart wired to a fake

```js
import { createMachine } from 'xstate';

const returnValidation = createMachine({
  id: 'returnValidation',
  initial: 'idle',
  states: {
    idle: { on: { INITIATE_RETURN: 'verifyingEligibility' } },
    verifyingEligibility: {
      invoke: {
        src: 'checkPolicyContract',          // calls the L2 fake/contract
        onDone: 'awaitingOperatorApproval',
        onError: 'rejectedBySystem'
      }
    },
    awaitingOperatorApproval: {
      on: { APPROVE: 'approved', REJECT: 'rejectedByOperator' }
    },
    approved: { entry: 'triggerRefundEvent', type: 'final' },
    rejectedBySystem: { type: 'final' },
    rejectedByOperator: { type: 'final' }
  }
});
```

The custom React Flow node renders a small live UI (an "HTML fake" of the
operator's panel) bound to this machine via `@xstate/react`'s `useMachine`. While
the machine is in `awaitingOperatorApproval`, the node shows Approve/Reject
buttons; clicking Approve sends `APPROVE`, the machine transitions to `approved`,
runs `triggerRefundEvent`, and calls the mock from Meso. The whole flow is real —
on fakes.

## The state explosion problem, and the math that fixes it

A flat finite state machine explodes combinatorially. If a component has *n*
independent boolean attributes (valid/invalid, enabled/disabled, dirty/clean…),
representing every combination takes the Cartesian product:

```
flat states  =  S₁ × S₂ × … × Sₙ      →  2ⁿ for n booleans
```

Ten independent booleans ⇒ 2¹⁰ = 1024 states. A diagram that size is useless.

Harel statecharts (native in XState) collapse this from multiplicative to additive
growth with three constructs:

- **Orthogonal (parallel) regions.** Model independent attributes as parallel
  sub-machines inside one superstate instead of combining them. The complexity
  becomes a *sum*, not a product:

  ```
  parallel states  =  S₁ + S₂ + … + Sₙ      →  2 + 2 + … + 2 = 2n
  ```

  Ten booleans ⇒ 20 states instead of 1024.

- **Hierarchy / nesting (superstates).** Group related states in a container.
  Global transitions like `CANCEL`, `ERROR`, `TIMEOUT` are lifted to the
  superstate boundary, deleting dozens of redundant edges from the diagram.

- **Guards and history.** Guards block invalid transitions based on context (this
  is where Macro policies live). History states (shallow `H` / deep `H*`) let a
  superstate resume where it left off after an interruption.

The practical payoff: the visual model stays legible as the system grows, and the
behaviour stays provably bounded — which is also what makes it testable.

## Authoring and connecting

- Author and visualise machines in **Stately Studio**, or hand-write them.
- Run them in the canvas with **XState v5** and render UI via `@xstate/react`.
- `invoke` services point at the **L2 fakes** during simulation and at real
  implementations at Atomic — the machine doesn't change when the backend becomes
  real, because it only ever knew the contract.

## What you leave Micro with

- A statechart per behaviourally-interesting component, with Macro policies encoded
  as guards.
- Live, interactive nodes on the canvas that run those machines against the fakes.
- A flow stakeholders can drive by hand — proving the *rules*, not just the happy
  path — before any production backend exists.
