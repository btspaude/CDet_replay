# Project context

## Scope

This repository supports CDet calibration work in the GEp-V thesis context. The present focus is establishing and validating the CDet calibration context; detailed physics claims, a final calibration objective, run selection, and results have not yet been established in this documentation.

## Software environment

The working environment primarily uses C++, ROOT, Hall A Analyzer/Podd, SBS-offline, SBS-replay, and shell scripts. Changes must remain compatible with SBS-offline and SBS-replay unless the project leader approves otherwise. `JLAB_INSTALL_DIR` points to `~/CDet_replay`. Useful navigation aliases include `gocdet`, `gocdetcode`, `gocdetdb`, `gocdetreplay`, `gocdetrun`, and `gocdetscripts`.

## Repository layout

`~/CDet_replay` is the parent Git repository. It contains nested repositories/submodules, so a clean-looking parent status does not prove that every nested worktree is clean.

| Purpose | Path |
|---|---|
| Active CDet database | `git-repo/sbs_devel/SBS-replay/DB/db_earm.cdet.dat` |
| CDet analysis scripts | `git-repo/sbs_devel/SBS-replay/scripts/cdet` |
| Replay directory | `git-repo/sbs_devel/SBS-replay/replay` |
| SBS-offline | `git-repo/sbs_devel/SBS-offline` |
| Other nested repositories | `git-repo/sbs_devel/jlab-HPC`, `git-repo/panguin`, `CDet` |

The implemented CDet replay path is:

```text
SBS-replay/replay/replay_CDet.C
  -> Podd MultiFileRun reads EVIO streams/segments
  -> SBS-offline/VETROCcdet decodes VETROC words
  -> SBSGenericDetector applies the database detector/channel maps
  -> SBSCDet forms position-aware leading-edge/trailing-edge/TOT hits
  -> SBS-replay/replay/replay_CDet.odef selects tree output and monitoring plots
```

The dedicated replay constructs `SBSCDet` as `earm.cdet`, enables raw-hit storage, and adds it to the GEp electron arm alongside ECal. The most relevant implementation files are `SBSCDet.{h,cxx}`, `SBSCDet_Hit.{h,cxx}`, `SBSGenericDetector.{h,cxx}`, and `VETROCcdet.{h,cxx}` in SBS-offline, plus the CDet replay and calibration macros in SBS-replay.

Before proposing a change, identify which repository owns each file and inspect both the parent and relevant nested status. Existing changes must be preserved. As inspected on 2026-07-27, the relevant nested revisions were SBS-offline `3a6f6ef9f8ea4360fa7dae90612e5cf096f622b4` and SBS-replay `6a3e9e8c07103e1ee7617173d63d323a81caab88`; both worktrees also contained pre-existing user changes, so a revision alone does not fully identify the inspected source state.

## Detector overview

CDet is a scintillating coordinate detector with two layers and three modules per layer. A mirror divides each module into left and right sides. Each side has 14 bars; each bar contains 14 scintillator paddles and is read by one 16-pixel multi-anode photomultiplier tube (maPMT). Fourteen pixels carry paddle signals and two are unused. See [DETECTOR_MODEL.md](DETECTOR_MODEL.md) for arithmetic and conventions.

## Before making changes

Read the current status, newest dated log, working agreement, relevant source or database files, and applicable repository guidance. Confirm the current branch and worktree state in every owning repository. For calibration work, also identify the objective, run set, revisions, cuts, units, coordinate convention, and the evidence supporting any channel mapping. Major changes require project-leader approval.
