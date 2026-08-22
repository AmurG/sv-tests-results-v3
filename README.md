# sv-tests results — CIRCT clean-dense, v3 (extended engine + four-state mode)

Full [sv-tests](https://github.com/chipsalliance/sv-tests) dashboard, **strictly additive on
[sv-tests-results-v2](https://amurg.github.io/sv-tests-results-v2/)**: every column of the v2
page is carried here from the v2 page's own logs — same logs, same grading, same provenance
statements, **zero verdict flips in either direction across all 20 carried columns** — plus
**four extended columns**, re-measured for this publish on the same harness, the same
stock report generator and the same grading pipeline:

**THE ARM LABELS (standing since the 2026-08-19 publish): the four-state honest power-on arm
is the ENGINE DEFAULT** (it is the arm the v2/v1 pages' `Arcilator` column publishes), and the
two-state-init arm is the documented opt-out. Column names are kept stable so deep links
survive. **NEW AT THIS PUBLISH: BOARD ≡ HONEST on every simulate column** — the case-leak grader
defect behind the dual-lens split is FIXED at the runner and the scanner reads ZERO on these
boards' own logs (the grading section below; the give-back rows are in the movement ledger):

| new column | engine arm | result | `uvm` cell |
|---|---|---|---|
| `circt_verilog_extended` | compile default (no four-valued flag) | **4998/5093** | 169/169 |
| `Arcilator_extended` | **two-state-init OPT-OUT arm** (pre-flip default; the reference arm) | **4673/5092** — BOARD ≡ HONEST | **166/169** |
| `circt_verilog_extended_4state` | `--four-valued` compile arm | **4993/5093** (rendered == strict at this publish — the renderer note below) | 169/169 |
| `Arcilator_extended_4state` | **THE DEFAULT ARM** (four-state honest power-on) | **4829/5092** — BOARD ≡ HONEST | 164/169 |

- **Publication posture (standing since the 2026-08-19 publish): the dashboards are public; the engine source is NOT published.** The per-row provenance fields read `not published`, the tool-header links point at this results page itself, and results provenance is retained privately; every number on this page is reproducible from that retained record.

- **Three grading/engine changes at this publish, disclosed in full on the [v2 page](https://amurg.github.io/sv-tests-results-v2/) and measured on THESE boards too:** (1) the **case-leak grader fix** — a printed self-reported failure verdict now demotes the row at scoring time; the scanner reads ZERO on all four extended columns and the give-back rows are the green-to-red sets in the movement ledger below (30 on the opt-out column, 21 on the default column — grader honesty, zero engine regressions); (2) the **timing-annotation compat hatch** — `--ignore-timing-annotations` rides both CIRCT frontends' invocations (documented flag; per-site warnings plus a mandatory once-per-run summary; a green under it is a zero-path-delay simulation, stated plainly); the once-per-run notice is present in 37/37 logs of the two compile columns and 37/37 logs of the two simulate columns here; (3) **assertion pass-action vacuity** — implication properties now report per obligation and pass actions execute on nonvacuous successes only, a DOCUMENTED reference-aligned deviation from the IEEE 1800-2023 20.11 default sentence (aligned with every external reference the campaign has banked for this construct; restorable via `$assertcontrol`; zero verdict flips on these boards on this account).

Movement against the previous (2026-08-19) v3 publish, per row: `circt_verilog_extended`
**+38** (zero green-to-red: 37 timing-annotation-class rows + `ibex`, the static-initializer
cure of this interval), `circt_verilog_extended_4state` **+45** as rendered (zero green-to-red:
the same 38-row mechanism, plus `uart_example` + two asicworld rows recovered by this interval's
engine picks, plus the 4 sequence/property rows the renderer's fixed parseLog no longer
mis-scores — the renderer note below; the rendered and strict counts now AGREE), `Arcilator_extended`
**+0 net** (the ledger is NOT empty: 30 green-to-red — exactly the case-leak give-back set of this
column — and 30 red-to-green: 29 timing-annotation-class rows + `ibex`), and
`Arcilator_extended_4state` **+10** (31 red-to-green — 29 timing-annotation-class + `ibex` +
`sequence_stable_test_uvm` on the page lens — and 21 green-to-red, exactly this column's
case-leak give-back set). Every green-to-red on this page is a case-leak give-back row;
zero engine regressions (re-grading the prior boards' banked logs under the fixed grader
flips the same sets — adjacent A/B, receipted).

**How the extended columns relate to the v2 table at this publish — the identity pairing
FLIPPED with the default.** The carried v2 table and the extended columns are the SAME engine
generation again (one tip, one snapshot), and the identity now runs through the four-state
column, measured on this page's own CSV:

* `circt_verilog_extended` vs the page's own `circt_verilog`: **per-row identical** — 0 verdict
  differences over 5,093 rows (the compile default carries no four-valued flag and is untouched
  by the power-on flip).
* `Arcilator_extended_4state` vs the page's own `Arcilator` (the v2 page's default column):
  **per-row identical** — 0 verdict differences over 5,092 rows. THE FOUR-STATE COLUMN IS THE
  DEFAULT COLUMN NOW; it is kept here so the `_4state` series stays continuous.
* `Arcilator_extended` (the opt-out arm) vs the page's own `Arcilator`: the flip ledger between
  the two arms of one build — 17 rows green only on the opt-out arm and 173 green only on
  the default arm (page lens; identical counts on the strict ledger). The opt-out arm is the
  pre-flip default, retained as the reference for the arm-cost rows the v2 page names.

The page's distinct content is now the OPT-OUT column (`Arcilator_extended` — the two-state
arm the engine no longer defaults to, kept published as the reference arm) and the
`--four-valued` compile arm (`circt_verilog_extended_4state`); the `Arcilator_extended_4state`
column coincides with the default column by construction (identity measured above). Same harness environment as the v2 CIRCT columns
(`ARCILATOR_UVM_NO_STUB=1 CIRCT_UVM_SHIMS=1 CIRCT_UVM_FABRICATED_REPORTS=0`,
`OVERRIDE_TEST_TIMEOUTS=900` on the simulate columns, AOT compile-and-run only), same
denominators (5093 compile-lens / 5092 simulate-lens, `regress-vlg_pr587_iv`
harness-excluded, standing disclosure). The dual lens is RETIRED at this publish: the
case-leak scanner re-run fail-closed on these boards' own logs reads **ZERO** on both
simulate columns — BOARD ≡ HONEST everywhere on this page (the grader fix in the bullet
above; the HONEST-lens history is preserved in the prior revisions).

**Source disclosure — PUBLICATION POSTURE (standing, by ruling): the dashboards are public; the engine source is NOT published.** The four extended columns were built from the campaign's engine tree of 2026-08-22; results provenance — the exact source tree, build receipts and board banks — is retained privately, every number is reproducible from that retained record, and nothing on this page implies the source is available (each row's `circt_ref` reads `not published`; the earlier revisions' public snapshot branches are retired). Two statements, both measured:

1. **The retained provenance tree IS the exact build tree.** Its tree is bit-identical to the source the
   boards' binaries were built from (tree-hash equality verified at staging); the
   earlier publishes' disclosure rewrites are part of this line's history now.
2. **Lineage:** the privately retained provenance line is linear — these columns are strictly
   downstream of every earlier publish's build, not a side line. The carried v2 table on
   this page is the v2 page's own fresh measurement at this same build.

**Four-state mover — the series continues.**
`Arcilator_extended_4state` 4564 → 4777 → 4819 → **4829** (this publish +10 BOARD; against the
prior HONEST 4798 the like-for-like step under the unified lens is +31). The 2026-08-15 publish's
mover was a *harness correction* and said so (58 of 66 new greens were a corrupted four-state
driver generator); that correction is already inside the 4564 baseline, so none of this +213
is rig. The interval splits into three measured segments:

| segment | Δ | what |
|---|---|---|
| 2026-08-06 → W22 | **+210** | four-valued **aggregate / net-type lowering** (+156: ref remat, union→slice, LTL clock-root, observe-variables), 15 cures (+24), 16 cures across 8 lanes (+14), re-landed + burn cures and the SplitLoops aggregate-element projection (+7/+2/+2), and **+1 that is NOT engine** — one large-core row oscillating against the 900 s budget. **Itemisation residue, stated: those items sum to 206, so 4 of the 210 are unattributed in the campaign's own decomposition.** |
| W22 → W23 | **+1 board, +4 honest** | x-semantics: an out-of-range dynamic select now reads `x` **per bit** (IEEE 1800-2023 §11.5.1). One row flips green; three more stop being green-while-printing-`Failed`, which is where the extra honest rows come from. |
| W23 → W24 | **+2** | a SystemVerilog event trigger now wakes an `always @(e)` block parked before the first trigger — `event_control_simulation_minimal` and `regress-sv_br_gh508a_iv`, on **both** arms. |
| W24 → W25 (2026-08-18) | **+1 board / +1 honest** | ⭐ **the clock-reload cure**: on the four-valued arm every register on a PROCESS-DRIVEN clock had committed ONE EVALUATION LATE (an end-of-eval reload defect reaching every plain testbench clock generator); cured, with the historical caveat below. |
| W25 → W26 | **+38** | ⭐ **the DEFAULT FLIP lands**: the honest power-on arm becomes the engine default; the 38-row recovery list was pre-registered from the historical flag boards and reproduced row-for-row (38/38 expected-hit, zero unexpected movers), minus the two named budget giants. |
| W26 → the 2026-08-19 publish (suite merge) | **+4 report lens only** | upstream `b5685cbe` removed the `:assert:` marker the report lens mis-scored on the sequence quartet (SCORING change; strict EXACT; the retirement paragraph below). `tests/` is byte-identical to upstream `c4229f3b` (unchanged at this publish). |
| the 2026-08-19 publish → this publish (W27+W28 waves + the grader honesty pass) | **+10 board / +31 vs prior honest** | ⭐ the case-leak GRADER cure (gives back the 21 leak rows of this column — BOARD ≡ HONEST from this publish on), the documented timing-annotation compat hatch (+29 on this column, zero-path-delay caveat stated), the static-initializer cure (`ibex`), and the renderer parseLog scoping fix (`sequence_stable_test_uvm` back on the page lens). |

**HISTORICAL-SERIES CAVEAT (travels with every comparison on this table):** the `_4state`
series crosses the W25 clock-reload cure — numbers published for this arm BEFORE 2026-08-18
were measured with every process-driven-clock register committing one evaluation late, so
cross-publish comparisons of this column are NOT like-for-like across that cure.

Row families across the interval: struct / packed-array / implicit-cast /
net-type-and-strength (`tri`, `supply`, `uwire`) / assertion-block / large-core, plus the
x-semantics families in the second segment. **Stated gap:** the first segment crosses a
test-driver generator succession never isolated by its own board; it is bounded at ~zero
(green-to-red is 0 end to end and no row of its known verdict-moving class is among the
movers) — and bounded is not measured.

**The previous publish's "cure does not reach the four-state arm" disclosure is RETIRED —
by measurement, not by fiat.** On this build the `sequence_rose/fell/past/changed_test_uvm`
family is **strict-green on the four-state arm** (4/4 by the strict ledger of
these very logs): the Reactive-region NBA cure reaches the four-valued lowering, and what had
actually been failing this arm was the W25 clock-reload defect above, now cured. The four are green on the page lens as well (since the 2026-08-19 publish),
and at THIS publish `sequence_stable_test_uvm` joins them there too — the renderer's
parseLog evaluation is now scoped to non-diagnostic context (the renderer note below).
What DOES remain engine-red on the default arm alone: the two deferred-assert rows (`assert0_test_uvm`, `assert_final_test_uvm`,
green on the opt-out column beside it) — the named open item of the flip, disclosed on the v2
page's default-arm bullet as well.

**Four-state mode cost (the `_4state` columns vs the default-arm columns, same rig, same
rowset), and it has collapsed.** The four-state mode trades rows for value fidelity, and
every lost row still fails LOUDLY:

| lens | G→R at 2026-08-15 | G→R now | R→G at 2026-08-15 | R→G now |
|---|---|---|---|---|
| compile (four-valued arm vs the compile default, strict grading) | 8 | **5** (all named in the campaign records; 3 of the 8 recovered by this interval's engine picks) | 0 | 0 |
| simulate (the DEFAULT arm vs the two-state opt-out arm, strict grading) | 18 | **17** | +123 | **+173** |

**The silent-wrong bucket — a lost row that exits 0 with a clean transcript — is EMPTY on both
lenses.** On the simulate lens the four-state arm — the DEFAULT — sits **156 rows above**
the opt-out arm, where at the 2026-08-19 publish it sat 147 above, at 2026-08-15 it sat 105 above
and at 2026-08-06 it sat 76 below. The 17 strict-lens simulate losses are the arm's named cost
set (2 budget giants, 2 deferred-assert rows, 13 honest-initialization rows — the count moves
12 → 13: `sequence_stable_test_uvm` left the set page-lens-recovered and `regress-vlg_pr2728032_iv`
entered it, a case-leak row this arm had been over-credited on), classified row-by-row on the v2
page's default-arm bullet.

**Renderer note (the stock renderer's own extra predicate — now SCOPED).** For `mode: simulation`
rows the stock report generator applies a transcript predicate the strict grader does not; at
this publish's rig that predicate's oracle evaluation is SCOPED to non-diagnostic context (a
grader fix, validated against the strict ledger). Re-measured by name at this publish: **0** rows
on both compile columns (the prior publish's 4-row `circt_verilog_extended_4state` seam is GONE —
rendered and strict agree at 4993), and exactly **1** row on each simulate column — `basic-tagged-union`
(report-red/strict-green on both arms) — with **0** rows in the other direction. **This page
publishes the lower, as-rendered number for every column**; the strict-graded counts are 4993
(`circt_verilog_extended_4state`), 4674 (`Arcilator_extended`) and 4830
(`Arcilator_extended_4state`), while `circt_verilog_extended` has no seam and is 4998 either way.

**Artefact disclosure — how these logs differ from the previous publish's.** The standing frontend
finding is carried: the 2026-08-15→19 interval's slang bump added dominantly repeated diagnostic
lines on ~15-20% of rows; those lines remain. THIS publish's interval adds no frontend bump; its
measured log-shape changes are: (a) the timing-annotation once-per-run notice and per-site warnings
(present in 37 / 37 logs of the compile columns and 37 / 37 logs of the simulate
columns); (b) the fixed runner's demotion rc on the case-leak rows (their logs now carry the
honest failing rc the give-back ledger reflects); (c) per-obligation SVA verdict reporting on
assertion-bearing rows (implication verdicts at their own completion ticks, nonvacuous-only pass
actions — the vacuity disclosure above). Verdict deltas on this account are exactly the movement
ledger above — nothing else moved.

**Carried-table note (a stock-renderer nondeterminism, named).** The carried columns' *log
content* comes from the v2 page's own logs unchanged (the v2 stage committed beside this page), and every carried verdict and exit code
matches it. The rendered pages are not byte-identical to the v2 page's, and the reason was
chased rather than waived: the stock report generator renders each row's `tags` field from an
unordered set, so the same tags appear in a different order between two runs. Over a
2,400-page sample of six carried columns, 960 pages differ byte-wise and **all 960 differences
disappear once the `tags` field alone is order-normalised — zero residual**. The same property
holds between the previously shipped pages; it is a generator artefact, not a content change.

**Grading.** The same fail-closed strict grading and case-leak scanner as the v2 page, both
re-run here on these boards' own logs — and the case-leak scanner reads **ZERO** on both
simulate columns: the grader defect that defined the HONEST lens is FIXED at the runner
(a printed self-reported failure verdict demotes unconditionally at scoring time), the historical
leak sets (30 opt-out / 21 default) flip green-to-red in the movement ledger above as the
give-back, and BOARD ≡ HONEST everywhere on this page. The rig's report-vs-strict divergence
on the simulate columns (4829 vs 4830 / 4673 vs 4674, exactly `basic-tagged-union`) is the
renderer-lens artifact disclosed above, stated on the v2 page as well.

**What this page does not claim.** The extended columns are not an upstream-CIRCT measurement
(they measure the campaign's fork engine; the source is not published — the posture above); the
four-state arm is the ENGINE DEFAULT (standing since 2026-08-19) and its row cost against the retained opt-out arm is stated
row-by-row rather than averaged away; rows red here for harness or renderer-lens reasons are
disclosed as such rather than re-rolled. For the all-stock baseline see
[sv-tests-results-v1](https://amurg.github.io/sv-tests-results-v1/); for the overlay board
and all standing column disclosures see
[sv-tests-results-v2](https://amurg.github.io/sv-tests-results-v2/).
