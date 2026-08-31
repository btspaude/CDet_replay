# Current status

**As of:** 2026-08-30

## Active calibration workflow

The active development path is the cross-target master macro and its cross-target drivers on the SBS-replay `tdccalib` branch. Production timing offsets use one constant per physical CDet logical pixel. The primary cross-target method is `extractAllCDetPixelTimingOffsets(true)`: it fits each pixel's `tECal - tCDetLE` peak with `gaus(0)+pol1(3)` over the requested signal window, calculates an inverse-variance-weighted detector centroid from accepted signal-fit means, and writes the additive residual `mu_pixel - mu_detector`. The broad background-subtracted distribution is retained for visualization and diagnostics only; its fitted mean is not used for the calibration constant and failure of that secondary diagnostic no longer rejects an otherwise valid signal fit. The same centroid rule is used by `extractCDetBarPixelTimingOffsets()` and its weighted bar reference.

The primary cross-target master file is `CDet_calibration_dt.dat` for individual runs and `CDet_calibration_dt_groupN.dat` for combined-run groups. It holds all 2,688 zero-based `[PixelOffsets]` entries together with `[ECalTiming]` and `[TimeWalk]`, so every calibration stage reads and updates the same file. The legacy LE-method `CDet_calibration.dat` remains separate for comparison. A loaded pixel-offset section is accepted only when all 2,688 entries are present.

Commit `3b4624f` introduced the pixel-level production system and is tagged `pixel-offsets-v1`. The current signal-fit centroid behavior was committed and pushed as `9e2229a` (`Use signal fits for CDet pixel timing offsets`) and passed ROOT ACLiC compilation. Real-data constants still need to be regenerated and inspected on ifarm before they are treated as validated calibration products.

The hydrogen master and hydrogen drivers remain on their older bar-based implementation by explicit project-leader decision. They will be reconciled after the current cross-target tests mature. Archived and legacy macros remain untouched.

## Selected-bar paired-hit diagnostics

`plotCDetLayersTimeComp()` now accepts a Layer 1 pixel base/ID and normalizes it to the containing 16-pixel bar. For example, pixel 429 selects bar 26 and pixels 416–431. The routine keeps its original architecture while providing full selected-bar diagnostics: matched Layer 2 versus Layer 1 x position, ECal x projected to the Layer 1 and Layer 2 planes, three-plane x occupancy, and an x-versus-z trajectory display using the Layer 1, Layer 2, and actual ECal x positions.

The routine also produces a 4x4 per-pixel leading-edge spectrum canvas for the selected Layer 1 bar. Those spectra are filled only when the Layer 1 hit participates in an accepted CDet pair with a matched Layer 2 hit and passes the final delta-t/delta-x and CDet-ECal timing selections. This directly tests whether each paired pixel retains a recognizable timing peak. Pixels with more than 20 entries receive the existing Gaussian display fit; unused channels are marked. The existing four-panel delta-t-versus-position canvas remains enabled.

The selected-bar cleanup and position work was committed and pushed in `a43c265`, `88f147f`, and `33e2ee9`. ROOT ACLiC compilation passed after these changes; the remaining eight compiler warnings predate this routine.

As of 2026-08-31, the ECal z position used by the cross-target analysis was corrected to match the survey geometry (`ECal_dist = 6 m`) and pushed in `a821d58`. Simulation studies indicate that the effective ECal z position is shifted by 13.3 cm relative to the surveyed position. The sign/direction of that effective shift is not recorded here and must be stated explicitly before it is incorporated into a projection or correction.

`plotECalCDetTimeCutStudy()` remains available for detector-wide and selected-pixel timing studies. `extractCDetBarPixelTimingOffsets()` normalizes a supplied pixel ID to its 16-pixel bar, classifies local signal fits, and calculates bar-local relative corrections using signal-fit means.

One pixel has shown a second LE/delta-t peak about 10 ns from the main peak, suspected to be crosstalk. The current preferred interim policy is to exclude that pixel from calibration rather than force a centroid until the crosstalk population can be identified and cut. The affected pixel ID and an explicit exclusion mechanism have not yet been recorded/implemented.

## Rates work

Raw CDet singles-rate and occupancy routines are implemented using all physical raw LE entries and explicit observation-window conventions. The ECal reconstructed-cluster rate routine counts stored `earm.ecal.clus.e` entries above a caller-selected threshold using a 250 ns event window. Interpretation remains limited by the replay-level 0.5 GeV cluster threshold and the default ten-cluster storage cap; collaborator feedback on the intended ECal storage configuration is pending.

## Confirmed detector conventions

- CDet has 2 layers, 6 modules, 12 sides, 168 bars/maPMTs, 2,688 total pixel slots, 336 unused pixels, and 2,352 instrumented physical pixels.
- Physical detector IDs are 0–2687. Logical ID 2696 is used as the event reference from the extra mapped DAQ slot.
- Replayed CDet timing values are in 10 ps units and current analysis converts them to ns with `0.01`.
- The physically bottom channel corresponds to positive transport `x`.

## Immediate validation priorities

1. Pull `tdccalib` on ifarm and inspect the selected-bar paired-hit LE and x-versus-z diagnostics on real data.
2. Record the double-peaked pixel ID and decide how to represent its temporary exclusion; then develop a crosstalk-identification cut before attempting to recover it.
3. Regenerate a fresh cross-target `[PixelOffsets]` calibration from real data using the signal-fit centroids and inspect channel coverage, fit statuses, low-statistics pixels, residual alignment, and closure passes before treating it as usable.
4. Compare the legacy CDet-LE and primary ECal-CDet delta-t pixel offsets with `plotCDetPixelOffsetMethodDifference()`; it plots delta-t minus legacy correction and omits pixels where either value is exactly zero.
5. Reconcile the hydrogen workflow after the cross-target implementation and validation choices stabilize.

## Protected working-tree state

The SBS-replay source repository was clean at the last check after `9e2229a` was pushed to `origin/tdccalib`. The parent documentation tree contains the ongoing note edits, and other nested repositories may contain unrelated work. Recheck every owning repository and preserve unrelated changes before future commits.
