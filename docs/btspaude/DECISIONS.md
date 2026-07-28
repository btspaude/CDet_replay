# Decision log

Use one entry per decision with **Date**, **Status**, **Context**, **Decision**, and **Consequences**. Proposed or superseded choices should be marked accordingly rather than erased.

## Initial documentation location and workflow

- **Date:** 2026-07-27
- **Status:** Accepted
- **Context:** The project needs a durable return-to-work point.
- **Decision:** Documentation lives in `~/CDet_replay/docs/btspaude`. Use dated logs plus a concise current-status page. The user-specific subdirectory distinguishes this documentation set from collaborators' work and reduces naming conflicts.
- **Consequences:** New tasks should read `CURRENT_STATUS.md` and the newest log first; durable changes should update the appropriate focused document.

## Generated calibration products

- **Date:** 2026-07-27
- **Status:** Accepted
- **Context:** Raw/generated artifacts can be large and may not be reproducible without provenance.
- **Decision:** Plots and calibration products are not tracked by default.
- **Consequences:** Small curated artifacts require project-leader approval and documented provenance.

## Branches, commits, and tags

- **Date:** 2026-07-27
- **Status:** Accepted
- **Context:** The parent contains nested repositories and existing work must be protected.
- **Decision:** Work remains on the current branch unless the project leader approves otherwise. Commits require explicit approval; tags require separate explicit approval.
- **Consequences:** Before proposing a commit, identify the owning repository and exact files, and exclude unrelated or pre-existing changes.

## Major technical changes

- **Date:** 2026-07-27
- **Status:** Accepted
- **Context:** Detector and calibration conventions affect physics interpretation and software compatibility.
- **Decision:** Major detector, calibration, or architecture changes require project-leader approval before implementation.
- **Consequences:** Coordinate/numbering, calibration definitions, event selection, physics logic, interfaces/formats, substantial analysis routines, and architecture changes must first be presented with evidence, risks, and alternatives.
