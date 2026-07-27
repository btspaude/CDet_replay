# Calibration notes

This is a code-informed notebook, not an approved calibration prescription or statement of accepted results. Each entry must distinguish implemented behavior, historical notes, verified evidence, assumptions, and open questions.

## Calibration objective

The current SBS-replay macros implement CDet timing calibration. Their stated goals are bar-to-bar timing offsets, CDet/ECal timing correlation, and TOT-dependent time-walk correction. The project leader still needs to confirm that this workflow, its outputs, and its acceptance criteria are the current objective.

## Inputs

The master macros read replayed ROOT trees containing `earm.cdet.hits.*` (generic/raw hits), `earm.cdet.hit.*` (the `SBSCDet_Hit` collection), ECal quantities, and selected HCal quantities. The cross-target macro can load a single run or a grouped run list. `crossRuns.txt` currently lists groups of runs from 3573 through 5992; this is evidence of an implemented candidate set, not approval that all are scientifically appropriate.

The macro reads/writes `CDet_calibration.dat`, with sections for 168 bar offsets, ECal timing parameters, and layer-dependent time-walk parameters. Record exact code/database revisions and preserve any existing calibration file before running a driver: some drivers can delete or replace it.

## Run selection

Document detector/DAQ conditions, target, exclusions, stream/segment ranges, event limits, and selection rationale. Existing drivers default to cross-target run 3575 and hydrogen run 6077, and grouped lists contain many additional runs. These defaults and lists show prior workflow only; the approved run set is unresolved.

## Timing calibration

The current cross-target master macro implements stages 0–8:

| Stage | Implemented purpose |
|---:|---|
| 0 | No calibration / raw inspection |
| 1–2 | Fit, then apply 168 bar timing offsets |
| 3–4 | Fit, then apply CDet/ECal timing correction with bar offsets |
| 5–6 | Inspect, then fit TOT-dependent time walk |
| 7 | Apply bar, ECal, and time-walk corrections |
| 8 | Fit CDet/ECal timing with bar and time-walk corrections but without an existing ECal correction (hydrogen path) |

The in-session cross-target driver runs an iterative sequence `0 → 1 → 3 → 6`, then `1 → 3 → 6`, followed by stage 7 closure/final passes. The time-walk model in the macro is layer-dependent and proportional to `1/sqrt(TOT) - 1/sqrt(TOT_ref)`.

The current cross-target and hydrogen macros explicitly use logical ID 2696 as an event reference and convert their TDC arrays with `TDC_calib_to_ns = 0.01`. This verifies what the macros do, not the hardware meaning or unit correctness. `VETROCcdet` comments describe its decoded raw time as picoseconds, while replay histogram titles say `10ps/ch`; resolve this before treating numerical timing constants as physical nanoseconds.

## Gain/amplitude calibration, if applicable

`SBSCDet` disables ADC processing by default, and the current master timing calibration sets `nBarsADC = 0`. No CDet gain/amplitude calibration procedure was found in the inspected active path. Establish whether it is applicable before adding one.

## Channel quality

The calibration macro contains bad/hot-channel suppression hooks, per-pixel/per-bar distributions, multiplicity checks, and hit-count storage for fitted bar offsets. The scientific criteria and approved bad-channel list still need documentation. Keep physical paddles, maPMT pixels, electronics channels, and logical IDs distinct; the macro's variable name `NumPaddles = 16` actually covers every maPMT pixel position.

## Coordinate and alignment checks

State the coordinate system, sign convention, units, surveyed or assumed geometry, and comparison method. The current convention says the physically bottom channel corresponds to positive transport `x`.

## Validation

Record independent checks, residuals, distributions, comparisons, failure modes, and acceptance criteria. Follow [validation/README.md](validation/README.md) for provenance.

## Uncertainties and systematics

Identify statistical uncertainty, model dependence, run dependence, channel-to-channel variation, selection sensitivity, reference uncertainty, and geometry/alignment effects as applicable. No values are established here.

## Open questions

- Is the current staged timing workflow the approved objective, and which cross-target/hydrogen runs are valid inputs?
- Which master macro and driver are canonical given the multiple historical and specialized variants?
- Is gain/amplitude calibration in scope?
- Does slot 20 channel 40 / logical ID 2696 carry the authoritative reference signal, and why are all 16 slot-20 channels ordinary detector elements in the database?
- How should the 2704-versus-2720 discrepancy be interpreted?
- What is the correct end-to-end TDC unit and conversion to ns?
- What validation criteria and uncertainty budget are required?
