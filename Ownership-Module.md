# ARCOS → Module → Ownership Module (Developer Overview)

## ARCOS

ARCOS is infrastructure that makes NFTs programmable and operational — not just a pointer to an owner, but a governed system for what can be done with a digital asset, by whom, and under what rules. It's organized as a set of **Modules**, each governing one domain of an asset's behavior.

---

## Module — the generic infrastructure

A Module is a governed state machine: one domain of state, owned by exactly one authority, changed only through defined transitions, reachable by anything else only through MAS. Every Module is built from the same nine pieces. The pieces are fixed; what fills them is domain-specific.

**Governed State** — the only thing ever actually stored. A fixed, named set of fields representing real facts about the asset. Nothing else is stored anywhere else.

**Authority** — who can legitimately cause the state to change. Never stored as its own fact — always computed live, from Governed State, every single time it's checked.

**Transition** — the finite, named set of ways Governed State is allowed to change (not a generic write — each one is named and specific). Every Transition runs the same order: check Authority (identity) → check Invariant (adequacy) → write → log. A failure at either check means nothing is written, not even partially.

**Invariant** — the rule that decides whether a specific change is valid, given that the caller already passed the Authority check. Identity asks *who*; Invariant asks *is this particular change okay right now*.

**History** — a permanent, append-only record of every Transition attempt, success or failure. Failures are kept exactly like successes — nothing is discarded.

**Lifecycle** — not stored. A live read over History, answering "given everything recorded so far, what stage is this asset in right now."

**Recovery/Failure** — Failure is what any ordinary Transition becomes when its Invariant doesn't pass. Recovery is a specific Transition gated by a *second, independent* Authority, kept in reserve for when the primary one is lost.

**Capabilities** — never stored. Named patterns read off Governed State, measured against Authority. A capability is a description of the state, not a separate fact about it.

**MAS (Module Activation System)** — the only path between Modules. No Module calls another directly. If one Module's state answers a question another Module needs, the second queries it through MAS instead of holding its own copy.

---

## Ownership Module — the applied instance

This is Module, filled in for one domain: who owns a digital asset, and what that ownership actually lets them do.

**Governed State:** five fields — **Shares, Possession, Use, Benefit, Version.** Nothing else is stored.

**Authority (Authority Root):** strict majority of Shares, recomputed live on every check. If Shares change, Authority changes automatically — nothing to update.

**Transitions:** mint, delegateUse, revokeUse, moveToCustody, returnFromCustody, splitShares, mergeShares, setGuardians, recoverOwnership.

**Invariants:** e.g. Use can't be granted if already committed elsewhere; Shares must sum to 100; a Rental can't be revoked before its term ends.

**History:** every Transition attempt, pass or fail, logged permanently — the Ledger.

**Lifecycle:** a read over that Ledger — Genesis / Active / Encumbered / Fractured / Suspended / Recovered.

**Recovery/Failure:** Recovery is gated by guardian quorum, not Shares-majority — a second authority, used once, for when the first is lost. Failure is just what you see in the Ledger when an ordinary Invariant didn't pass.

**Capabilities:** Delegation, Rental, Custody, Shared Ownership — four patterns read off the five fields, not stored separately.

**MAS in practice:** Settlement (payment/consideration for a Rental) was deliberately kept *outside* Ownership — different authority, different lifecycle — and Ownership's Invariant queries it through MAS rather than storing a price field itself.

---

## Current status — what needs work

**Ownership Module today governs Rights only.** Possession, Use, Benefit, and Shares are all Rights — what a holder is *entitled* to do. Nothing in the current Governed State represents what a holder *owes* while holding something.

---

## Demo

Live, interactive, no wallet or blockchain needed — walks through Governed State, Authority Root, every Transition, Invariant pass/fail, History, Lifecycle, and Recovery on one NFT (Sunset #7).
