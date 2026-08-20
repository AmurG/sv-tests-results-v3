# sv-tests results — CIRCT clean-dense, v3 (extended engine + four-state mode)

Full [sv-tests](https://github.com/chipsalliance/sv-tests) dashboard, **strictly additive on
[sv-tests-results-v2](https://amurg.github.io/sv-tests-results-v2/)**: every column of the v2
page is carried here from the v2 page's own logs — same logs, same grading, same provenance
statements, **zero verdict flips in either direction across all 20 carried columns** — plus
**four extended columns**, re-measured for this publish on the same harness, the same
stock report generator and the same grading pipeline:

**THE ARM LABELS CHANGED AT THIS PUBLISH: the four-state honest power-on arm is now the
ENGINE DEFAULT** (it is the arm the v2/v1 pages' `Arcilator` column publishes), and the
two-state-init arm is the documented opt-out. Column names are kept stable so deep links
survive; what flipped is which column IS the default:

| new column | engine arm | result | `uvm` cell |
|---|---|---|---|
| `circt_verilog_extended` | compile default (no four-valued flag) | **4960/5093** | 169/169 |
| `Arcilator_extended` | **two-state-init OPT-OUT arm** (pre-flip default; the reference arm) | **BOARD 4673/5092, HONEST 4643/5092** | **166/169** |
| `circt_verilog_extended_4state` | `--four-valued` compile arm | **4948/5093** (as rendered; 4952 strict — the renderer note below) | 167/169 |
| `Arcilator_extended_4state` | **THE DEFAULT ARM** (four-state honest power-on) | **BOARD 4819/5092, HONEST 4798/5092** | 163/169 |

- **Publication-posture change (this publish).** Earlier revisions of these pages linked every engine row to a public source-snapshot branch. From this publish the engine source is NOT published (ruling of record: public dashboards, nothing else public): the per-row provenance fields read `not published`, the tool-header links point at this results page itself, results provenance is retained privately, and the previously published snapshot branches are being retired. This is a link-posture change only — ZERO cell movement, gated by the publish verification (numbers re-derived unchanged on every column).

Movement against the previous (2026-08-15) v3 publish, per row: `circt_verilog_extended`
**+0** (zero flips), `circt_verilog_extended_4state` **+4** as rendered
(4 red-to-green, zero green-to-red), `Arcilator_extended` **+0** (zero flips —
the opt-out arm is row-for-row EXACT to the prior publish), `Arcilator_extended_4state`
**+42** (44 red-to-green and **2 green-to-red: `bp_multicore_4`, `bp_multicore_4_cce_ucode_cfg`** — both are LOUD
rc=71 budget timeouts on the honest-init default's measured runtime cost (~1.37x on the two
largest cores), both green on the opt-out column beside them; the giants bullet of the v2 page
carries the idle-replay numbers). Green-to-red is zero everywhere else.

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
  the two arms of one build — 17 rows green only on the opt-out arm and 163 green only on
  the default arm (page lens). The opt-out arm is the pre-flip default, retained as the
  reference for the arm-cost rows the v2 page names.

The page's distinct content is now the OPT-OUT column (`Arcilator_extended` — the two-state
arm the engine no longer defaults to, kept published as the reference arm) and the
`--four-valued` compile arm (`circt_verilog_extended_4state`); the `Arcilator_extended_4state`
column coincides with the default column by construction (identity measured above). Same harness environment as the v2 CIRCT columns
(`ARCILATOR_UVM_NO_STUB=1 CIRCT_UVM_SHIMS=1 CIRCT_UVM_FABRICATED_REPORTS=0`,
`OVERRIDE_TEST_TIMEOUTS=900` on the simulate columns, AOT compile-and-run only), same
denominators (5093 compile-lens / 5092 simulate-lens, `regress-vlg_pr587_iv`
harness-excluded, standing disclosure), same dual lens on the simulate columns (BOARD = the
rig's `report.csv` `Pass` column; HONEST = BOARD minus the case-leak rows, definition
unchanged — **30** rows on `Arcilator_extended`, **21** on `Arcilator_extended_4state`,
both re-derived fail-closed on these boards' own logs).

**Source disclosure — PUBLICATION POSTURE (changed at this publish, by ruling): the dashboards are public; the engine source is NOT published.** The four extended columns were built from the campaign's engine tree of 2026-08-19; results provenance — the exact source tree, build receipts and board banks — is retained privately, every number is reproducible from that retained record, and nothing on this page implies the source is available (each row's `circt_ref` reads `not published`; earlier revisions' public snapshot branches are being retired). Two statements, both measured:

1. **The retained provenance tree IS the exact build tree.** Its tree is bit-identical to the source the
   boards' binaries were built from (tree-hash equality verified at staging); the
   earlier publishes' disclosure rewrites are part of this line's history now.
2. **Lineage:** the privately retained provenance line is linear — these columns are strictly
   downstream of every earlier publish's build, not a side line. The carried v2 table on
   this page is the v2 page's own fresh measurement at this same build.

**Four-state mover — the series continues, and its biggest step is THE FLIP ITSELF.**
`Arcilator_extended_4state` 4564 → 4777 → **4819** (this publish +42 BOARD,
+43 HONEST). The previous publish's
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
| W26 → this publish (suite merge) | **+4 report lens only** | upstream `b5685cbe` removed the `:assert:` marker the report lens mis-scored on the sequence quartet (SCORING change; strict EXACT; the retirement paragraph below). `tests/` is now byte-identical to upstream `c4229f3b`. |

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
actually been failing this arm was the W25 clock-reload defect above, now cured. At this publish the four are green on the page lens as well — upstream's
suite update removed the expectation marker the renderer's predicate was mis-scoring
(the segments table above). What DOES remain engine-red on the
default arm alone: the two deferred-assert rows (`assert0_test_uvm`, `assert_final_test_uvm`,
green on the opt-out column beside it) — the named open item of the flip, disclosed on the v2
page's default-arm bullet as well.

**Four-state mode cost (the `_4state` columns vs the default-arm columns, same rig, same
rowset), and it has collapsed.** The four-state mode trades rows for value fidelity, and
every lost row still fails LOUDLY:

| lens | G→R at 2026-08-15 | G→R now | R→G at 2026-08-15 | R→G now |
|---|---|---|---|---|
| compile (four-valued arm vs the compile default, strict grading) | 8 | **8** (all named in the campaign records) | 0 | 0 |
| simulate (the DEFAULT arm vs the two-state opt-out arm, strict grading) | 18 | **16** | +123 | **+163** |

**The silent-wrong bucket — a lost row that exits 0 with a clean transcript — is EMPTY on both
lenses.** On the simulate lens the four-state arm — now the DEFAULT — sits **147 rows above**
the opt-out arm, where at the previous publish it sat 105 above and at 2026-08-06 it sat 76
below. The 16 strict-lens simulate losses are the arm's named cost set (2 budget giants, 2
deferred-assert rows, 12 honest-initialization rows), classified row-by-row on the v2 page's
default-arm bullet.

**Renderer note (the stock renderer's own extra predicate).** For `mode: simulation` rows the
stock report generator applies a transcript predicate the strict grader does not, so a few
rows that exit 0 and pass by transcript are denied on the page. Re-measured by name at this
publish: **4** rows on `circt_verilog_extended_4state` — `property_disable_iff_test`, `property_local_var_test`, `sequence_local_var_test`, `sequence_stable_test_uvm` —
**1** on `Arcilator_extended` (`basic-tagged-union`), **2** on
`Arcilator_extended_4state` (`basic-tagged-union`, `sequence_stable_test_uvm` — the strict-green report-lens residue discussed above), and **0** rows in the other direction on any column. **This
page publishes the lower, as-rendered number for every column** rather than patching the table
against its own renderer; the strict-graded counts for the same logs are 4952
(`circt_verilog_extended_4state`), 4674 (`Arcilator_extended`) and 4821
(`Arcilator_extended_4state`), while `circt_verilog_extended` has no seam and is 4960 either way.

**Artefact disclosure — how these logs differ from the previous publish's.** The previous
publish's finding stands and is carried: its frontend bump added diagnostic lines on ~19.6% of
rows, dominantly byte-identical repeats — those lines are still in these logs. THIS publish's
interval (2026-08-15 page → this page) was measured page against rendered page with volatile
fields normalized (this lane's `logdelta` receipt):

```
(log BODY only; page chrome, volatile metadata, command echoes, local paths and the publication redaction token normalized out; vs the LIVE v3 page)
circt_verilog_extended: rows-compared=5093 log-body-identical=5089 log-body-diff=4 diag-line-set-gained-rows=2 diag-line-set-lost-rows=1
circt_verilog_extended_4state: rows-compared=5093 log-body-identical=5080 log-body-diff=13 diag-line-set-gained-rows=11 diag-line-set-lost-rows=5
Arcilator_extended: rows-compared=5092 log-body-identical=2499 log-body-diff=2593 diag-line-set-gained-rows=2590 diag-line-set-lost-rows=1
Arcilator_extended_4state: rows-compared=5092 log-body-identical=2477 log-body-diff=2615 diag-line-set-gained-rows=2587 diag-line-set-lost-rows=7
dominant gained-line cause carried from the premerge measurement: the per-row AOT-compile 'assertFailCount' C-linkage warning (W25 assert-exit header) + its warnings-counter bump; verdict-neutral, header cleanup filed. The suite-merge rows additionally change text on the 5 merged files' pages.
```

The default-arm columns' content movement is the flip's own class (power-on constants and SSA
renumbering in the recorded pipeline output, plus the rows the flip genuinely moved); the
opt-out arc column is verdict-EXACT to the prior publish and its content deltas are the
standing wave-over-wave nondeterministic rows plus one diagnostic-only remark. Verdict deltas
on this account are exactly the flip ledger above — nothing else moved.

**Carried-table note (a stock-renderer nondeterminism, named).** The carried columns' *log
content* comes from the V18 v2 page's own logs unchanged (the v2 stage committed beside this page), and every carried verdict and exit code
matches it. The rendered pages are not byte-identical to the v2 page's, and the reason was
chased rather than waived: the stock report generator renders each row's `tags` field from an
unordered set, so the same tags appear in a different order between two runs. Over a
2,400-page sample of six carried columns, 960 pages differ byte-wise and **all 960 differences
disappear once the `tags` field alone is order-normalised — zero residual**. The same property
holds between the previously shipped pages; it is a generator artefact, not a content change.

**Grading.** The same fail-closed strict grading and case-leak scanner as the v2 page, both
re-run here on these boards' own logs: the `Arcilator_extended` (opt-out) leak set is the
standing **30**, row for row; the `Arcilator_extended_4state` (default) set is **21**,
re-derived on this arm's own logs — the leak population is arm-dependent and is re-derived,
never carried. The case-leak deduction defines the HONEST lens in the table above. The rig's
report-vs-strict divergence on the default arm (4819 vs 4821) is the renderer-lens
artifact disclosed above, stated on the v2 page as well.

**What this page does not claim.** The extended columns are not an upstream-CIRCT measurement
(the published snapshot is a fork tree, cited above); the four-state arm is the ENGINE DEFAULT
at this publish (no longer opt-in) and its row cost against the retained opt-out arm is stated
row-by-row rather than averaged away; rows red here for harness or renderer-lens reasons are
disclosed as such rather than re-rolled. For the all-stock baseline see
[sv-tests-results-v1](https://amurg.github.io/sv-tests-results-v1/); for the overlay board
and all standing column disclosures see
[sv-tests-results-v2](https://amurg.github.io/sv-tests-results-v2/).
