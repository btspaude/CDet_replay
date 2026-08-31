# Calibration notes

This is a code-informed notebook, not an approved calibration prescription or statement of accepted results. Each entry must distinguish implemented behavior, historical notes, verified evidence, assumptions, and open questions.

## Calibration objective

The active cross-target SBS-replay macro implements CDet timing calibration using pixel-to-pixel timing offsets, CDet/ECal timing correlation, and TOT-dependent time-walk correction. The primary pixel offsets now come from `extractAllCDetPixelTimingOffsets(true)`, which fits each pixel's ECal-CDet delta-t peak and aligns valid pixels to one detector-wide uncertainty-weighted centroid. The leading `generateOffsets` argument defaults to `false`, so calling the routine without `true` exits without fitting or changing output files. `extractCDetBarPixelTimingOffsets()` remains the visual bar-by-bar diagnostic.

## Inputs

The master macros read replayed ROOT trees containing `earm.cdet.hits.*` (generic/raw hits), `earm.cdet.hit.*` (the `SBSCDet_Hit` collection), ECal quantities, and selected HCal quantities. The cross-target macro can load a single run or a grouped run list. `crossRuns.txt` currently lists groups of runs from 3573 through 5992; this is evidence of an implemented candidate set, not approval that all are scientifically appropriate.

The cross-target macro reads/writes `CDet_calibration_dt.dat` for individual runs or `CDet_calibration_dt_groupN.dat` for combined groups, with sections for 2,688 pixel offsets, ECal timing parameters, and layer-dependent time-walk parameters. `[PixelOffsets]` uses zero-based physical logical pixel IDs 0–2687. The older `CDet_calibration.dat` is retained as the regular CDet-LE-method comparison file. Record exact code/database revisions and preserve existing calibration files before running a driver: some drivers can delete or replace the active delta-t file.

## Run selection

Document detector/DAQ conditions, target, exclusions, stream/segment ranges, event limits, and selection rationale. Existing drivers default to cross-target run 3575 and hydrogen run 6077, and grouped lists contain many additional runs. These defaults and lists show prior workflow only; the approved run set is unresolved.

## Timing calibration

The current cross-target master macro implements stages 0–8:

| Stage | Implemented purpose |
|---:|---|
| 0 | No calibration / raw inspection |
| 1–2 | Fit, then apply 2,688 detector-referenced pixel timing offsets from ECal-CDet delta-t spectra |
| 3–4 | Fit, then apply CDet/ECal timing correction with pixel offsets |
| 5–6 | Inspect, then fit TOT-dependent time walk |
| 7 | Apply pixel, ECal, and time-walk corrections |
| 8 | Fit CDet/ECal timing with pixel and time-walk corrections but without an existing ECal correction |

The in-session cross-target driver runs an iterative sequence `0 → 1 → 3 → 6`, then `1 → 3 → 6`, followed by stage 7 closure/final passes. The production extraction call is `extractAllCDetPixelTimingOffsets(true)`. For each sufficiently populated physical pixel, the calibration centroid is the Gaussian mean from the local `gaus(0)+pol1(3)` fit over the actual delta-t signal window. Accepted means are combined with inverse-variance weights to form the detector reference, and the stored additive correction is `mu_pixel - mu_detector`. `extractCDetBarPixelTimingOffsets()` uses the same signal-fit centroid and weighting rule for its bar-local reference. The broad background-subtracted spectrum and its clean Gaussian remain diagnostic visuals only and do not determine the constant or veto an otherwise accepted local signal fit. Known unused or rejected pixels receive zero residual. The time-walk model is layer-dependent and proportional to `1/sqrt(TOT) - 1/sqrt(TOT_ref)`.

For a genuinely fresh `CDet_calibration_dt.dat`, preserve and then move/remove the old file before the stage-1 run. If an existing file is present, stage 1 loads and applies it and the subsequent extraction adds residual corrections, which is an iterative closure pass rather than a clean rebuild. Stage 2 applies the pixel offsets stored in `CDet_calibration_dt.dat` (or the selected group file).

