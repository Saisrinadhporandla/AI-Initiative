---
name: eggplant-live-sut-script-generation
description: Generate and execute reliable Eggplant Functional SenseTalk UI tests from Excel test steps by studying official EPF documentation, sample scripts, and the live SUT. Use whenever creating, debugging, or running Eggplant scripts that must identify the correct UI element among multiple matches.
---

# Eggplant Functional: documentation-led, live-SUT script generation

## Mission

Generate a correct, executable SenseTalk test from the supplied Excel test steps. The test must interact with the live SUT through Eggplant Gateway and Eggplant Studio, observe the actual screen, choose the intended UI element unambiguously, and verify the result.

Do not produce a script based only on guessed OCR strings, assumed coordinates, or generic examples. Documentation, user-provided sample scripts, and live SUT evidence are the sources of truth.

## Required inputs and locations

Use the active workspace and user-specified paths. For the current ENOVIA test, the supplied workbook is:

`enovia_advanced_search_part_test_steps(1).xlsx`

Workbook sheets:
- `Test Cases`
- `Test Steps`
- `Validation Checklist`
- `Read Me`

Current test case:
- ID: `TC_ENOVIA_001`
- Goal: open 3DSearch Advanced Search, use Source `Collaborative Spaces`, select Type `Part`, run search, inspect result `N9910-60002` revision `013`, review properties and Related Items.

Do not assume this test case or workbook path applies to a later task. Read the workbook actually supplied for the current task.

## Source-of-truth order

1. Official Eggplant Functional documentation: https://docs.eggplantsoftware.com/epf/
2. User-provided sample scripts and common handlers.
3. The current live SUT screen and actual execution results.
4. Excel test case, steps, validation checklist, and notes.
5. General SenseTalk knowledge only when the above sources do not cover a point; clearly identify it as unverified and do not silently treat it as documented.

## Mandatory documentation study

Before writing or changing SenseTalk:

1. Open the official EPF documentation site.
2. Identify the documentation sections relevant to the planned commands and language constructs. At minimum investigate:
   - SenseTalk syntax, handlers, parameters, local variables, scope, and default parameter values.
   - `ImageFound`, `EveryImageLocation`, `FoundImageLocation`, `imageRectangle`, and image location collections.
   - OCR/text search options, case sensitivity, valid words, contrast, DPI, ignored spaces/newlines, and search rectangles.
   - `Click`, `click`, `moveTo`, keyboard input, scrolling, and image/text interaction.
   - `WaitFor`, `wait`, repeat loops, `repeatindex()`, exit behavior, and timeouts.
   - Screen capture and image capture/asset storage, if an image asset is needed.
   - Suite information, images folder, properties, and search-area configuration.
3. For every command or property used in the final script, verify its syntax and semantics in the docs or in a known-good sample script.
4. Record the relevant documentation page/title and the rule confirmed. Do not claim to have read every page of the entire documentation site; study all sections relevant to the actual script.
5. If a documentation page cannot be accessed, report that limitation and do not invent the missing syntax.

## Mandatory sample-script review

Read the supplied common script and relevant sample scripts in full enough context to understand:
- Handler declarations and exact parameter names.
- Whether parameters are positional or named and their defaults.
- Search rectangle format and how named search areas are resolved.
- How image and OCR matches are distinguished.
- How multiple matches are returned and selected.
- How success, error, and screenshot logging work.
- How scroll loops stop and report failure.

Do not copy a helper blindly. Compare its declared parameter names with every variable referenced in its body. Flag inconsistencies before reuse.

### Important review notes for the supplied common handlers

The supplied `ClickElement` example declares parameters `textLabel`, `SearchRectangle`, and `waitTime`, but its body refers to `label`, `SR`, and `text`. These names do not match. Do not use this handler as-is until the mismatch is resolved against the official SenseTalk parameter rules and the intended variable names.

The supplied `IsElementPresentOnScreen` handler declares `textToSearch` and `SR`, but its first line checks `label` and later sets `text`; these are also inconsistent with the declared arguments. Review and correct only with evidence from docs and the user's authorization.

The supplied `scrollTo` handler uses `toFind`, `SR`, `scroll`, and `scrollSpeed`; verify all command names and options, and ensure its loop has an explicit success/failure result. A loop that exits after ten attempts must not be treated as if the target was found.

The sample `launchApp` handler uses Windows keyboard shortcuts and a hard-coded Windows Start image. Do not use it automatically for an already-open SUT or a different environment. Verify its suitability first.

The line `click item 1 of EveryImageLocation(text:"Add to cart")` illustrates selecting a match by collection index. It is not automatically safe: first prove that item 1 is the intended target from the live screen and search scope. Never assume the first match is correct.

## Live SUT interaction is mandatory

Use the already configured and connected Eggplant Gateway connection selected by the user. Inspect the current screen before acting.

For each Excel step:
1. Read the action, target/test data, expected result, validation type, and automation notes.
2. Observe the live screen.
3. Identify the target and determine whether it is unique in the relevant screen region.
4. Choose a documented interaction strategy.
5. Perform one action.
6. Wait for the expected state and verify it on the live screen.
7. Capture/log evidence and continue only when the step succeeded.

Do not generate a speculative full script first and only then attempt to run it. Develop the script from verified live interactions, then execute the completed script from a known starting state.

