# TODO

Items below are investigations or proposals, not authorization to implement changes.

## Immediate

- Obtain the project leader's current calibration objective, required outputs, acceptance criteria, and run set.
- Select the canonical master macro and driver from the current cross-target, hydrogen, and historical variants.
- Record the code and database revisions relevant to the selected runs.

## Calibration

- Establish timing observables, reference definition, cuts, units, fit/extraction method, and validation criteria.
- Decide whether gain/amplitude calibration is applicable and in scope.
- Define channel-quality criteria and uncertainty/systematic studies.
- Define coordinate/alignment checks using an explicit coordinate convention.

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