## Paired-hit trajectory and timing diagnostics

`plotCDetLayersTimeComp()` accepts a Layer 1 pixel ID and normalizes it to the containing 16-pixel bar. It plots the paired Layer 1 and Layer 2 x positions, ECal x projected onto both CDet planes, and an x-versus-z view across Layer 1, Layer 2, and ECal (`ECal_dist = 6.6 m`). Its selected-bar 4x4 LE canvas is filled only by Layer 1 hits that have an accepted Layer 2 partner and pass the final pair and CDet-ECal timing selections. This canvas is intended to verify that the paired-hit sample retains a timing peak in each pixel.

A roughly 10 ns double peak has been observed for one pixel and is suspected to be crosstalk. Do not interpret a forced single-peak centroid as a validated offset. The interim plan is to exclude the affected pixel until the second population can be identified and removed with a justified crosstalk cut. The pixel ID and code-level exclusion are still outstanding.

The hydrogen master is intentionally still bar-based and behind the active cross-target implementation. Do not assume that cross-target `[PixelOffsets]` files are compatible with the hydrogen workflow until the two paths are deliberately reconciled.

The project leader confirms that the extra DAQ slot is mapped as 16 IDs but only logical ID 2696 is used, as the reference signal. Physical detector pixels are IDs 0–2687. The current cross-target and hydrogen macros correctly select ID 2696 as the event reference.

CDet timing arrays in replayed ROOT files are in 10 ps units. The macros use `TDC_calib_to_ns = 0.01`, so `t [ns] = 0.01 × t [10 ps units]`. The exact upstream offline location that establishes this convention is not yet identified. Continue applying the existing conversion for current analysis; any later unit migration must explicitly address replay output, cuts, constants, plots, and compatibility with existing ROOT files.

## Gain/amplitude calibration, if applicable

`SBSCDet` disables ADC processing by default, and the current master timing calibration sets `nBarsADC = 0`. No CDet gain/amplitude calibration procedure was found in the inspected active path. Establish whether it is applicable before adding one.

## Channel quality

The calibration macro contains bad/hot-channel suppression hooks, per-pixel/per-bar distributions, multiplicity checks, and per-pixel hit-count storage for fitted production offsets. The scientific criteria and approved bad-channel list still need documentation. Keep physical paddles, maPMT pixels, electronics channels, and logical IDs distinct; the macro's variable name `NumPaddles = 16` actually covers every maPMT pixel position.

## Raw occupancy and singles rate

The cross-target macro counts every raw leading-edge entry on physical CDet logical IDs 0–2687, including multiple entries from the same channel in one event. The raw occupancy is the mean hit count per processed event, not the fraction of events with at least one hit. The raw singles-rate estimate assumes a 60 ns observation window per processed event:

`rate [Hz] = raw hit count / (processed events × 60 ns)`.

Its Poisson statistical uncertainty is `sqrt(raw hit count) / observed time`. The corresponding occupancy uncertainty is `sqrt(hit count) / processed events`. The stored rate and uncertainty vectors remain in Hz; the ROOT rate histogram and four-panel layer/side display use kHz. Rate and occupancy displays use the same four-panel layer/side organization. Unused maPMT pixels remain at their logical channel positions but are omitted from the plotted `TGraphErrors`, producing gaps without renumbering the channel axis. This calculation does not apply TOT, ECal, reconstructed-good-hit, multiplicity, or bad-channel cuts. The rate remains conditional on any event-level selection enclosing the raw-hit loop; currently `good_elastic` is forced true.

## ECal reconstructed-cluster rate

The cross-target macro collects every finite entry available in `earm.ecal.clus.e` for every processed event. Collection occurs before the macro's electron/elastic selection, so the analysis adds no electron, elastic, position, timing, or other event-quality cut. Multiple qualifying clusters in one event are counted separately. The processed-event denominator includes every event accepted by the macro's event limit, including events with no ECal cluster.

