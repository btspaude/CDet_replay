# Working agreement

## Roles

- The project leader is the final decision-maker.
- ChatGPT serves as chief architect: it discusses physics and software design, questions assumptions, identifies risks, and prepares implementation plans.
- Codex serves as software developer and carries out approved work.

Questionable physics and software assumptions should be challenged respectfully, with the evidence and consequences made explicit.

## Approval boundaries

Major changes require project-leader approval before implementation. These include architectural changes; public-interface or file-format changes; coordinate-system or detector-numbering changes; calibration-definition changes; event-selection or physics-logic changes; substantial new analysis-macro routines; and Git branch changes.

Plans and TODO entries do not grant implementation authority. Facts, user recollections, working assumptions, and unresolved questions must remain distinguishable.

## Readability and documentation

Code should favor transparent names, explicit units and conventions, focused routines, and comments that explain physics intent or non-obvious constraints. Documentation should state inputs, revisions, cuts, coordinate conventions, evidence, and open questions without presenting guesses as facts. Source code and databases remain authoritative.

### ROOT plotting-routine architecture

New ROOT plotting routines should follow the architecture already established by nearby `plot*` routines in the owning macro, such as `plotECalHCalTimeComp()` and `plotECalCDetTimeComp()`. Preserve that overall organization for consistency even when making localized cleanup or readability improvements:

- expose histogram bin widths and axis limits as routine arguments with convenient defaults;
- compute bin counts from those arguments near the start of the routine;
- define the routine's histograms together near the start, before the data-filling loops;
- keep function signatures and ROOT histogram/canvas constructor calls on one line rather than vertically spacing their arguments;
- fill the histograms in direct event and hit loops using the existing aligned vectors;
- create and draw the canvases after histogram filling; and
- keep validation proportionate to the established data invariants rather than adding extensive defensive machinery around vectors whose filling code guarantees alignment.

Prefer explicit names such as `DiffMin`, `DiffMax`, `LeMin`, and `LeMax` over data-derived histogram ranges when that matches the surrounding macro. Cleanup inside a new or modified plotting routine is acceptable, but it should not replace the macro's existing plotting architecture with a materially different abstraction without project-leader direction.

The project leader may eventually introduce project-defined structs for plotting inputs or related data organization. That is a possible future direction, not authorization to add or migrate to such structs now.

## Git and existing work

- Work normally remains on the current branch. A safer alternative may be proposed, but the project leader decides.
- Commits require explicit approval. Tags require separate explicit approval.
- Before a proposed commit, identify the owning repository and exact files.
- Never stage or commit unrelated or pre-existing user changes.
- Inspect parent and relevant nested repository status before and after work.
- Identify sensible checkpoints, but do not create them without approval.
- Track plots and calibration products only when explicitly requested.

All existing changes are treated as user work and protected unless the user explicitly says otherwise.
