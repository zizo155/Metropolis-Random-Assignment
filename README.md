# Metropolis-Hastings for the Random Assignment Problem

A Monte Carlo study of the random assignment problem: given an n×n cost
matrix with entries drawn uniformly from [0,1], find the permutation
that minimises total assignment cost, and examine how that minimum
behaves as n grows.

## Background

The assignment problem asks for the minimum-cost bijection between n
tasks and n resources. Brute force is O(n!), so the search space becomes
intractable quickly. This project approaches it with Metropolis-Hastings:
starting from a random permutation, proposing transpositions of two
assignments, and accepting each proposal with probability

    min(1, exp(-β · Δcost))

where β is an inverse temperature. Lower-cost proposals are always
accepted; higher-cost ones are accepted with a probability that falls as
β rises, so the chain shifts from exploration to exploitation over the
course of a run.

The quantity of interest is the expected minimum cost as n → ∞. Aldous
(2001) proved this converges to ζ(2) = π²/6 ≈ 1.645 — notably, a
constant, not something growing with n. The simulation tests how quickly
that limit is approached.

## Method

- Cost matrices drawn i.i.d. uniform on [0,1]
- Proposals: swap two entries of the current permutation
- Annealing: β increased over the course of each run
- n ∈ {5, 10, 15, 20, 25, 30}, 1000 chain steps, 100 independent
  simulations per n, averaged
- Runs parallelised across cores with `joblib`

## Results

Average minimal cost is plotted against n, with π²/6 marked as a
reference line.

## Setup

    pip install -r requirements.txt

Then open `probability.ipynb` and run all cells.

## Notes

The annealing schedule multiplies β by (1 + t/N) at every step rather
than incrementing it, so β compounds and grows extremely fast — the
chain becomes effectively greedy within the first hundred or so
iterations. An additive schedule (β ← β₀·(1 + t/N)) would give the
intended gradual cooling and is worth comparing against.

For a stronger benchmark, the exact optimum can be computed in
polynomial time with the Hungarian algorithm
(`scipy.optimize.linear_sum_assignment`), which would turn the
approximation gap into a measurable quantity rather than an assumed one.

## Reference

Aldous, D. (2001). The ζ(2) limit in the random assignment problem.
*Random Structures & Algorithms*, 18(4), 381–418.

---

**Zohreh Taghibakhshi** · [GitHub](https://github.com/zizo155)
