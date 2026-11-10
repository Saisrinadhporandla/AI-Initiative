# Eggplant Functional project rules

For any Eggplant Functional/SenseTalk script generation or debugging task:

- Invoke the `eggplant-live-sut-script-generation` skill.
- Treat https://docs.eggplantsoftware.com/epf/, user-supplied sample scripts, the current live SUT, and the current Excel workbook as the evidence sources.
- Verify syntax and command semantics before using them. Never invent handlers, parameters, OCR text, image names, or coordinate values.
- Inspect the live SUT and build scripts from observed successful interactions; do not write speculative scripts without live verification.
- Detect duplicate text/image matches. Never click the first match without proving it is the intended element.
- Use a search rectangle when matches repeat or when the intended panel/row/dialog must be isolated. Derive it from a current screenshot and actual screen dimensions; never assume a 1920x1080 screen or reuse stale rectangles.
- A search rectangle may scope visual/OCR matching. It is not permission to blind-click fixed coordinates. Click only a location returned by a verified Eggplant match.
- Inspect all matches before selecting from `EveryImageLocation`; do not rely on undocumented match ordering.
- Review supplied common handlers for parameter-name mismatches and incorrect variable references before reuse.
- Execute the completed script against the live SUT and report actual logs/evidence. Script creation alone is not a passing test.
- Modify only files explicitly authorized by the user. Keep reference/sample suites read-only unless expressly authorized.
