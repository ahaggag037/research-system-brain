# One-file fallback note

The modular V10 architecture is preferred.

If the target environment cannot work with multiple files, concatenate the model files in numeric order, but preserve the state-machine rule:

- treat packaging sections as inactive until DESIGN_CONVERGED;
- treat final-audit sections as inactive until packaging is complete.

This fallback is less ideal because the model can see all later instructions at once.
