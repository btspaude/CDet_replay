# TODO

Items below are investigations or proposals, not authorization to implement changes.

## Immediate

- Pull `origin/tdccalib` on ifarm and inspect the new selected-bar paired-hit LE spectra and x-versus-z trajectory plots on real data.
- Record the logical pixel ID with the approximately 10 ns double peak and add an explicit temporary calibration exclusion only after confirming the intended mechanism.
- Regenerate a fresh `CDet_calibration_dt.dat` from stage 1 plus `extractAllCDetPixelTimingOffsets(true)`, then run stage 2/closure checks and review fit-status coverage.
- Record the exact run set and code/database revisions used for the regenerated constants.

## Calibration

- Establish final timing cuts and validation criteria; the implemented centroid is now the local `gaus+pol1` signal-fit mean and the background-subtracted fit is visual only.
- Study how to identify and cut crosstalk hits, then determine whether the currently excluded double-peaked pixel can be recovered without bias.
- Decide whether gain/amplitude calibration is applicable and in scope.
- Define channel-quality criteria and uncertainty/systematic studies.
- Define coordinate/alignment checks using an explicit coordinate convention.
- Record the sign and simulation provenance of the 13.3 cm ECal effective-z shift, and decide whether each projection should use the surveyed 6 m position or the simulation-derived effective position.

## Detector/database verification

- Preserve the confirmed mapping convention: IDs 0–2687 are physical detector pixels; ID 2696 is the reference in the extra 16-channel slot; the other 15 extra-slot IDs are not physical CDet channels.
- Consider removing or explaining the historical commented `nchan = 2720` only as part of a separately approved database cleanup; 2704 is the confirmed active mapped total.
- Document the authoritative physical-to-logical mapping while keeping paddles, bars, maPMT pixels, electronics channels, and logical IDs distinct.
- Locate and document where upstream offline processing establishes the 10 ps units stored in CDet ROOT branches.
- Audit the master macro's `nTdc`/reference count declarations, whose expression and `2704` comment currently disagree.

## Documentation

- Update `CURRENT_STATUS.md` and add a dated log at meaningful checkpoints.
- Record approved technical decisions in `DECISIONS.md`.
- Add calibration evidence and provenance without duplicating source/database contents.
- Review whether durable project guidance should be proposed for the applicable `AGENTS.md`.

## Later

- Design a backward-compatible migration plan before proposing any change from 10 ps ROOT timing units; include existing ROOT files, cuts, constants, and plots.
- Propose curated validation summaries after objectives and provenance requirements are established.
- Identify a sensible Git checkpoint and exact owning-repository file list; wait for explicit commit approval.
- Consider tracking small plots or calibration products only with project-leader approval.
