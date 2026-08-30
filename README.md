# sv-tests results — CIRCT clean-dense, v3 (extended engine + four-state mode)

Full [sv-tests](https://github.com/chipsalliance/sv-tests) dashboard, **strictly additive on
[sv-tests-results-v2](https://amurg.github.io/sv-tests-results-v2/)**: every column of the v2
page is carried here from the v2 page's own logs — same logs, same grading, same provenance
statements, **zero verdict flips in either direction across all 20 carried columns** — plus
**four extended columns**, re-measured for this publish on the same harness, the same
stock report generator and the same grading pipeline:

**THE ARM LABELS — CORRECTED AT THE 2026-08-23 PUBLISH; STANDING HERE.** The 2026-08-19
and 2026-08-22 revisions of this page called the four-state honest power-on arm *the ENGINE
DEFAULT* and called the two-state arm *the opt-out*. **That is backwards.** The four-valued
lowering is opt-in — the engine's own option table reads `Default false = the unchanged
two-valued path` — the boards reach it through a wrapper that adds `--four-valued` to the
same engine binary the other arm runs raw, and the shipped command-line simulator does not
carry the flag at all (it needs `--four-state`). So on this page **`Arcilator_extended`
and `circt_verilog_extended` are the DEFAULT arm — what a user actually gets — and the two
`_4state` columns are the OPT-IN arm**, which is also the arm the v1/v2 pages headline.
Column KEYS are unchanged so deep links survive; only the labels are corrected. BOARD ≡
HONEST holds on every simulate column (standing since 2026-08-22): the case-leak grader
defect is fixed at the runner and the scanner reads ZERO on these boards' own logs.

| new column | engine arm | result | `uvm` cell |
|---|---|---|---|
| `circt_verilog_extended` | **compile DEFAULT arm** (no `--four-valued`) | **5000/5093** | 169/169 |
| `Arcilator_extended` | **THE DEFAULT ARM** — two-valued; what a default invocation gets | **4672/5092** — BOARD ≡ HONEST | **166/169** |
| `circt_verilog_extended_4state` | **OPT-IN** `--four-valued` compile arm | **5039/5093** (rendered; the renderer note below) | 169/169 |
| `Arcilator_extended_4state` | **OPT-IN four-valued arm** (the arm the v1/v2 pages headline) | **4889/5092** — BOARD ≡ HONEST | 164/169 |

The `uvm_id_register` red inside both simulate columns' `uvm` cells is proven by reference identity, re-verified at this publish: our transcript is line-for-line identical to the second reference's, including every timestamp and all 26 UVM_ERRORs — the example's `create_map(..., UVM_BIG_ENDIAN)` makes `uvm_reg_map` byte-swap the full 64-bit `uvm_reg_data_t` inside the UVM library itself, and no conforming IEEE 1800.2 simulator can pass this row as graded (full disclosure on the [v2 page](https://amurg.github.io/sv-tests-results-v2/)). The `_4state` simulate uvm cell reads **164/169** — UP TWO at this publish: the reopened `property/sequence_local_var_test_uvm` pair is CURED by the tracked NBA-region fix (the grading bullet below).

- **Publication posture (standing since the 2026-08-19 publish): the dashboards are public; the engine source is NOT published.** The per-row provenance fields read `not published`, the tool-header links point at this results page itself, and results provenance is retained privately; every number on this page is reproducible from that retained record.

- **Three grading/engine changes STANDING since the 2026-08-22 publish (they landed there, not here), disclosed in full on the [v2 page](https://amurg.github.io/sv-tests-results-v2/) and re-measured on THESE boards:** (1) the **case-leak grader fix** — a printed self-reported failure verdict now demotes the row at scoring time; the scanner reads ZERO on all four extended columns and the give-back rows were the green-to-red sets of the 2026-08-22 movement ledger (30 on the DEFAULT column, 21 on the opt-in column — grader honesty, zero engine regressions); (2) the **timing-annotation compat hatch** — `--ignore-timing-annotations` rides both CIRCT frontends' invocations (documented flag; per-site warnings plus a mandatory once-per-run summary; a green under it is a zero-path-delay simulation, stated plainly); the once-per-run notice is present in 37/42 logs of the two compile columns (DEFAULT / opt-in — the opt-in count rose with the rows its gains take through the annotation-dropping path) and 37/42 logs of the DEFAULT / opt-in simulate columns here; (3) **assertion pass-action vacuity** — implication properties now report per obligation and pass actions execute on nonvacuous successes only, a DOCUMENTED reference-aligned deviation from the IEEE 1800-2023 20.11 default sentence (aligned with every external reference the campaign has banked for this construct; restorable via `$assertcontrol`; zero verdict flips on these boards on this account).

- **STANDING SINCE THE EARLIER 2026-08-25 PUBLISH — two more verdict-scanner holes closed there, the give-back fully named; re-verified here (ONE give-back row — `regress-vlg_br_ml20150315_iv` — is ENGINE-CURED at this publish on both simulate columns, its silenced simulation now printing its monitor line and passing; the rest stay red).** The strict evaluator had two blind spots, repaired in the GRADER, not the engine: a bare all-caps `FAIL` verdict token was invisible to the verdict scan (21 ivtest sources print exactly that), and the runtime-output scan applied only to `.sv` sources, so a silenced `.v`-source simulation (rc=0, no output at all) kept its green. Validated old-vs-new before any board ran (banked arms reproduce exactly under the old grader; the new grader's flip set equals an independently surveyed prediction exactly; doctored controls bite). Give-back on THESE boards: 10 named rows on the DEFAULT simulate column, the same 10 plus `regress-vlg_pr1676071_iv` on the opt-in simulate column, zero on either compile column — every row named on the [v2 page](https://amurg.github.io/sv-tests-results-v2/). Also from that publish, the honest REOPENING of `property_local_var_test_uvm` and `sequence_local_var_test_uvm` on the opt-in simulate column — their earlier green was masked by a change violating IEEE 1800-2023 4.5's region ordering, excluded for exactly that violation — is RESOLVED AT THIS PUBLISH BY THE TRACKED PATH: the proper cure (moving the NBA commit point to the LRM's NBA region) landed in this interval's first stamped wave and both rows are GREEN here, rc=0 to their own passing verdicts under conforming region ordering.

Movement against the previous (2026-08-25 second-revision) v3 publish, per column, across
TWO stamped integration waves. Every flip is named, and GREEN-TO-RED IS ZERO ON ALL FOUR.
The compile columns: `circt_verilog_extended` **+1** and `circt_verilog_extended_4state`
**+1** — the single mover on both is `hdlconvertor_std2017_p612`, GIVEN BACK by this
publish's crash-heuristic GRADER repair (the standing false positive: a correct diagnostic
containing the word *aborted* tripped the process-crash heuristic; the repaired grader
was battery-validated offline over the four prior banked boards BEFORE any board ran —
prediction EXACT, six cells and nothing else across 20,370 rows). This interval's
compile-side ENGINE movement is ZERO on both arms, row-for-row. The simulate columns:
`Arcilator_extended` (the DEFAULT arm) reads **4672/5092**, **+8** — two engine cures
shared with the opt-in column (`regress-vlg_br_ml20150315_iv`, whose silenced simulation
now prints its monitor line and passes, and `regress-vlg_scanf2_iv`, a crash cure,
rc=134 → 0), FIVE delay-family engine cures that land on the default arm alone
(`regress-vlg_ldelay1_iv`, `regress-vlg_ldelay3_iv`, `regress-vlg_rise_fall_decay1_iv`,
`regress-vlg_rise_fall_delay1_iv`, `regress-vlg_rise_fall_delay2_iv` — the first two
stay red on the opt-in column and JOIN its named cost set; the three `rise_fall` rows
were already green there), plus the `hdlconvertor_std2017_p612` give-back;
`Arcilator_extended_4state` (the opt-in arm) reads **4889/5092** on a **12 red-to-green /
ZERO green-to-red ledger with every flip named**: the reopened
`property/sequence_local_var_test_uvm` pair (the tracked NBA-region cure LANDED — the
bullet above), the wired-net resolution pair `regress-vlg_triand_iv`/`regress-vlg_trior_iv`
(multi-driver wired-AND/-OR resolution lands; each row passes the self-checks it printed
failing before), `regress-vlg_br_ml20150315_iv` and `regress-vlg_scanf2_iv` (the two
shared cures above), `regress-vlg_test_mos_strength_reduction_iv` (the earlier page's
GRADER-LENS red: the `FAILED:`-quoting diagnostic no longer rides its log and its own
PASSED transcript stands — a diagnostic-text clearance, not a behaviour change),
`hdlconvertor_std2017_p612` and the two `cva6_*` grader-lens reds (the crash-heuristic
grader give-back — rc=0 with completed elaboration all along), and the two
`bp_multicore_4*` giants, home INSIDE their own declared 1000 s budgets: 836.3 s and
972.8 s measured (the FLOOR override honored the declared 1000 s; verdicts of record
taken in a dedicated calibrated slot with the measurement window held by the scoring
lane, walls within 1.1%/0.5% of solo calibration runs 827.5/968.3 s — contention-controlled,
not contention-assisted; the prior page measured 1002.5/1002.6 s rc=71 on the same
budgets, and this interval's engine performance work pays the register-retention
wall-clock down far enough to fit). The ENGINE-ATTRIBUTABLE step on the opt-in simulate
column is **+8** (six behaviour cures plus the two giants); the diagnostic-text
clearance rides along, and the three grader give-backs are grader honesty in the
favourable direction.

**How the extended columns relate to the v2 table at this publish — the identity pairing
FLIPPED with the default.** The carried v2 table and the extended columns are the SAME engine
generation again (one tip, one snapshot), and the identity now runs through the four-state
column, measured on this page's own CSV:

* `circt_verilog_extended` vs the page's own `circt_verilog`: **per-row identical** — 0 verdict
  differences over 5,093 rows (the compile default carries no four-valued flag and is untouched
  by the power-on flip).
* `Arcilator_extended_4state` vs the page's own carried `Arcilator` (the v2 page's headline
  column): **per-row identical** — 0 verdict differences over 5,092 rows. The v1/v2 pages
  headline the OPT-IN arm, and this is that same measurement; it is kept here so the
  `_4state` series stays continuous.
* `Arcilator_extended` (**the DEFAULT arm**) vs the page's own carried `Arcilator`: the flip
  ledger between the two arms of one build, re-measured here — **18 rows green only on the
  DEFAULT arm and 235 green only on the opt-in arm** (page lens). Read that as a
  verdict-lens ledger, not a fidelity score. This column is what a user gets without
  passing a flag, and it is retained precisely so the cost and the reach of opting in are
  both visible on one page.

The page's distinct content is the DEFAULT arm (`Arcilator_extended` — the two-valued arm a
user gets without passing anything) and the `--four-valued` compile arm
(`circt_verilog_extended_4state`); the `Arcilator_extended_4state` column coincides with the
carried `Arcilator` column by construction (identity measured above). Same harness environment as the v2 CIRCT columns
(`ARCILATOR_UVM_NO_STUB=1 CIRCT_UVM_SHIMS=1 CIRCT_UVM_FABRICATED_REPORTS=0`,
`OVERRIDE_TEST_TIMEOUTS=900` on the simulate columns, AOT compile-and-run only), same
denominators (5093 compile-lens / 5092 simulate-lens, `regress-vlg_pr587_iv`
harness-excluded, standing disclosure). The dual lens was RETIRED at the 2026-08-22 publish and stays retired: the
case-leak scanner re-run fail-closed on these boards' own logs reads **ZERO** on both
simulate columns — BOARD ≡ HONEST everywhere on this page (the grader fix in the bullet
above; the HONEST-lens history is preserved in the prior revisions).

**Source disclosure — PUBLICATION POSTURE (standing, by ruling): the dashboards are public; the engine source is NOT published.** The four extended columns were built from the campaign's engine tree of 2026-08-27; results provenance — the exact source tree, build receipts and board banks — is retained privately, every number is reproducible from that retained record, and nothing on this page implies the source is available (each row's `circt_ref` reads `not published`; the earlier revisions' public snapshot branches are retired). Two statements, both measured:

1. **The retained provenance tree IS the exact build tree.** Its tree is bit-identical to the source the
   boards' binaries were built from (tree-hash equality verified at staging); the
   earlier publishes' disclosure rewrites are part of this line's history now.
2. **Lineage:** the privately retained provenance line is linear — these columns are strictly
   downstream of every earlier publish's build, not a side line. The carried v2 table on
   this page is the v2 page's own fresh measurement at this same build.

**Four-state mover — the series continues.**
`Arcilator_extended_4state` 4564 → 4777 → 4819 → 4829 → 4865 → 4864 → 4877 → **4889** (this publish net **+12** across two stamped waves: six engine cures of the pages' own standing named reds, the two budget giants home inside their declared budgets, one diagnostic-text clearance and three crash-heuristic grader give-backs, ZERO green-to-red — the ledger above; the DEFAULT arm moves **+8** in the same interval, its first engine movement in five publishes). The 2026-08-15 publish's
mover was a *harness correction* and said so (58 of 66 new greens were a corrupted four-state
driver generator); that correction is already inside the 4564 baseline, so none of this +217
is rig. The interval splits into three measured segments:

| segment | Δ | what |
|---|---|---|
| 2026-08-06 → W22 | **+210** | four-valued **aggregate / net-type lowering** (+156: ref remat, union→slice, LTL clock-root, observe-variables), 15 cures (+24), 16 cures across 8 lanes (+14), re-landed + burn cures and the SplitLoops aggregate-element projection (+7/+2/+2), and **+1 that is NOT engine** — one large-core row oscillating against the 900 s budget. **Itemisation residue, stated: those items sum to 206, so 4 of the 210 are unattributed in the campaign's own decomposition.** |
| W22 → W23 | **+1 board, +4 honest** | x-semantics: an out-of-range dynamic select now reads `x` **per bit** (IEEE 1800-2023 §11.5.1). One row flips green; three more stop being green-while-printing-`Failed`, which is where the extra honest rows come from. |
| W23 → W24 | **+2** | a SystemVerilog event trigger now wakes an `always @(e)` block parked before the first trigger — `event_control_simulation_minimal` and `regress-sv_br_gh508a_iv`, on **both** arms. |
| W24 → W25 (2026-08-18) | **+1 board / +1 honest** | ⭐ **the clock-reload cure**: on the four-valued arm every register on a PROCESS-DRIVEN clock had committed ONE EVALUATION LATE (an end-of-eval reload defect reaching every plain testbench clock generator); cured, with the historical caveat below. |
| W25 → W26 | **+38** | ⭐ honest x/z **power-on within four-valued lowering** became default-ON. The campaign recorded this at the time as *the flip to a four-state engine default*; **that reading was withdrawn at the 2026-08-23 publish** — the switch it turned on applies only where four-valued leaves exist, so it is inert on the two-valued arm by construction, and four-valued lowering itself stayed opt-in throughout (the arm paragraph at the top). The 38-row recovery list was pre-registered from the historical flag boards and reproduced row-for-row (38/38 expected-hit, zero unexpected movers), minus the two named budget giants — all of it on THIS column, none of it on the default arm. |
| W26 → the 2026-08-19 publish (suite merge) | **+4 report lens only** | upstream `b5685cbe` removed the `:assert:` marker the report lens mis-scored on the sequence quartet (SCORING change; strict EXACT; the retirement paragraph below). `tests/` is byte-identical to upstream `c4229f3b` (unchanged at this publish). |
| the 2026-08-19 publish → the 2026-08-22 publish (W27+W28 waves + the grader honesty pass) | **+10 board / +31 vs prior honest** | ⭐ the case-leak GRADER cure (gives back the 21 leak rows of this column — BOARD ≡ HONEST from this publish on), the documented timing-annotation compat hatch (+29 on this column, zero-path-delay caveat stated), the static-initializer cure (`ibex`), and the renderer parseLog scoping fix (`sequence_stable_test_uvm` back on the page lens). |
| the 2026-08-22 publish → the 2026-08-23 publish (the W29 wave) | **+36** | switch-level primitives (`bufif0/1`, `notif0/1`, `nmos`, `pmos`, `tran*`) and explicit drive strengths, all four-valued-gated: 33 rows the prior board refused at import, 1 strength-resolution value row, and 2 budget giants published as contention. **The DEFAULT arm moved by 0 rows across the same interval** |
| the 2026-08-23 publish → the earlier 2026-08-25 publish (the W32A wave + the scanner repair) | **-1 net: +18 / -19, all named** | ⭐ the four-valued packed-vector write-through-select value-semantics family lands (+18, each row's prior log printed its own failing self-check); the verdict-scanner repair gives back 11 rows (grader honesty, arm-independent — 10 of the 11 also flip on the DEFAULT column); the giants return to rc=71 at the budget as the register-retention price; two `cva6_*` grader-lens textual reds; the reopened region-ordering UVM pair; `br_gh315` honest red; `pr1662508` bare-`FAIL` made visible. **The DEFAULT arm's engine verdicts moved by 0 rows across the interval; its -10 is entirely the scanner repair** |
| the earlier 2026-08-25 publish → the 2026-08-25 second-revision publish (the W33 wave) | **+13** | ⭐ power/arithmetic unknown-operand value semantics (+5, IEEE 1800-2023 Table 11-4 and kin) and the rnmos/rpmos/cmos/rcmos switch-primitive class (+7), each row attributed by its prior log's own failure text; plus the `fx68k` HARNESS repair (+1, flips on all four columns — not booked as engine). GREEN-TO-RED ZERO on every column; the two budget giants stay red at their now-honored declared 1000 s budgets (the timeout override became a FLOOR — a rig correction, zero rows moved on it). **The DEFAULT arm's engine verdicts moved by 0 rows across the interval; its +1 is the harness repair** |
| the 2026-08-25 second-revision publish → this publish (the W34+W35 waves + the crash-heuristic grader repair) | **+12** | ⭐ six engine cures of the pages' OWN standing named reds: the reopened region-ordering UVM pair (the tracked NBA-region commit-point cure LANDS), the `triand`/`trior` wired-net multi-driver resolution wall, a silenced-simulation row (`br_ml20150315`, both arms) and a crash row (`scanf2`, rc=134 → 0, both arms); the two budget giants HOME inside their declared 1000 s budgets (836.3/972.8 s, calibrated lane-held slot); one diagnostic-text clearance (`test_mos_strength_reduction`); and the crash-heuristic grader repair gives back `hdlconvertor_std2017_p612` (all four columns) and the two `cva6_*` textual reds (battery-validated offline, prediction EXACT: six cells / 20,370 rows). GREEN-TO-RED ZERO on every column. **The DEFAULT arm takes +8 — two shared cures, five delay-family cures of its own, and the grader row** |

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
and `sequence_stable_test_uvm` joined them there at the 2026-08-23 publish (the renderer's
parseLog evaluation scoped to non-diagnostic context — the renderer note below), staying
green here.
What remains red on the OPT-IN arm alone: the two deferred-assert rows (`assert0_test_uvm`,
`assert_final_test_uvm`, green on the DEFAULT column beside them) — the reopened `property/sequence_local_var_test_uvm` pair is CURED at this publish by the tracked NBA-region fix (the scanner-and-reopening bullet above) and both rows are green here. On the opt-in arm the
asserted expression is `x` at the assertion statement and an `x` is not true; on the
two-valued arm the same expression is a concrete zero. Which of those is the conforming
answer is tracked as an open adjudication — the earlier description of the pair as a
*misfire* is withdrawn — and the rows are published red as measured.

**Four-state mode cost (the `_4state` columns vs the default-arm columns, same rig, same
rowset), and it has collapsed.** The four-state mode trades rows for value fidelity, and
every lost row still fails LOUDLY:

| lens | G→R at 2026-08-15 | G→R now | R→G at 2026-08-15 | R→G now |
|---|---|---|---|---|
| compile (four-valued arm vs the compile default, strict grading) | 8 | **5** (all named in the campaign records) | 0 | **44** |
| simulate (the OPT-IN four-valued arm vs the DEFAULT two-valued arm, page lens) | 18 | **18** | +123 | **+235** |

CORRECTION OF RECORD (standing since the earlier 2026-08-25 publish) on the compile row's R→G cell: two earlier revisions published **0** there while their own CSVs read **35**. The cell carries the measured number — **44** here (the four-valued compile arm's green-only rows; the same measurement that puts `circt_verilog_extended_4state` 39 net above the compile default).

**The silent-wrong bucket — a lost row that exits 0 with a clean transcript — is EMPTY on both
lenses** (re-measured here: every rc=0 member of the cost set carries the very diagnostic or
`FAIL` text that demoted it). On the simulate lens the OPT-IN four-valued arm sits **217 rows
above** the DEFAULT arm, where at the 2026-08-25 second-revision publish it sat 213 above,
at the earlier 2026-08-25 201, at 2026-08-23 192, at 2026-08-22 156, at 2026-08-19 147,
at 2026-08-15 105, and at 2026-08-06 it sat 76 below. The 18 simulate
losses are the arm's named cost set: 2 deferred-assert rows, 11 honest-initialization rows
(two prior members — `regress-sv_edge_iv`, `regress-vlg_memport_bs_iv` — cured on the opt-in arm
at the earlier 2026-08-25 publish), 2 NEW logic-delay rows (`regress-vlg_ldelay1_iv`,
`regress-vlg_ldelay3_iv` — this interval's DEFAULT-arm-only cures, red here with rc=1
self-checks), and 3 more named on the v2 page's ledger (`br_gh315` honest red;
`pr1662508` and `pr1676071` bare-`FAIL` visibility) — the 2 budget giants, the 2 `cva6_*`
rows and the reopened `property/sequence_local_var_test_uvm` pair LEFT the set at this
publish, cured on the opt-in column (the movement ledger above) — classified row-by-row
on the v2 page. That cost buys the 235-row reach above (itself a stale-223 correction:
the prior revision's reach sentence lagged its own updated ledger) — and none of that
reach is available to a default invocation.

**Renderer note (the stock renderer's own extra predicate — now SCOPED).** For `mode: simulation`
rows the stock report generator applies a transcript predicate the strict grader does not; at
this publish's rig that predicate's oracle evaluation is SCOPED to non-diagnostic context (a
grader fix, validated against the strict ledger). Re-measured by name at this publish: **0** rows
on both compile columns (rendered and strict agree at 5039 on the four-valued compile column,
as they did at the prior publish), and exactly **1** row on each simulate column —
`basic-tagged-union` (report-red/strict-green on both arms) — with **0** rows in the other
direction. **This page publishes the lower, as-rendered number for every column**; the
strict-graded counts are 5039 (`circt_verilog_extended_4state`), 4673 (`Arcilator_extended`)
and 4890 (`Arcilator_extended_4state`), while `circt_verilog_extended` has no seam and is
5000 either way.

**Artefact disclosure — how these logs differ from the previous publish's.** The standing frontend
finding is carried: the 2026-08-15→19 interval's slang bump added dominantly repeated diagnostic
lines on ~15-20% of rows; those lines remain. THIS publish's interval adds no frontend bump; its
measured log-shape changes are: (a) the value-content changes of the named engine cures
(the local-var pair and `triand`/`trior` now run to their passing verdicts; the silenced
`br_ml20150315` monitor line APPEARS; `scanf2` no longer crashes; the delay-family rows
print their passes on the DEFAULT column); (b) a mechanical line-number shift in
C-linkage compile warnings (a runtime header grew ~57 lines under this interval's `%t`
work — audited, zero transcript delta); the earlier 2026-08-25 publishes' scanner
demotions and the four-valued-aggregate warnings remain in the carried logs (the
warnings no longer cost verdicts — the grader give-back in the ledger above). The timing-annotation
once-per-run notice census at this publish reads 37 / 42 (compile DEFAULT / opt-in) and
37 / 42 (simulate DEFAULT / opt-in). Verdict deltas on this account are exactly the movement
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
leak sets (30 default-arm / 21 opt-in-arm) gave their rows back at the 2026-08-22 publish, and
BOARD ≡ HONEST holds everywhere on this page. The rig's report-vs-strict divergence on the
simulate columns (4889 vs 4890 / 4672 vs 4673, exactly `basic-tagged-union`) is the renderer-lens
artifact disclosed above, stated on the v2 page as well.

**What this page does not claim.** The extended columns are not an upstream-CIRCT measurement
(they measure the campaign's fork engine; the source is not published — the posture above); the
four-state arm is NOT the engine default and this page no longer says it is — it is reached by
passing `--four-valued` (or `--four-state` in the shipped simulator), the DEFAULT arm is
published beside it as `Arcilator_extended`, and the opt-in arm's row cost against that
default is stated row-by-row rather than averaged away; rows red here for harness or renderer-lens reasons are
disclosed as such rather than re-rolled. For the all-stock baseline see
[sv-tests-results-v1](https://amurg.github.io/sv-tests-results-v1/); for the overlay board
and all standing column disclosures see
[sv-tests-results-v2](https://amurg.github.io/sv-tests-results-v2/).
