# Generate and execute an Eggplant Functional test from the supplied Excel workbook

Use the `eggplant-live-sut-script-generation` skill.

## Current task
Generate and execute the Eggplant Functional SenseTalk script for the test case in the supplied Excel workbook. For the current workbook, use `TC_ENOVIA_001`.

## Mandatory sequence
1. Read all four workbook sheets: Test Cases, Test Steps, Validation Checklist, and Read Me.
2. Read the user-provided common script and sample scripts. Check handler parameter names against variables used inside each handler. Do not assume the supplied common handlers are correct as written.
3. Study the relevant official Eggplant Functional documentation at https://docs.eggplantsoftware.com/epf/. Verify the syntax and behavior of every command/parameter used. Do not claim to have read the entire docs site; list the relevant documentation pages/rules you actually verified.
4. Connect to the user's already configured live SUT through Eggplant Gateway. Do not change connections or reconnect unless asked.
5. Inspect the live screen. For each Excel step, identify the intended target, count duplicate matches, and decide whether a search rectangle is required.
6. When an SR is needed, capture/inspect the actual screen, establish actual dimensions, and define a tight rectangle around the correct panel/row/dialog/control. Never assume 1920x1080. Never use a stale SR after scrolling or navigation.
7. Interact with the SUT one step at a time using documented Eggplant visual/OCR operations. Verify the resulting UI state after each action.
8. Handle multiple matches safely. Do not blindly click the first match or rely on undocumented result ordering. Use `EveryImageLocation` only after inspecting match count and positions and proving which match belongs to the intended control.
9. Generate the script from the verified working interactions, using valid SenseTalk syntax and the sample-suite conventions.
10. Execute the script against the live SUT, inspect logs/screenshots, fix failures, and rerun when safe.
11. Modify only the authorized output script and any specifically authorized assets. Do not change shared common handlers, sample scripts, or other files without approval.

## Current workbook facts
`TC_ENOVIA_001`: open Advanced Search; confirm Source = Collaborative Spaces; choose Type = Part; run Search; inspect results; open part N9910-60002 revision 013; verify displayed properties; inspect Related Items.

Observed values in the workbook:
- Description: Tested Battery-rechargeable Li-Ion 70WH
- Maturity State: Preliminary
- Owner: SDE - COS
- Status: Production
- Related item: N9910-60002, Part, revision 013

Treat the result count/order and observed properties as environment-dependent until confirmed live. Do not add actions beyond the workbook scope.

## Final response
Report the generated script path, actual live interactions, SR decisions/regions, documentation sections checked, execution result, and precise blockers. Do not claim success unless the live script execution and required assertions actually passed.

## Stop conditions
If official documentation is inaccessible, report which syntax could not be verified. If the SUT screen cannot be inspected or a target remains ambiguous, stop before clicking and request the missing screenshot/access. Do not invent coordinates, image names, OCR strings, or successful outcomes.
