# Goodman's formula and supersaturation for two-colourings

[![CI](https://github.com/Egly443/lean-goodman-supersaturation/actions/workflows/ci.yml/badge.svg)](https://github.com/Egly443/lean-goodman-supersaturation/actions/workflows/ci.yml)

A Lean 4 library counting monochromatic structures in red/blue colourings of complete graphs:
Goodman's formula for monochromatic triangles with its sharp lower bound, monochromatic books,
edge balance, and supersaturation for cliques, both by averaging over Ramsey-sized sets and by a
counted version of the Erdős–Szekeres recursion.

It is a **Mathlib-ready strawman**, written to Mathlib's conventions and offered for reuse as is,
by people and by AI systems. Anyone in the Mathlib community may upstream it, in whole or in part,
if and when they choose. The author does not plan to drive it through Mathlib review.

**Provenance:** AI-generated with human guidance. Every file:

* builds with no `sorry` and no warnings;
* uses only the standard axioms (`propext`, `Classical.choice`, `Quot.sound`);
* passes Mathlib's `#lint`, `runLinter` and `lint-style`, and the `linter.mathlibStandardSet`
  linters;
* depends only on Mathlib;
* meets [LeanPool](https://github.com/LeanPool/lean-pool)'s content rules (file headers, no
  waivers or `set_option`).

Licensed under Apache 2.0.

A colouring is a graph `G` (red) and its complement `Gᶜ` (blue). Most statements come in two forms:
inside an arbitrary finite vertex set `S` (the form recursive Ramsey arguments need), and for the
whole finite vertex type.

## Using it

The library is pinned to Lean and Mathlib `v4.35.0-rc3` (see `lean-toolchain` and
`lakefile.toml`). To depend on it, add to your `lakefile.toml`:

```toml
[[require]]
name = "goodman_supersaturation"
git = "https://github.com/Egly443/lean-goodman-supersaturation"
rev = "main"
```

and `import GoodmanSupersaturation`, or import individual modules. To build it, run
`lake exe cache get` and then `lake build`.

Releases are tagged `vX.Y.Z` from `main` by the manually triggered `Release` workflow
(`.github/workflows/release.yml`).

## Contents

| File | Main results |
|---|---|
| `GoodmanSupersaturation/Goodman.lean` | `SimpleGraph.degreeWithin G S v` and `SimpleGraph.cliqueCountIn G S k` (degrees and clique counts inside `S`), with `degreeWithin_add_degreeWithin_compl` (`d + d' + 1 = \|S\|`), the `univ` simp lemmas, and the bridges to Mathlib's induced graph `degreeWithin_eq_degree_induce` and `cliqueCountIn_eq_card_cliqueFinset_induce` (the local counts are the degrees and clique counts of `G.induce S`). **Goodman's formula** `goodman_within` / `goodman`: `2 (T + T') + ∑ v, d v · d' v = 2 C(n, 3)`. The degree-imbalance form `goodman_sq_within` / `goodman_sq`: `8 (T + T') + n (n - 1)² = 8 C(n, 3) + ∑ v, (d v - d' v)²`. **Goodman's bound** `goodman_bound_within` / `goodman_bound`: `n (n - 1) (n - 5) ≤ 24 (T + T')`, with equality for any graph regular of degree `k` on `2k + 1` vertices (`IsRegularOfDegree.goodman_eq`; such graphs exist only for even `k`, that is `n ≡ 1 (mod 4)`); for even `n` the stronger `n (n - 2) (n - 4) ≤ 24 (T + T')` (`goodman_bound_of_even_card`); every colouring of `K_6` has two monochromatic triangles (`two_le_card_cliqueFinset_add_card_cliqueFinset_compl`). **Monochromatic books** `exists_monochromatic_book`: if `4q + 2 ≤ n`, some edge has `q` common neighbours in its own colour. **Edge balance** `sq_two_mul_sub_le_card_mul_sum_sq`: `(2 (e - e'))² ≤ n ∑ v, (d v - d' v)²`. Also `degree_add_degree_compl`. |
| `GoodmanSupersaturation/Supersaturation.lean` | The averaging lemma `Finset.choose_le_card_mul_choose`: if every `m`-subset of `s` contains a member of a family `𝓕` of `t`-sets, then `C(\|s\|, t) ≤ \|𝓕\| C(m, t)`. **Supersaturation by averaging** `choose_le_card_cliqueFinset_add_mul_choose`: if every `m`-set contains a monochromatic `K_t`, there are at least `C(n, t) / C(m, t)` of them. **Counted Erdős–Szekeres recursion** `supersaturation`: inside any `S` with `x = \|S\| + 1 - 2^(k+l-3) > 0`, `G` has at least `supersaturationBound k l x = 2^(-E(k,l)) x^k / k!` `k`-cliques or `Gᶜ` has at least `supersaturationBound l k x` `l`-cliques, where `E(k, l) = k (l - 2) + C(k + 1, 2) - 1`; `supersaturation_diag` counts both colours. Also `cliqueCountIn_mono`, `cliqueCountIn_one`, and the rooted double count `sum_cliqueCountIn_neighbors_eq` (equality; `_le` its inequality form). |

Goodman's formula is proved by summing one pointwise identity over ordered triples of distinct
vertices: for each triple, the six mixed-wedge indicators (one per ordering) plus twice the
monochromatic indicator equal `2`. Each ordering sums to the same total by relabelling, so no case
analysis on unordered triples is needed. The counted recursion keeps the shape of the
Erdős–Szekeres proof but tracks counts: at least half the vertices have large degree in one
colour, the induction hypothesis applies inside each such neighbourhood, and the rooted double
count turns rooted cliques into cliques.

## Notes for anyone upstreaming to Mathlib

* **Placement.** `Goodman.lean` would go to `Mathlib/Combinatorics/SimpleGraph/Goodman.lean`, or
  next to `Mathlib/Combinatorics/SimpleGraph/Triangle/Counting.lean`. `Supersaturation.lean` would
  start `Mathlib/Combinatorics/SimpleGraph/Ramsey/Supersaturation.lean`.
* **Lemmas that belong elsewhere.** `Finset.choose_le_card_mul_choose` is pure set-family double
  counting and belongs in `Mathlib/Combinatorics/Enumerative/DoubleCounting.lean`.
  `degree_add_degree_compl` belongs in `Finite.lean` next to `degree_compl`.
* **Names.** `degreeWithin` (not `degreeIn`, a name LeanPool's Sabidussi project uses for another
  notion) and `cliqueCountIn`. `supersaturationBound` names the explicit function rather than an
  asymptotic.
* **Overlap with Mathlib.** None. Mathlib's `degree_compl` is used for the global degree identity.
* **Design questions.** The local forms take a `Finset V` instead of the induced subgraph `G.induce
  S`, because recursive arguments restrict to neighbourhoods repeatedly and filters compose
  without changing the vertex type. Counts are stated division-free in `ℕ` or `ℤ`.
* **Older Mathlib.** `Mathlib.Data.Real.Basic` is deprecated in `v4.35`, so the real numbers are
  imported through `Mathlib.Tactic.Rify`, which exists in both versions. `push_neg` and `if_false`
  are avoided (deprecated in one version or the other).

## Relation to other libraries

[LeanPool](https://github.com/LeanPool/lean-pool) and TauCeti have no Goodman or two-colour supersaturation results (LeanPool's
`multicolor-triangle-ramsey` project bounds multicolour triangle Ramsey numbers).
[lean-ramsey](https://github.com/Egly443/lean-ramsey) has Ramsey numbers but no counting. A Ramsey
bound for `K_t` (from lean-ramsey, or `RamseyBound` in [lean-ramsey-reservoir](https://github.com/Egly443/lean-ramsey-reservoir)) supplies the
hypothesis of `choose_le_card_cliqueFinset_add_mul_choose`; this package does not depend on
either.

## Not included

* Goodman's exact minimum for every `n` (`C(n, 3) - ⌊n/2 ⌊((n-1)/2)²⌋⌋`): the bounds here are the
  smooth form, attained when `n ≡ 1 (mod 4)` (regular colourings of degree `(n - 1)/2`), and the
  even-order form `goodman_bound_of_even_card`. The case `n ≡ 3 (mod 4)` and the matching
  constructions for even `n` are not formalised.
* Higher cliques (Goodman-type bounds for `K_4` and the Erdős conjecture that random colourings
  minimise monochromatic `K_t`, which is false for `t ≥ 4`): out of scope.
* Research refinements (support-weighted book bounds, book deficits, cross-degree moments): they
  serve particular Ramsey arguments and are not standard results.
* Book Ramsey numbers beyond the averaging bound `(n - 5)/4`.

## Citation

The mathematics is from the works below. BibTeX entries for Mathlib's `docs/references.bib` are in
`references.bib`. Each was checked against zbMATH, carries its identifier, and the file is
normalised with the `bibtool` command from Mathlib's `scripts/lint-bib.sh`.

* A. W. Goodman, *On sets of acquaintances and strangers at any party*, Amer. Math. Monthly 66
  (1959), 778–783, doi:10.2307/2310464.
* P. Erdős, G. Szekeres, *A combinatorial problem in geometry*, Compos. Math. 2 (1935), 463–470.