The rate is calculated only when `calculateECalClusterRate()` is called after the main analysis. For a caller-selected energy threshold,

`rate [Hz] = number of stored clusters with E >= threshold / (processed events × 250 ns)`.

The Poisson statistical uncertainty is `sqrt(passing cluster count) / (processed events × 250 ns)`. The routine prints the processed-event count, stored-cluster count, `nclus`/array-size mismatch count, nonfinite-energy count, threshold, passing-cluster count, rate, and statistical error. It does not write a CSV or separate ROOT output file.

The routine draws the stored reconstructed-cluster energy spectrum, a red dashed line at the selected threshold, and a legend containing the threshold and calculated rate. Its current arguments are `energyThresholdGeV`, `energyBinWidthGeV`, `binMinGeV`, and `binMaxGeV`, in that order. `binMinGeV` and `binMaxGeV` control only the histogram display range; entries outside that range go to ROOT underflow or overflow and remain eligible for the independent rate count. The defaults are a 0.01 GeV requested bin width and a 0–12 GeV display range. The in-memory histogram carries `TParameter` metadata for the 250 ns window, threshold, requested bin width, display bounds, and processed-event count.

Two upstream replay behaviors limit interpretation and are not cuts introduced by this analysis:

- `earm.ecal.emin_clTotal = 0.5` in `db_earm.ecal.dat` means the replay does not retain reconstructed clusters below 0.5 GeV. A spectrum beginning near 0.5 GeV therefore reflects the replay threshold, not the analysis display minimum or a newly applied rate cut.
- `SBSCalorimeter` defaults `fMaxNclus` to 10. `earm.ecal.nclus` reports the full reconstructed-cluster count, while `earm.ecal.clus.e` stores at most ten cluster energies unless `earm.ecal.nmax_cluster` is configured differently during replay. Consequently, events with `nclus > 10` produce an expected count/array-size mismatch and the present analysis cannot count the unstored clusters. Whether this storage limit is intentional for the current ECal workflow is awaiting ECal-expert feedback; do not change it without project-leader approval.

## Coordinate and alignment checks

State the coordinate system, sign convention, units, surveyed or assumed geometry, and comparison method. The current convention says the physically bottom channel corresponds to positive transport `x`.

On 2026-08-31, the cross-target analysis constant was changed from `ECal_dist = 6.6 m` to `ECal_dist = 6 m` to match the surveyed ECal z position. Simulation indicates an additional 13.3 cm displacement of the effective ECal z position relative to the survey geometry. Keep the surveyed mechanical position and simulation-derived effective position distinct in plots and calibration logic. The direction/sign of the 13.3 cm shift still needs to be documented before applying it numerically.

## Validation

Record independent checks, residuals, distributions, comparisons, failure modes, and acceptance criteria. Follow [validation/README.md](validation/README.md) for provenance.

## Uncertainties and systematics

Identify statistical uncertainty, model dependence, run dependence, channel-to-channel variation, selection sensitivity, reference uncertainty, and geometry/alignment effects as applicable. No values are established here.

## Open questions

- Is the current staged timing workflow the approved objective, and which cross-target/hydrogen runs are valid inputs?
- Which master macro and driver are canonical given the multiple historical and specialized variants?
- Is gain/amplitude calibration in scope?
- Where in the offline processing is the 10 ps ROOT-output unit established?
- If the timing representation is changed later, what migration and compatibility plan will prevent mixing old and new ROOT files or constants?
- Should the mostly unused extra-slot IDs eventually be represented differently in the database/software?
- What validation criteria and uncertainty budget are required?
- Is the ECal ten-cluster output cap intentional for the current replay, and must the relevant ROOT files be replayed with a larger `earm.ecal.nmax_cluster` before quoting an all-cluster rate?
