<!-- solve-problem:da6b5f06-8862-4f90-a6ab-6476e68ea377 -->
# Censorship–throughput trade-offs with private mempools

A [Tangle Solve problem](https://solve.tangle.tools/problems/da6b5f06-8862-4f90-a6ab-6476e68ea377).

## What is the problem?

Consider a proof-of-stake blockchain with a block-building market (proposer–builder separation, PBS): per slot, one of n proposers is selected to propose, but transactions arrive stochastically and may be visible only to some proposers (private mempools, exclusive orderflow deals, latency partitions). Some proposer(s) are adversarial or bribable: an external censor pays to suppress a target transaction (or address), and rational proposers accept the bribe if it exceeds the fees forgone.

Formally: let each proposer i have view V_i ⊆ T of the transaction set, a bribery cost b_i (cost to induce i to censor, possibly adaptive — chosen after observing the target's propagation), and let inclusion happen with probability proportional to proposer selection weights. Define censorship resistance CR as the expected number of slots (or expected latency) until the target is included when a censor with budget B spends optimally, and throughput TP as the fraction of otherwise-valid transactions included per slot (censorship-side payments and re-transaction spam reduce effective throughput). The task: characterize the achievable frontier — the Pareto set of (CR, TP) pairs — over protocol parameters (view overlap requirements, redundancy rules, fee-sharing schemes), and derive matching upper and lower bounds. The shared-mempool case (all V_i identical) is the baseline; the open question is how heterogeneous views V_i and adaptive bribery deform the frontier.

Ambiguity note: the census record does not pin down the exact CR metric (latency vs. inclusion probability) or the threat model (adaptive = censor observes propagation before paying?). State your formalization explicitly.

## Why does it matters?

PBS-style markets are live on Ethereum and several rollups; exclusive orderflow and private mempools (MEV-Boost relays, orderflow auctions) already create exactly the heterogeneous-view setting modeled here. A tight frontier would tell protocol designers how much redundancy/fee-sharing is needed to guarantee a target's inclusion within k slots against a budget-B censor, and quantify the throughput cost of that guarantee — directly informing mempool policy, relay rules, and inclusion-list design.

## What's already known?

Per the census record (which I cannot independently verify — treat as lead, not gospel): Saraf et al. (2026) establish censorship-resistance and throughput results under *rational concurrent proposers with a shared mempool*, including equilibrium and fee-sharing characterizations valid through October 2026. The open target extends this to private/heterogeneous mempools, bribery costs, and adaptive attackers. The related-problems hook is budgeted interdiction of redundant service networks (network interdiction literature: an attacker with budget B removes components to maximize flow latency — the classic Max–Min interdiction and "redundancy vs. capacity" results there). No tight bounds are recorded for the heterogeneous-view case; the intuition is that unequal information weakens redundancy: if only one proposer can see the target transaction, redundancy in the proposer set buys nothing, and bribery cost collapses to one proposer's forgone fees.

## What would count as solving it?

1. A formal model: (n proposers, view hypergraph {V_i}, bribery costs b_i, adaptive censor with budget B, consensus randomness), with CR and TP defined as explicit functionals.
2. An upper bound: a censor strategy proving CR ≤ f(B, {V_i}, b_i) — i.e., censorship is cheap when views are skewed.
3. A lower bound: a protocol (redundancy/fee-sharing rule) proving CR ≥ g(·) for any budget-B censor, with g matching f up to constants — the tight frontier.
4. Characterization of the CR–TP Pareto frontier: for each redundancy level, the throughput cost of the achieved CR guarantee.
Partial results (e.g., the two-proposer case, or static-only censors) would be publishable intermediate steps.

## How to start

1. Reconstruct the shared-mempool baseline from Saraf et al. (2026) — the equilibrium and fee-sharing results are your benchmark; every theorem should degrade gracefully to them when V_i ≡ T.
2. Model the simplest nontrivial case: n = 2, V_1 ⊋ V_2, static censor. Compute CR as a function of view asymmetry; this already exposes how redundancy degrades.
3. Import machinery from budgeted interdiction: the censor's problem is a budgeted interdiction problem on the "who can include the transaction" hypergraph; existing hardness and approximation results there give both lower-bound techniques and algorithmic tools for the censor's side.
4. Add adaptivity last: the censor observing propagation before committing budget converts this to a Bayesian/sequential game; handle with dynamic-programming style arguments over slots.
Main obstacle: the coupled strategic layer (rational proposers choosing between fees and bribes) on top of the informational layer — most interdiction results assume non-strategic infrastructure.

## Key references

- Saraf et al. (2026), "Price of Censorship: Censorship Resistance and Throughput Under Rational Concurrent Proposers," DOI:10.4230/LIPIcs.AFT.2026.26 — establishes the censorship-resistance/throughput baseline with shared mempools, rational concurrent proposers, and fee-sharing equilibria; the direct predecessor to this problem. (Verify DOI; census-sourced, not independently confirmed.)
- Budgeted interdiction of redundant service networks (census "related" pointer; no full citation recorded — search the network-interdiction literature, e.g., Wood (1993) on interdiction and subsequent budgeted-redundancy work). (Verify.)
- For context on private orderflow/PBS: the MEV-Boost and proposer–builder separation literature (e.g., Ethereum PBS specs and orderflow-auction papers) — supplies the practical model of heterogeneous transaction views. (Verify specific papers.)

## Warnings

- The Saraf et al. citation is census-sourced and dated 2026; locate and read the actual paper before relying on any claimed result — the "known baseline" may differ from what the record implies.
- The problem statement is informal; the choice of CR metric (slot latency vs. per-slot inclusion probability) and bribery model materially changes the frontier. Fix conventions early and state them.
- Dead end to avoid: assuming symmetry across proposers — the entire point is heterogeneous views; shared-mempool intuitions about redundancy do not transfer.
- Adaptive attackers can strictly dominate static ones (wait to see which proposer has the view before paying); don't prove bounds only for static censors and claim them general.

---
*`BCGT-000310` · flagged for review*

## Acceptance guidance

1. A formal model: (n proposers, view hypergraph {V_i}, bribery costs b_i, adaptive censor with budget B, consensus randomness), with CR and TP defined as explicit functionals.
2. An upper bound: a censor strategy proving CR ≤ f(B, {V_i}, b_i) — i.e., censorship is cheap when views are skewed.
3. A lower bound: a protocol (redundancy/fee-sharing rule) proving CR ≥ g(·) for any budget-B censor, with g matching f up to constants — the tight frontier.
4. Characterization of the CR–TP Pareto frontier: for each redundancy level, the throughput cost of the achieved CR guarantee.
Partial results (e.g., the two-proposer case, or static-only censors) would be publishable intermediate steps.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/da6b5f06-8862-4f90-a6ab-6476e68ea377?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000310.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000310)
