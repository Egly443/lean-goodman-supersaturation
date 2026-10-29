# Instructions for AI agents

This repository is a Mathlib candidate, also intended for LeanPool. Standing instructions from the
owner:

- Every Lean file starts with exactly this header, using the current year and never a
  placeholder:

  ```
  /-
  Copyright (c) 2026 Brian Sanders. All rights reserved.
  Released under Apache 2.0 license as described in the file LICENSE.
  Authors: Brian Sanders
  -/
  ```

- Every pull request, here, to LeanPool or upstream to Mathlib, states in its description that
  the code was AI-generated with human guidance.
- Keep the code LeanPool-compliant: no `sorry`, `set_option`, `nolint` waivers or diagnostic
  commands (`#check`, `#print`, ...) in Lean files, and no warnings in `lake build`.
