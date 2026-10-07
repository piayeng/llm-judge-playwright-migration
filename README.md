# Playwright Migration LLM Judge

This repository benchmarks an LLM-based workflow for converting legacy TestComplete and UFT automation scripts into modern Playwright tests.
[Promptfoo](https://www.promptfoo.dev/) supplies the legacy scripts to a migration prompt, calls the configured model, and evaluates each generated response against scenario-specific checks and a QA rubric.

The current configuration uses OpenAI GPT-4o. 
Running an evaluation sends prompts and test inputs to the configured model provider and may incur API charges.

## How the evaluation works

1. Each test case supplies a legacy script and its migration requirements.
2. 'prompts/migration_prompt.txt' asks the model to produce a Playwright TypeScript test using resilient locators, web-first assertions, proper async handling, and other modern patterns.
3. 'prompts/judge_rubric.txt' defines the judge's scoring scale and JSON response format.
4. The assertions in 'tests/test_cases.json' assess each response. These include LLM rubric checks and, where appropriate, explicit output checks.

The judge maps ratings to scores from '0.2' to '1.0'; a score of '0.8' or higher is considered a pass. 
Evaluation checks the generated response:it does **not** execute the generated Playwright code against a website.

## Scenario coverage

The 25 cases cover locator strategies, frames and shadow DOM, authentication and data-driven tests, form controls, dialogs and popups, file uploads and downloads, hover and drag-and-drop interactions, date pickers and keyboard input, assertions and optional elements, API and visual checks, database-related hallucinations, and asynchronous page behavior such as infinite scroll and loading indicators.

## Repository layout

| Path | Purpose |
| 'promptfooconfig.yaml' | Promptfoo configuration, model provider, prompt, and test-case references |
| 'prompts/migration_prompt.txt' | Instructions for converting a legacy script into a Playwright test |
| 'prompts/judge_rubric.txt' | General grading rubric and required judge response format |
| 'tests/test_cases.json' | Legacy-script inputs, scenario metadata, and evaluation assertions |
| 'eval-results.json' | A saved evaluation-results export |
| '.github/workflows/promptfoo' | Evaluation workflow definition |

The checked-in 'eval-results.json' is a saved export and is not automatically refreshed by each evaluation.

## Requirements

- Node.js 20 or newer and npm
- An API key for the configured provider
-  the current configuration uses OpenAI and requires 'OPENAI_API_KEY'

Install Promptfoo globally:

npm install --global promptfoo
promptfoo --version

## Run an evaluation

Set 'OPENAI_API_KEY' in your shell. 
In PowerShell:
```powershell
$env:OPENAI_API_KEY = "your-openai-api-key"

In Bash or a similar shell:
  export OPENAI_API_KEY="your-openai-api-key"
  
Then run the evaluation from the repository root:
  promptfoo eval
  
To inspect the latest evaluation in Promptfoo's local results viewer:
  promptfoo view

Keep API keys out of source control. The repository's '.gitignore' excludes environment files such as '.env'; provide credentials through environment variables or your CI secret store instead.

## Customize the benchmark

- Edit 'prompts/migration_prompt.txt' to change the requested migration behavior.
- Edit 'prompts/judge_rubric.txt' to change the general grading criteria or output format.
- Add or update cases and their assertions in 'tests/test_cases.json'.
- Change the model or provider settings in 'promptfooconfig.yaml'.


