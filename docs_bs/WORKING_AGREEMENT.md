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

## Git and existing work

- Work normally remains on the current branch. A safer alternative may be proposed, but the project leader decides.
- Commits require explicit approval. Tags require separate explicit approval.
- Before a proposed commit, identify the owning repository and exact files.
- Never stage or commit unrelated or pre-existing user changes.
- Inspect parent and relevant nested repository status before and after work.
- Identify sensible checkpoints, but do not create them without approval.
- Track plots and calibration products only when explicitly requested.

All existing changes are treated as user work and protected unless the user explicitly says otherwise.
