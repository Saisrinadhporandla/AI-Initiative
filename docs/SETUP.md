# Setup Guide: VS Code + GitHub Copilot + Eggplant MCP

## 1. Prepare the workspace
1. Open the root folder of your automation project in VS Code.
2. Copy `.github/`, `docs/`, and `README.md` from this kit into that root folder.
3. Place the Excel workbook at `input/test-cases.xlsx`, or edit the reusable prompt to use your real path.
4. Make the reference suite available under `reference-suite/`, or update the path. If it is in a different folder, ensure Copilot can read it.
5. Create `output/generated_scripts/` if it does not exist.
6. Keep the reference suite under source control or otherwise backed up. Do not use the reference suite as the output folder.

## 2. Configure MCP servers
1. Open the VS Code workspace where you use GitHub Copilot Agent Mode.
2. Open your existing MCP configuration and confirm both Eggplant Studio MCP and Eggplant Gateway MCP are configured and enabled.
3. Use the MCP server-management UI in your installed VS Code version to list the tools each server exposes.
4. Start or reload the MCP servers if required, then start a fresh Copilot Agent session.
5. Ask Copilot to list the available tools from each server and summarize their documented purpose before generating anything.
6. Verify that the Gateway tools can see the intended test SUT and that Studio tools are available.
7. Do not paste passwords, access tokens, or private connection strings into prompts or checked-in files.

MCP configuration format and file location can vary with the server package and VS Code version. Preserve your existing working server configuration; do not replace it with a guessed configuration. If a server is not listed, fix that connection before generation.

## 3. Check Copilot custom instructions and skills
1. Ensure `.github/copilot-instructions.md` is in the workspace root at that exact relative path.
2. Ensure the skill file is at `.github/skills/eggplant-script-generation/SKILL.md`.
3. Ensure the prompt is at `.github/prompts/generate-eggplant-scripts.prompt.md`.
4. Update VS Code/GitHub Copilot if your version does not support repository-level Agent Skills or prompt files. Feature support and discovery can vary by extension/version.
5. In Copilot Chat, select Agent mode. Start by asking it to inspect the workspace, read the skill, and discover the configured MCP tools. Do not immediately ask it to run a large batch.

## 4. First safe test
Use one simple, non-destructive test case first:
1. Confirm the Excel and reference suite paths.
2. Ask Copilot to inspect, but not edit, the reference suite.
3. Ask it to inspect the current SUT screen using available Gateway tools.
4. Run the `generate-eggplant-scripts` prompt, replacing the test case ID with one test case.
5. Review the generated `.script` file manually in Eggplant Studio before execution.
6. Execute only in an approved test environment and only after confirming the script's actions are safe.

## 5. If the Excel file cannot be read
Export only the relevant worksheet to CSV and update the prompt path, for example `input/test-cases.csv`. Do not assume Copilot has parsed an attached workbook if it has not shown the extracted rows.

## 6. Troubleshooting
- **MCP server is missing:** inspect VS Code's MCP server list and logs; check that the server is enabled and connected.
- **Tools appear but cannot inspect the SUT:** verify the active SUT/session and server permissions.
- **Copilot invents SenseTalk syntax:** direct it to an actual reference script/helper and ask it to compare the proposed syntax with that file.
- **Wrong output location:** restate the exact workspace-relative output path and check the created file.
- **Existing filename:** the skill requires a new unique filename and prohibits silent overwrite.
- **No test execution tool:** generation can still create the `.script` file, but execution status must be reported as "not run".
