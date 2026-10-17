# AI agent unit tests (Hexlet, 2026)

[English](README.md) | [Русский](README.ru.md)

A Hexlet learning demo for generating Python unit tests with an AI agent in GitHub Actions. A developer opens a pull request, the agent checks coverage and adds tests to the same branch, and a maintainer reviews the result.

The project is small enough to read the source, tests and agent changes together.

## How the demo works

```text
Feature branch -> Pull request to main -> GitHub Actions -> AI agent
                                                       -> Test commit on the PR branch
                                                       -> Maintainer review
```

1. Implement a feature on a `feature/*` branch.
2. Open a pull request against `main`.
3. The `AI Test Agent` workflow runs when the PR is opened or updated.
4. The agent reads the diff, checks test coverage and writes tests for uncovered functions.
5. It runs the tests, then commits and pushes test changes to the PR branch.
6. A maintainer reviews the code and generated tests before accepting or rejecting the PR.

## Project structure

| Path | Content |
| --- | --- |
| [.github/workflows/ai-test-agent.yml](.github/workflows/ai-test-agent.yml) | Agent workflow and prompt |
| [features/add-data-processor.md](features/add-data-processor.md) | Data-processing exercise and acceptance criteria |
| [features/add-geometry.md](features/add-geometry.md) | Geometry exercise and acceptance criteria |
| [src](src) | Python modules used in the demo |
| [tests](tests) | Unit tests for the modules |
| [pyproject.toml](pyproject.toml) | Python requirements, development dependencies and pytest settings |

## Modules and test exercises

The original exercise asks the agent to find missing tests for the functions below. The repository now includes test files for all four modules. Use a coverage report to choose the next case to add; a test file alone does not show whether every path is covered.

| Module | Functions | Test file |
| --- | --- | --- |
| [calculator.py](src/calculator.py) | `add`, `subtract`, `multiply`, `divide`, `factorial`, `is_prime`, `gcd` | [test_calculator.py](tests/test_calculator.py) |
| [string_utils.py](src/string_utils.py) | `reverse`, `capitalize_words`, `is_palindrome`, `count_vowels` | [test_string_utils.py](tests/test_string_utils.py) |
| [data_processor.py](src/data_processor.py) | `filter_by_key`, `sort_by_key`, `aggregate_by_key`, `merge_records` | [test_data_processor.py](tests/test_data_processor.py) |
| [geometry.py](src/geometry.py) | `circle_area`, `circle_circumference`, `rectangle_area`, `rectangle_perimeter`, `triangle_area` | [test_geometry.py](tests/test_geometry.py) |

The data-processing exercise covers lists of dictionaries, filtering, sorting, counting values and merging consecutive records. Its acceptance criteria include empty inputs and edge cases such as missing keys and `None`. The geometry exercise covers circles, rectangles and triangles, with `ValueError` for negative dimensions, zero-sized shapes and float results. The detailed assignments remain in `features/`.

## Run locally

Use Python 3.10 or newer. CI uses Python 3.12.

```bash
git clone https://github.com/ivan-zamula/ai-agent-unit-tests-2026-hexlet.git
cd ai-agent-unit-tests-2026-hexlet
python -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"
```

Run the tests and inspect coverage:

```bash
pytest tests/ -v
pytest tests/ --cov=src --cov-report=term-missing
```

## GitHub Actions and the agent

The workflow uses the GitHub environment `demo`, Python 3.12 and Node.js/npm. Its job requests `contents: write` and `pull-requests: write` so the agent can commit test changes back to the PR branch. The selected agent is OpenCode:

```bash
npm install -g opencode-ai
opencode run --model opencode/big-pickle "..."
```

The full prompt is in [ai-test-agent.yml](.github/workflows/ai-test-agent.yml). It tells the agent to:

1. Compare the PR with `main`.
2. Run pytest with coverage to find uncovered functions.
3. Read source files and existing tests to follow the project style.
4. Add normal, edge and exception cases.
5. Run the tests and fix any generated tests that fail.
6. Commit changes as `ai-test-agent[bot]` and push them to the PR branch.

The workflow also writes a GitHub Actions summary with links to the PR and workflow run.

## Adapt the demo

- To use another provider or model, update the OpenCode command and its environment. Store required keys such as `ANTHROPIC_API_KEY`, `OPENAI_API_KEY` or `GEMINI_API_KEY` in GitHub Secrets and pass them to the agent step. Adding a secret does not configure the step by itself.
- Add project conventions to the prompt, such as using `unittest` or mocking external APIs.
- To use another agent, update both its installation step and invocation.
- A Docker image with the agent and dependencies already installed can reduce setup work for each run.

The aim is to study how AI-generated tests fit into CI/CD while keeping code review with the maintainer.
