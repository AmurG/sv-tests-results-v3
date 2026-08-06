# sv-tests results — CIRCT clean-dense, v3 (extended engine + four-state mode)

Full [sv-tests](https://github.com/chipsalliance/sv-tests) dashboard, **strictly additive on
[sv-tests-results-v2](https://amurg.github.io/sv-tests-results-v2/)**: every column of the v2
page (2026-08-06 publish) is carried here **byte-identical** — same logs, same grading, same
provenance statements — plus **four extended-engine columns** measured 2026-08-05 on the
same overlay tree (the v2 receipted test-content overlay), the same harness, the same stock
report generator, and the same grading pipeline:

| new column | engine arm | result |
|---|---|---|
| `circt_verilog_extended` | shipped line, default (two-state) | **4952/5093** |
| `Arcilator_extended` | shipped line, default (two-state) | **BOARD 4640/5092, HONEST 4610/5092** |
| `circt_verilog_extended_4state` | shipped line, four-state mode | **4874/5093** (as rendered; 4882 strict — the renderer note below) |
| `Arcilator_extended_4state` | shipped line, four-state mode | **BOARD 4564/5092, HONEST 4541/5092** |

**What the extended engine is at this publish.** At the prior (2026-08-04) publish the
extended columns previewed a further-integrated line beyond the then-published v2 build. At
THIS publish the lines have converged: the v2 page's own `circt_verilog`/`Arcilator` columns
now measure the SAME shipped engine build, so the two default-arm extended columns here are
the same boards as the v2 page's fresh columns (per-row identical by construction), retained
for column continuity. The page's distinct content is the two `_4state` columns: the SAME
binaries with the engine's opt-in four-state mode enabled (`--four-valued` lowering), against
the default two-state arm. Same harness environment as the v2 CIRCT columns
(`ARCILATOR_UVM_NO_STUB=1 CIRCT_UVM_SHIMS=1 CIRCT_UVM_FABRICATED_REPORTS=0`,
`OVERRIDE_TEST_TIMEOUTS=900` on the simulate columns, AOT compile-and-run only), same
denominators (5093 compile-lens / 5092 simulate-lens, `regress-vlg_pr587_iv`
harness-excluded, standing disclosure), same dual lens on the simulate columns (BOARD = the
rig's `report.csv` `Pass` column; HONEST = BOARD minus the case-leak rows, definition
unchanged from the v2 page — 30 rows on `Arcilator_extended`, a 23-row subset of the same
class on `Arcilator_extended_4state`).

**Source disclosure.** The engine's source snapshot is **published at this publish**:
AmurG/circt [`batch-20260806`](https://github.com/AmurG/circt/tree/batch-20260806) @
[`08e7ee1bd5`](https://github.com/AmurG/circt/tree/08e7ee1bd5885e1a5ea399d67ded482ab3c6a03b)
— the same single-commit provenance snapshot the v2 page cites, with the same two stated
qualifications (lineage: a separately integrated line, not a descendant of the prior
`batch-20260802` build; scrub: the published tree differs from the exact build tree by a
comment-grade disclosure scrub — 120 comment lines across 43 files, line counts preserved,
zero non-comment lines touched, binaries unaffected). The prior v3 page's
"source not published" stance for the extended columns ends at this publish; the per-log
tool links on the extended columns now point at the published snapshot.

**Harness-correction disclosure — the on-arc mover at this publish (FQ2).** The prior page's
`Arcilator_extended_4state` figure (BOARD 4498 / HONEST 4475) was measured under a four-state
test-driver generator later found to carry a memory-corruption defect (an absolute-offset
write past a buffer — a heap stomp — in the generated four-state driver path). The corrected
generator (the campaign's FQ2 pin) re-measured the whole leg: **58 named rows** whose ON-arm
drivers the defect had corrupted re-score green, with **ZERO green-to-red** at both gradings
and the remaining rows' verdicts unchanged; the OFF-arm (default) drivers were proven
byte-identical under the old and corrected generator, so no other column moved on this
correction. The further **+8** on-arc gains vs the prior page are engine (the shipped line's
signed-display four-state formatting and four-valued force-override window families, each row
named in the campaign records). Net: 4498 → **4564** BOARD (4475 → **4541** HONEST). This is
disclosed as a harness correction, not an engine gain: 58 of the 66 new greens exist because
the measurement rig was fixed.

**Default-arm continuity vs the prior v3 page (every flip named).** `circt_verilog_extended`
4952 → 4952 and `circt_verilog_extended_4state` 4874 → 4874: zero flips in either direction,
per-row identical. `Arcilator_extended` 4639 → 4640: zero green-to-red, ONE red-to-green
(`regress-vlg_sdw_force_iv` — the shipped line's four-valued force-override window). The v2
carried table's own `circt_verilog`/`Arcilator` columns move with the v2 page at this publish
(its lineage disclosure and 16-row named green-to-red ledger apply; see the v2 README — this
page's carried table is byte-identical to it).

**Four-state mode cost (the `_4state` columns vs the default-arm columns, same rig, same
rowset).** The four-state mode trades rows for value fidelity, and every lost row fails
LOUDLY — the silent-wrong bucket (exit 0, quietly wrong output, nothing in the transcript) is
**empty** on both lenses as measured at this publish:

| lens | G→R | in-log evidence classes | R→G |
|---|---|---|---|
| compile (`_4state` vs default, strict grading) | **70** | 47 loud elaboration errors + 18 tool crashes (the run dies without writing its completion record — the SIGSEGV class) + 5 timeouts at the 900s budget | 0 |
| simulate (`_4state` vs default, strict grading) | **178** | 71 loud elaboration errors + 99 loud runtime failures (a self-reported FAIL / UVM_ERROR / runtime fatal printed in the row's own transcript) + 6 crashes + 1 timeout + 1 nonzero exit | **+103** — X-semantics families the two-state default arm cannot pass (`casex`/`casez` groups, `udp_x`, wild-card compares, undefined-shift, four-state system functions) |

Precisely stated: every `_4state`-lost row carries evidence of its failure **in its own
published log** — a tool error, a crash, a timeout, or a printed failure. No lost row exits 0
with a clean transcript. (The compile-lens G→R above is stated on the strict transcript
grading; on the rendered page the `_4state` compile column additionally shows 8 fewer greens —
the renderer note below.)

**Renderer note (8 rows, named — standing from the prior page, re-measured identical).** On
`circt_verilog_extended_4state` this page's stock renderer denies 8 rows that exit 0 and pass
by transcript, and which the strict grader (the same fail-closed grading used for the board
records) credits: `property_disable_iff_test`, `property_local_var_test`,
`sequence_changed_test_uvm`, `sequence_fell_test_uvm`, `sequence_local_var_test`,
`sequence_past_test_uvm`, `sequence_rose_test_uvm`, `sequence_stable_test_uvm` (the
four-state SVA-sampling family; their `_4state` logs carry heavy four-valued conversion
warnings that the stock renderer does not credit through). This page publishes the **lower,
as-rendered** number (4874) for that column rather than patching the table against its own
renderer; the strict-graded count for the same logs is 4882. All other CIRCT-backed columns
on this page are per-row identical under both gradings (Pass verdicts; the simulate columns'
CSV carries the renderer's exit codes, with the strict record's exit codes preserved in the
campaign receipts — verdict-identical, disclosed).

**Grading.** The same fail-closed strict grading and case-leak scanner as the v2 page
(scanner unchanged; the `Arcilator_extended` leak set is row-for-row the v2 page's 30; the
`Arcilator_extended_4state` set is a 23-row subset of the same class). The case-leak
deduction defines the HONEST lens in the table above.

**What this page does not claim.** The extended columns are not an upstream-CIRCT
measurement (the published snapshot is a fork tree, cited above), the four-state mode is
opt-in and its row cost is stated rather than averaged away, and rows red here for harness
reasons are disclosed as such rather than re-rolled. For the all-stock baseline see
[sv-tests-results-v1](https://amurg.github.io/sv-tests-results-v1/); for the overlay board
and all standing column disclosures see
[sv-tests-results-v2](https://amurg.github.io/sv-tests-results-v2/).