Never claim the test passed unless the script was executed and the required assertions were observed.

## Correct element identification and duplicate matches

Eggplant is visual UI automation. OCR text can occur multiple times. A text match alone is not proof that the match is the intended control.

For every target, assess:
- How many matches are on screen?
- Are matches inside the expected panel, dialog, row, card, or section?
- Is the target associated with the correct label/value/record?
- Is there a stable visual context or nearby unique text?
- Does the match location correspond to the expected control in the live screenshot?

### Search rectangle (SR) decision rules

A search rectangle is required whenever:
- The same text/image appears more than once on screen.
- The screen contains repeated rows, cards, menus, tabs, buttons, or table cells.
- A match must be constrained to a specific dialog, panel, result row, detail pane, or related-items area.
- A full-screen search could return a different instance than the intended target.
- The user/sample helper requires an SR for the operation.

A search rectangle may be omitted only when:
- The target is demonstrably unique on the entire visible screen, and
- The relevant documentation confirms the operation's default search scope, and
- The live SUT confirms there is no competing match.

Never default to `[0,0,1920,1080]` as a substitute for identifying the actual target region. Do not assume the SUT resolution is 1920x1080.

### How to create an SR from the live screen

1. Capture or inspect the current SUT screenshot using a documented Studio/Gateway capability.
2. Identify the visible boundaries of the relevant UI container (for example, the search dialog, left filter panel, result row, part details pane, or Related Items table).
3. Define the rectangle using the actual screen coordinate convention and actual screenshot dimensions confirmed by the tools/docs.
4. Keep the rectangle as tight as practical while including the complete target text/control and enough context to distinguish it.
5. If the target moves, the layout changes, or the page scrolls, inspect again and recalculate the SR. Do not reuse stale coordinates.
6. Prefer a named, reusable search area only when the suite's properties/search-area configuration supports it and the area is genuinely stable.
7. Log the screen evidence and explain why the SR isolates the intended target.

Coordinates may be used to define a search rectangle when the user permits it; they must not be used as blind click coordinates. Click the location returned by a verified image/text matching operation, not an assumed fixed point.

If the tool cannot provide screen dimensions or a screenshot from which an SR can be grounded, do not invent one. Report the missing evidence and ask for the screenshot or a supported capture method.

## Multiple match handling

When using `EveryImageLocation` or another multiple-match API:
1. Determine and record the number of matches.
2. Inspect the match locations and their relationship to the UI structure.
3. Select by verified context (correct row/panel/card) rather than blindly selecting item 1.
4. If the result order is not documented/stable, do not rely on ordering.
5. If ambiguity remains, stop and report exactly what cannot be distinguished. Do not click.

## Common handler policy

The user supplied examples for:
- `launchApp`
- `Success`
- `Error`
- `ClickElement`
- `scrollTo`
- `IsElementPresentOnScreen`

Treat them as user-provided examples, not automatically valid library code. Verify their syntax against official docs and check variable names, return values, error paths, and match selection. Do not silently rewrite shared common handlers. Prefer adding test-specific code or a new handler only when authorized. Keep changes scoped to files the user explicitly permits.

## Excel-driven test design

Use the workbook as the test specification:
- `Test Cases`: objective, preconditions, data, expected outcome.
- `Test Steps`: ordered actions and expected results.
- `Validation Checklist`: required and recommended assertions.
- `Read Me`: environment and evidence limitations.

Preserve the workbook's step order and distinguish:
- Required assertions from recommended checks.
- Observed values from stable expected values.
- Environment-specific values from values guaranteed by the test data.
- What the recording visibly proves from what it does not prove.

For the current `TC_ENOVIA_001`, do not assume result count or order is stable. The observed part data is `N9910-60002`, revision `013`, description `Tested Battery-rechargeable Li-Ion 70WH`, Maturity State `Preliminary`, Owner `SDE - COS`, Status `Production`. Confirm these against the live SUT before treating them as hard assertions.

## Script quality requirements

- Use valid SenseTalk syntax verified against the docs/sample scripts.
- Use consistent parameter names and clear variable names.
- Keep handlers small and explicit.
- Every search/click must be scoped appropriately.
- Every wait must be followed by a meaningful verification when the step changes UI state.
- Every loop must have a clear success condition, timeout/attempt limit, and explicit failure path.
- Do not log success unless the condition was actually met.
- Do not swallow errors or continue after a required step fails.
- Do not use arbitrary sleeps as the only synchronization strategy when a documented wait-for-condition is available.
- Do not invent image assets, labels, OCR text, screen coordinates, or UI state.
- Do not use browser DOM, Selenium, WebDriver, or other non-Eggplant UI automation unless explicitly requested.
- Do not perform destructive actions or submit/commit transactions outside the test scope.

## Execution and completion report

After writing:
1. Syntax-check or load the script using the available Eggplant Studio capability.
2. Execute against the live SUT from the documented starting state.
3. Review the actual execution log and screenshots.
4. Fix failures and rerun only when safe and authorized.
5. Report:
   - Script path.
   - Actual SUT actions performed.
   - Search rectangles used and why.
   - Commands/parameters verified from documentation.
   - Pass/fail result for each required validation.
   - Any remaining blocker with exact error/evidence.

Never say "passed" based only on script creation or a successful text match before the action.
