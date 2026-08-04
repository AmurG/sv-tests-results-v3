# sv-tests results — CIRCT clean-dense, v3 (extended engine)

Full [sv-tests](https://github.com/chipsalliance/sv-tests) dashboard, **strictly additive on
[sv-tests-results-v2](https://amurg.github.io/sv-tests-results-v2/)**: every column of the v2
page (2026-08-02 publish) is carried here **byte-identical** — same logs, same grading, same
provenance statements — plus **four new extended-engine columns** measured 2026-08-04 on the
same overlay tree (the v2 receipted test-content overlay), the same harness, the same stock
report generator, and the same grading pipeline:

| new column | engine arm | result |
|---|---|---|
| `circt_verilog_extended` | extended line, default | **4952/5093** |
| `Arcilator_extended` | extended line, default | **BOARD 4639/5092, HONEST 4609/5092** |
| `circt_verilog_extended_4state` | extended line, four-state mode | **4874/5093** |
| `Arcilator_extended_4state` | extended line, four-state mode | **BOARD 4498/5092, HONEST 4475/5092** |

**What the extended engine is.** A further-integrated line of the same CIRCT-fork compiler and
runtime the v2 `circt_verilog`/`Arcilator` columns measure — additional lowering and runtime
mechanisms beyond the v2 build. The `_4state` columns run the SAME extended binaries with the
engine's opt-in four-state mode enabled (`--four-valued` lowering); the default-arm columns run
them with the flag off. Same harness environment as the v2 CIRCT columns
(`ARCILATOR_UVM_NO_STUB=1 CIRCT_UVM_SHIMS=1 CIRCT_UVM_FABRICATED_REPORTS=0`,
`OVERRIDE_TEST_TIMEOUTS=900` on the simulate columns, AOT compile-and-run only), same
denominators (5093 compile-lens / 5092 simulate-lens, `regress-vlg_pr587_iv` harness-excluded,
standing disclosure), same dual lens on the simulate columns (BOARD = the rig's `report.csv`
`Pass` column; HONEST = BOARD minus the case-leak rows, definition unchanged from the v2 page).

**Source disclosure.** The extended engine's source tree is **not published**. Unlike the v2
columns (whose `batch-20260802` provenance snapshot is linked on the v2 page and carried in
their cells here), the four extended columns carry **no provenance links**: their per-log tool
link points back at this results repository, and the in-log `circt_commit` field is a bare
build-identity string of an unpublished tree. The v2 columns' provenance statements are
unchanged and remain on the v2 page.

**Superset audit vs the v2 columns (measured at this publish, every exception named).** The
v3 board was ruled strictly additive: any row green on a v2 CIRCT column and red on the
matching extended default-arm column is enumerated by name and classified. Result:

| pair (published v2 CIRCT column vs the matching extended default-arm column) | G→R | every loss named + classified | R→G |
|---|---|---|---|
| `circt_verilog` → `circt_verilog_extended` | **1** | `regress-vlg_scanf4_iv` — LINEAGE-GAP: `$sscanf` `%m` hierarchical-path capture is not legalized on the extended line (loud elaboration error in the row's log) | +1 (`hdlconvertor_std2017_p348`) |
| `Arcilator` → `Arcilator_extended` | **16** | 13× MISSING-LINE-CURE — fixes that landed on the published column's line after the extended line branched from it; every one of the 13 was re-verified **red at the branch-point build on this same rig** at this publish (`regress-sv_array_size_iv`, `regress-vlg_array4_iv`, `regress-vlg_pr1696137_iv`, `regress-sv_always_comb_void_func_iv`, `regress-sv_sv_ps_array_cassign_iv`, `regress-vlg_array_lval_select6_iv`, `regress-vlg_delay5_iv`, `regress-vlg_ldelay1_iv`, `regress-vlg_ldelay3_iv`, `regress-vlg_pr1570451_iv`, `regress-vlg_pr2425055a_iv`, `regress-vlg_sdw_always3_iv`, `regress-vlg_task_mem_iv`); 3× HARNESS-HEURISTIC false red (`regress-sv_always_comb_warn_iv`, `regress-sv_always_ff_warn_iv`, `regress-sv_always_latch_warn_iv`) — the extended engine newly exports module-level registers as observable state and the harness's name-based completion heuristic then misreads these rows' dead `int done` loop variable as an unasserted completion flag; the simulation output is identical on both engines, only the verdict is lost | +4 (`hdlconvertor_std2017_p348`, `regress-synth_sqrt32synth_iv`, `regress-vlg_sqrt32_iv`, `sva_sva_range`) |

No other row anywhere on the board is green on a v2 CIRCT column and red on the matching
extended default-arm column, and the extended columns' rowsets are complete (no dropped rows).

**Four-state mode cost (the `_4state` columns vs the default-arm columns, same rig, same
rowset).** The four-state mode trades rows for value fidelity, and every lost row fails LOUDLY
(an elaboration error, a self-reported FAIL, or a timeout at the 900s budget) — the
silent-wrong bucket (exit 0, quietly wrong output, nothing in the transcript) is **empty** on
both lenses as measured:

| lens | G→R | loud tool failure | exit-0, caught in the transcript | R→G |
|---|---|---|---|---|
| compile (`_4state` vs default) | **70** | 70 = 39 elaboration errors + 26 tool crashes (SIGSEGV) + 5 timeouts at the 900s budget | 0 | 0 |
| simulate (`_4state` vs default) | **243** | 217 = 71 elaboration errors + 58 aborts + 6 SIGSEGV + 80 self-reported FAILs + 1 timeout + 1 non-zero exit | 26 = 23 testbench-caught value/X mismatches (the tool exits 0; the row's own checker prints the mismatch) + 3 signedness-rendering rows (a 4-state operand prints unsigned, e.g. `-15` rendered `241`; the discrepancy is visible in the row's own assert trace) | **+102** — X-semantics families the 2-state default arm cannot pass (`casex`/`casez` groups, `udp_x`, wild-card compares, undefined-shift, 4-state system functions) |

Precisely stated: every `_4state`-lost row carries evidence of its failure **in its own
published log** — a tool error, a crash, a timeout, a self-reported FAIL, or a printed
value/rendering mismatch. No lost row exits 0 with a clean transcript. The 26 exit-0 rows
are the honest quality cost of the mode and are disclosed as such, not hidden: the compiler
does not flag them; the testbench or the grader does. (The compile-lens G→R above is stated
on the strict transcript grading; on the rendered page the `_4state` compile column
additionally shows 8 fewer greens — see the renderer note below the table.)

**Renderer note (8 rows, named).** On `circt_verilog_extended_4state` this page's stock
renderer denies 8 rows that exit 0 and pass by transcript, and which the strict grader (the
same fail-closed grading used for the board records) credits: `property_disable_iff_test`,
`property_local_var_test`, `sequence_changed_test_uvm`, `sequence_fell_test_uvm`,
`sequence_local_var_test`, `sequence_past_test_uvm`, `sequence_rose_test_uvm`,
`sequence_stable_test_uvm` (the four-state SVA-sampling family; their `_4state` logs carry
heavy four-valued conversion warnings that the stock renderer does not credit through).
This page publishes the **lower, as-rendered** number (4874) for that column rather than
patching the table against its own renderer; the strict-graded count for the same logs is
4882. All other CIRCT-backed columns are per-row identical under both gradings.

**Grading.** `report.csv` carries the strict-graded record for the CIRCT-backed columns
(fail-closed regrade of tool-reported successes), exactly as on the v2 page. The case-leak
subtraction on the simulate columns uses the same scanner definition as the v2 page
(pre-validated at this publish against the v2 boards' banked 30-row leak set, reproduced
identically before use).

**What this page does not claim.** The extended columns are a capability preview of an
unpublished engine line: they are not an upstream-CIRCT measurement, they carry no
reproducibility link, and rows red here for harness reasons are disclosed as such above rather
than re-rolled. For the all-stock baseline see
[sv-tests-results-v1](https://amurg.github.io/sv-tests-results-v1/); for the overlay board and
all standing column disclosures see
[sv-tests-results-v2](https://amurg.github.io/sv-tests-results-v2/).
