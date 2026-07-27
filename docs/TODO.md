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

- Confirm from DAQ configuration or an authoritative note that slot 20 channel 40 / logical ID 2696 is the intended reference; its use in current macros is already verified.
- Resolve the mapped/grid total of 2704 versus the commented `nchan = 2720` value.
- Determine the role of logical IDs 2688–2703 and the final DAQ-slot channels.
- Document the authoritative physical-to-logical mapping while keeping paddles, bars, maPMT pixels, electronics channels, and logical IDs distinct.
- Reconcile decoder, replay-label, and calibration-macro TDC units and conversions.
- Audit the master macro's `nTdc`/reference count declarations, whose expression and `2704` comment currently disagree.

## Documentation

- Update `CURRENT_STATUS.md` and add a dated log at meaningful checkpoints.
- Record approved technical decisions in `DECISIONS.md`.
- Add calibration evidence and provenance without duplicating source/database contents.
- Review whether durable project guidance should be proposed for the applicable `AGENTS.md`.

## Later

- Propose curated validation summaries after objectives and provenance requirements are established.
- Identify a sensible Git checkpoint and exact owning-repository file list; wait for explicit commit approval.
- Consider tracking small plots or calibration products only with project-leader approval.
