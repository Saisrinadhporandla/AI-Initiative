# Recommended Excel Test Case Format

The skill can adapt to an existing workbook. These columns are recommended when you control the template:

| TestCaseID | TestCaseName | StepNo | Action | TestData | ExpectedResult |
|---|---|---:|---|---|---|
| TC_001 | Valid login | 1 | Open the login page | URL or environment | Login page is displayed |
| TC_001 | Valid login | 2 | Enter the username | test user | Username is accepted |
| TC_001 | Valid login | 3 | Enter the password | Test secret reference, not a real secret | Password is entered |
| TC_001 | Valid login | 4 | Select Sign in | — | Home page is displayed |

## Guidance
- Repeat the test case ID/name on each row belonging to that case, or document how blank continuation cells should be interpreted.
- Keep one action per row where possible.
- Write observable expected results, not vague outcomes such as "works fine".
- Do not store production passwords, tokens, or other secrets in the workbook. Use approved test credentials or a secure secret reference.
- Include preconditions and postconditions in separate columns if your team needs them.
- If the existing workbook uses different column names, Copilot should map those columns rather than requiring you to rebuild it.
- If Copilot cannot read the `.xlsx` workbook in the current VS Code setup, export the required worksheet as UTF-8 CSV and point the prompt to that CSV.
