# Eggplant Copilot Script Generation Skill Kit

This kit configures GitHub Copilot to generate new Eggplant SenseTalk `.script` files from:
1. Excel test steps
2. An existing reference Eggplant suite
3. The live System Under Test (SUT), accessed through configured Eggplant MCP servers

## Included files

- `.github/copilot-instructions.md` — workspace-wide rules
- `.github/skills/eggplant-script-generation/SKILL.md` — reusable generation skill
- `.github/prompts/generate-eggplant-scripts.prompt.md` — reusable prompt for a generation run
- `docs/EXCEL_TEST_CASE_FORMAT.md` — recommended Excel columns and handling
- `docs/SETUP.md` — setup and verification guide

## Suggested workspace layout

```text
your-workspace/
├── .github/
│   ├── copilot-instructions.md
│   ├── skills/
│   │   └── eggplant-script-generation/
│   │       └── SKILL.md
│   └── prompts/
│       └── generate-eggplant-scripts.prompt.md
├── input/
│   └── test-cases.xlsx
├── reference-suite/
│   └── ... existing Eggplant suite files ...
├── output/
│   └── generated_scripts/
└── docs/
    └── EXCEL_TEST_CASE_FORMAT.md
```

Copy these kit files into the root of your actual VS Code workspace. Put the real Excel file and reference suite in the corresponding folders, or update the paths in the prompt.

## Important notes

- Keep your reference suite read-only during generation. If it lives elsewhere, point Copilot to that directory and tell it not to edit it.
- MCP tool names and capabilities depend on your actual server configuration. Copilot must inspect the tools exposed by the configured servers rather than assuming tool names or capabilities.
- The skill mentions `prerequisites_sensetalk_generation`, `sensetalk_snippets_search`, and `capture_region` as tools previously identified in this project. Use them only if they are actually exposed by your MCP server.
- Do not assume Gateway MCP can execute arbitrary SenseTalk scripts or that Studio MCP can run a test. Use only tools actually available.
- If Copilot cannot read `.xlsx` in your setup, export the relevant worksheet to `.csv` and use that as the input. Preserve step order.
- Generated files use the `.script` extension. Verify that the generated syntax and file encoding match the conventions of your Eggplant version and suite.
