# sv-tests results — CIRCT clean-dense, v3 (extended engine + four-state mode)

Full [sv-tests](https://github.com/chipsalliance/sv-tests) dashboard, **strictly additive on
[sv-tests-results-v2](https://amurg.github.io/sv-tests-results-v2/)**: every column of the v2
page is carried here from the v2 page's own logs — same logs, same grading, same provenance
statements, **zero verdict flips in either direction across all 20 carried columns** — plus
**four extended-engine columns**, re-measured for this publish on the same harness, the same
stock report generator and the same grading pipeline:

| new column | engine arm | result | `uvm` cell |
|---|---|---|---|
| `circt_verilog_extended` | extended line, default (two-state) | **4960/5093** | 169/169 |
| `Arcilator_extended` | extended line, default (two-state) | **BOARD 4673/5092, HONEST 4643/5092** | **166/169** |
| `circt_verilog_extended_4state` | extended line, four-state mode | **4944/5093** (as rendered; 4952 strict — the renderer note below) | 163/169 |
| `Arcilator_extended_4state` | extended line, four-state mode | **BOARD 4777/5092, HONEST 4755/5092** | 159/169 |

Movement against the previous (2026-08-06) v3 publish, per row, every flip named in the
campaign records: `circt_verilog_extended` **+8**, `circt_verilog_extended_4state` **+70** as
rendered, `Arcilator_extended` **+33**, `Arcilator_extended_4state` **+213** — and
**green-to-red is ZERO on all four columns**, 5,092/5,093 rows compared per column.

**What the extended engine is at this publish, and how it now relates to the v2 table.** At
the 2026-08-06 publish the extended default-arm columns were the *same boards* as the v2
page's own `circt_verilog`/`Arcilator` columns — per-row identical by construction. **That is
no longer true and the difference is stated rather than smoothed over.** The carried v2 table
is the v2 page's published-engine measurement; the extended columns are two integration waves
ahead of it. Measured on this page:

* `circt_verilog_extended` vs the page's own `circt_verilog`: **per-row identical** — 0 verdict
  differences, 0 exit-code differences over 5,093 rows.
* `Arcilator_extended` vs the page's own `Arcilator`: **6 red-to-green, ZERO green-to-red**, and
  the six are named — `event_control_simulation_minimal`, `regress-sv_br_gh508a_iv`,
  `sequence_rose_test_uvm`, `sequence_fell_test_uvm`, `sequence_past_test_uvm`,
  `sequence_changed_test_uvm`. A further **48** rows keep the same verdict while their exit
  code improves from 1 to 0 (a grader demotion that no longer fires); **53 exit codes move and
  every one of them moves in the 1→0 direction — zero rows exit worse.**

The page's distinct content remains the two `_4state` columns: the same binaries with the
engine's opt-in four-state mode enabled (`--four-valued` lowering) against the default
two-state arm. Same harness environment as the v2 CIRCT columns
(`ARCILATOR_UVM_NO_STUB=1 CIRCT_UVM_SHIMS=1 CIRCT_UVM_FABRICATED_REPORTS=0`,
`OVERRIDE_TEST_TIMEOUTS=900` on the simulate columns, AOT compile-and-run only), same
denominators (5093 compile-lens / 5092 simulate-lens, `regress-vlg_pr587_iv`
harness-excluded, standing disclosure), same dual lens on the simulate columns (BOARD = the
rig's `report.csv` `Pass` column; HONEST = BOARD minus the case-leak rows, definition
unchanged — **30** rows on `Arcilator_extended`, **22** on `Arcilator_extended_4state`).

**Source disclosure.** The engine's source snapshot for the four extended columns is
AmurG/circt [`batch-20260817`](https://github.com/AmurG/circt/tree/batch-20260817) @
[`0f3df6801c`](https://github.com/AmurG/circt/tree/0f3df6801c21b7337e08691d4a6a43776e95bd98),
a single provenance commit whose tree is the tree these columns were built from, and a
fast-forward descendant of the [`batch-20260815`](https://github.com/AmurG/circt/tree/batch-20260815)
snapshot the carried v2 table cites. Two qualifications, both measured:

1. **The snapshot is the exact build tree plus one lit test and a comment-grade disclosure
   sweep.** The board binaries were built at the commit immediately preceding the snapshot
   line; `git diff` between that tree and this snapshot returns **58** files: one lit test
   (`test/Conversion/ImportVerilog/uvm-covergroup-forward-ref.sv`), the 08-15 oracle-neutral
   comment restatement (56 files, 124 comment lines, line-for-line balanced), and the same
   restatement extended to one lit header that sweep predates (5 comment lines). **Every
   non-test hunk is a comment; no compiled byte differs** — the binaries the boards ran are
   built from the same compiled sources this snapshot carries.
2. **Lineage:** this snapshot is a descendant of the v2 page's, which is itself a descendant of
   the 2026-08-06 page's — the batch-`*` provenance line is linear, so the extended columns are
   strictly downstream of every previously published snapshot, not a side line.

**Four-state mover — this one is engine.** `Arcilator_extended_4state` 4564 → **4777**
(+213 BOARD, +214 HONEST), green-to-red **0** across the whole interval. The previous publish's
mover was a *harness correction* and said so (58 of 66 new greens were a corrupted four-state
driver generator); that correction is already inside the 4564 baseline, so none of this +213
is rig. The interval splits into three measured segments:

| segment | Δ | what |
|---|---|---|
| 2026-08-06 → W22 | **+210** | four-valued **aggregate / net-type lowering** (+156: ref remat, union→slice, LTL clock-root, observe-variables), 15 cures (+24), 16 cures across 8 lanes (+14), re-landed + burn cures and the SplitLoops aggregate-element projection (+7/+2/+2), and **+1 that is NOT engine** — one large-core row oscillating against the 900 s budget. **Itemisation residue, stated: those items sum to 206, so 4 of the 210 are unattributed in the campaign's own decomposition.** |
| W22 → W23 | **+1 board, +4 honest** | x-semantics: an out-of-range dynamic select now reads `x` **per bit** (IEEE 1800-2023 §11.5.1). One row flips green; three more stop being green-while-printing-`Failed`, which is where the extra honest rows come from. |
| W23 → W24 | **+2** | a SystemVerilog event trigger now wakes an `always @(e)` block parked before the first trigger — `event_control_simulation_minimal` and `regress-sv_br_gh508a_iv`, on **both** arms. |

Row families across the interval: struct / packed-array / implicit-cast /
net-type-and-strength (`tri`, `supply`, `uwire`) / assertion-block / large-core, plus the
x-semantics families in the second segment. **Stated gap:** the first segment crosses a
test-driver generator succession never isolated by its own board; it is bounded at ~zero
(green-to-red is 0 end to end and no row of its known verdict-moving class is among the
movers) — and bounded is not measured.

**Disclosure — one of this publish's cures does NOT reach the four-state arm.** The same
integration wave that gave `Arcilator_extended` its four SVA rows
(`sequence_rose/fell/past/changed_test_uvm` — an assertion action block now sees its own
edge's non-blocking update) moved **zero** of them on `Arcilator_extended_4state`. Measured
here on both arms of the same build: on the four-state arm those rows still print an
assertion counter one edge stale. The visible consequence is that the four-state mode's
measured row cost below **grew by exactly those four** — *because the default arm was cured
and the four-valued lowering was not, not because the four-state arm lost anything.* The
four-state arm lost no row at this publish. This is an open engine item, disclosed here
rather than left to be inferred from a moving cost number.

**Four-state mode cost (the `_4state` columns vs the default-arm columns, same rig, same
rowset), and it has collapsed.** The four-state mode trades rows for value fidelity, and
every lost row still fails LOUDLY:

| lens | G→R at 2026-08-06 | G→R now | R→G at 2026-08-06 | R→G now |
|---|---|---|---|---|
| compile (`_4state` vs default, strict grading) | 70 | **8** (5 exit `1`, 3 exit `71`; all named) | 0 | 0 |
| simulate (`_4state` vs default, strict grading) | 178 | **18** (all exit `1`) | +103 | **+123** |

**The silent-wrong bucket — a lost row that exits 0 with a clean transcript — is EMPTY on both
lenses.** On the simulate lens the four-state arm now sits **104 rows above** the default arm,
where at the previous publish it sat 76 below.

**Renderer note (the stock renderer's own extra predicate).** For `mode: simulation` rows the
stock report generator applies a transcript predicate the strict grader does not, so a few
rows that exit 0 and pass by transcript are denied on the page. Re-measured by name at this
publish: **8** rows on `circt_verilog_extended_4state` — `property_disable_iff_test`,
`property_local_var_test`, `sequence_changed_test_uvm`, `sequence_fell_test_uvm`,
`sequence_local_var_test`, `sequence_past_test_uvm`, `sequence_rose_test_uvm`,
`sequence_stable_test_uvm` (the four-state SVA-sampling family, whose logs carry heavy
four-valued conversion warnings the stock renderer does not credit through) — **1** on
`Arcilator_extended` (`basic-tagged-union`), **2** on
`Arcilator_extended_4state` (`basic-tagged-union`, `sequence_stable_test_uvm`), **0** on
`circt_verilog_extended`, and **0** rows in the other direction on any column. **This page
publishes the lower, as-rendered number for every column** rather than patching the table
against its own renderer; the strict-graded counts for the same logs are 4952
(`circt_verilog_extended_4state`), 4674 (`Arcilator_extended`) and 4779
(`Arcilator_extended_4state`), while `circt_verilog_extended` has no seam and is 4960 either way.

**Artefact disclosure — the row logs carry more frontend diagnostic lines than the previous
publish.** The extended columns' frontend was upgraded across this interval and emits more
diagnostic lines on the same sources. Measured page against page — the 2026-08-06 v3 logs
against these, on every row of every extended column:

| column | rows | gained diagnostic lines | of which a repeat of a line already present | at a new location/text | lost any line |
|---|---|---|---|---|---|
| `Arcilator_extended` | 5092 | **998 (19.6%)** | 892 | 113 | 6 |
| `circt_verilog_extended` | 5093 | **997 (19.6%)** | 890 | 114 | 6 |
| `Arcilator_extended_4state` | 5092 | **1001 (19.7%)** | 889 | 194 | 39 |
| `circt_verilog_extended_4state` | 5093 | **999 (19.6%)** | 888 | 194 | 39 |

The dominant shape is a **byte-identical repeat** of a diagnostic the row already emitted at
the same file, line and column — not a new finding. Every row that *lost* a line was checked
in the direction that matters: on `Arcilator_extended` all 6 are rows that turned green (a row
that now succeeds no longer prints the error that stopped it), and on
`Arcilator_extended_4state` **zero of the 39 are green on both pages** — no passing row quietly
dropped a diagnostic. **No verdict changes on this account**; it is an artefact change, not a
result change, and anyone diffing published logs against the previous v3 page should be told
rather than discover it.

**Carried-table note (a stock-renderer nondeterminism, named).** The carried columns' *log
content* comes from the v2 page's own logs unchanged, and every carried verdict and exit code
matches it. The rendered pages are not byte-identical to the v2 page's, and the reason was
chased rather than waived: the stock report generator renders each row's `tags` field from an
unordered set, so the same tags appear in a different order between two runs. Over a
2,400-page sample of six carried columns, 960 pages differ byte-wise and **all 960 differences
disappear once the `tags` field alone is order-normalised — zero residual**. The same property
holds between the previously shipped pages; it is a generator artefact, not a content change.

**Grading.** The same fail-closed strict grading and case-leak scanner as the v2 page, both
re-run here on these boards' own logs: the `Arcilator_extended` leak set is the standing 30,
row for row. The `Arcilator_extended_4state` set is **22** — smaller than the previous
publish's 23 and smaller than the 25 measured on the intervening board — and the three names
that left the intervening set were each checked individually rather than netted: all three are
green under **both** gradings at **both** ends, so the reduction is rows that stopped being
green-while-printing-`Failed`, never rows that went red. One member of the 22
(`regress-vlg_pr2728032_iv`) sits outside the default arm's 30-class. The case-leak deduction
defines the HONEST lens in the table above.

**What this page does not claim.** The extended columns are not an upstream-CIRCT measurement
(the published snapshot is a fork tree, cited above), the four-state mode is opt-in and its row
cost is stated rather than averaged away, and rows red here for harness reasons are disclosed
as such rather than re-rolled. For the all-stock baseline see
[sv-tests-results-v1](https://amurg.github.io/sv-tests-results-v1/); for the overlay board
and all standing column disclosures see
[sv-tests-results-v2](https://amurg.github.io/sv-tests-results-v2/).
