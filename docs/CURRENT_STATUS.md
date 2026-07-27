# Current status

**As of:** 2026-07-27

## Work completed

The initial documentation system under `~/CDet_replay/docs` has been created and updated after a read-only trace through the CDet implementations in SBS-offline and SBS-replay. This work authorized documentation only; it did not authorize code, database, generated-file, branch, tag, or push changes. A documentation commit still requires workflow approval.

## Fresh Git observations before documentation edits

- Parent `CDet_replay`: branch `main`, tracking `origin/main`; nested paths `SBS-offline`, `SBS-replay`, and `jlab-HPC` were reported modified, and `git-repo/sbs_devel/build/.rootrc` was modified.
- `SBS-offline`: branch `master`, tracking `origin/master`; untracked `CDET_REPLAY_REVIEW.md`.
- `SBS-replay`: branch `tdccalib`, tracking `origin/tdccalib`; deleted `scripts/cdet/CDet_calibration.dat` and modified `scripts/cdet/PlotElastic_Calibration_Master_stageflag_singlefile_crosstarget.C`.
- `jlab-HPC`: branch `cdet`, tracking `origin/cdet`; no file changes shown by concise status.
- `git-repo/panguin` and `CDet`: detached HEADs; no file changes shown by concise status.

These are observations, not changes made by this documentation task. Treat all pre-existing changes as protected user work. Recheck status before future work.

## Confirmed detector/database facts

- CDet has 2 layers, 6 modules, 12 sides, 168 bars/maPMTs, 2688 total maPMT pixels, 336 unused pixels, and 2352 instrumented paddles.
- Physical ordering is bottom-to-top and then left-to-right; the physically bottom channel is positive transport `x`.
- The active database file contains a 2704-entry detector map/channel map and declares `4 × 676 = 2704` logical elements.
- Its `nchan = 2720` line is commented out.
- `SBSGenericDetector` constructs 2704 ordinary elements from the current database. The channel map does not use its `-1000` special-reference marker.
- Current cross-target and hydrogen calibration macros explicitly select logical ID 2696 as the event reference. By database order this is crate 63, slot 20, channel 40.
- The active code path is VETROC decoding → generic channel/geometry mapping → `SBSCDet` position-aware LE/TE/TOT hits → replay tree/monitoring output.
- The current calibration macro implements staged bar-offset, ECal-timing, and layer-dependent TOT time-walk corrections; CDet ADC processing is disabled in `SBSCDet` by default.

## Recollection and unresolved questions

Use of ID 2696 is now verified in current calibration code, but its authoritative hardware/physics role is not. The meaning of all 16 final mapped channels, the 2704-versus-2720 discrepancy, the physical-to-logical mapping, and the end-to-end TDC units remain unresolved. The project leader must still confirm the calibration objective, canonical macro/driver, approved run set, validation criteria, and uncertainty requirements.

## Recommended next steps

With project-leader direction, confirm whether the discovered staged workflow and run lists are current; reconcile TDC units; verify slot 20 channel purposes from DAQ records; audit the macro's inconsistent 2692/2704 count constants; and document the physical-to-logical mapping from evidence. Obtain approval before implementing any resulting major change.
