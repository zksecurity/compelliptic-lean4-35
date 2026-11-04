# Lean 4.35 compatibility branch

This branch preserves the CompElliptic development at upstream
`daira/CompElliptic` commit `2c0444035a84db957f27f06433715058d1e890ad`
while rebuilding it with Lean 4.35.0, CompPoly `v4.35.0` and that release's
Mathlib dependency.
The original upstream Git history and dual license are retained.

The package enables Lean's `backward.do.legacy` and
`backward.isDefEq.respectTransparency = false` compatibility options. These
preserve the previous elaborator's loop lowering and implicit-unification
behavior. They do not disable kernel checking or add proof axioms.

The source port updates a moved Mathlib import and deprecated conditional
lemma names. It does not change theorem hypotheses or conclusions. Native
arithmetic remains opt-in under the original package structure; this port
introduces no new native proof leaves.

Build with the checked-in toolchain and lockfile:

```sh
lake exe cache get
lake build
```

The exact computability census has one explicit dependency change. Mathlib
4.35's `Finset.instSetLike` uses `Classical.choice` in its injectivity proof.
`Fintype.complete` now uses that membership instance, so `Fintype`,
`TonelliShanks` and `SSWUParams` carry this erased proof dependency in their
types. Their nine generic branch-cover arithmetic definitions therefore use
the existing `assert_computable ... +choice` mode, with no blanket allowance.
It still rejects unsafe, partial and noncomputable definitions and compiled-body
substitutions; it also rejects a redundant `+choice` flag. The arithmetic data
remains computed by plain definitions. The corresponding 4.30 `Fintype.card`
and `TonelliShanks` axiom lists had only `propext`/`Quot.sound`; on 4.35 they
also include standard `Classical.choice`. No new axiom declaration is added.
