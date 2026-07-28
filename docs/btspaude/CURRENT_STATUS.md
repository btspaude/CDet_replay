# Current status

**As of:** 2026-07-28

## Work completed

The initial documentation system was created, updated after a read-only code trace, and committed as parent-repository commit `0e2d9bc`. It now lives under `~/CDet_replay/docs/btspaude`; the user-specific subdirectory distinguishes this documentation set from collaborators' work. No code, database, generated-file, branch, tag, or push change is authorized by this work.

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
- The project leader confirms that physical detector pixels are IDs 0–2687. The extra DAQ slot maps 16 further IDs to make the correct total of 2704, but only ID 2696 is used, as the reference signal; the other 15 are not physical detector channels.
- CDet timing values in replayed ROOT files are in 10 ps units. Current analysis converts them to ns with a factor of `0.01`.
- The active code path is VETROC decoding → generic channel/geometry mapping → `SBSCDet` position-aware LE/TE/TOT hits → replay tree/monitoring output.
- The current calibration macro implements staged bar-offset, ECal-timing, and layer-dependent TOT time-walk corrections; CDet ADC processing is disabled in `SBSCDet` by default.

## Confirmed conventions and unresolved questions

The 2704 mapped total, physical range 0–2687, reference ID 2696, and 10 ps ROOT timing unit are confirmed project conventions. The historical commented 2720 value has no active role. Still unresolved are the upstream location that establishes the timing unit, the full physical-to-logical mapping, and a safe plan for any eventual timing-unit migration. The project leader must also confirm the calibration objective, canonical macro/driver, approved run set, validation criteria, and uncertainty requirements.

## Recommended next steps

With project-leader direction, confirm whether the discovered staged workflow and run lists are current; locate the upstream timing-unit conversion; audit the macro's inconsistent 2692/2704 count constants; and document the physical-to-logical mapping from evidence. Before changing timing units, design and approve a compatibility plan for replay output, analysis cuts, calibration constants, and existing ROOT files.
