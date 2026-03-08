---
name: generate-claude-md
description: Analyze the current project and generate an optimal CLAUDE.md file based on project rules and tech stack best practices.
disable-model-invocation: true
argument-hint: "[output-path (default: ./CLAUDE.md)]"
allowed-tools: Read, Glob, Grep, Bash, Write, Edit, Agent
---

# Generate CLAUDE.md

You are an expert at analyzing codebases and creating optimal CLAUDE.md configuration files for Claude Code.

**Remember: CLAUDE.md is a configuration file that becomes part of Claude's system prompt — not documentation. Claude treats system-level instructions with greater strictness than user prompts.**

## Output path

- If `$ARGUMENTS` is provided, write CLAUDE.md to that path
- Otherwise, write to `./CLAUDE.md` in the project root

## Process

Follow these steps strictly in order:

### Step 1: Project Discovery

Scan the project to identify its nature. Run these searches **in parallel**:

**Package/manifest files:**
- JS/TS: `package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `bun.lockb`, `deno.json`, `deno.jsonc`
- Rust: `Cargo.toml`
- Go: `go.mod`, `go.sum`
- Python: `pyproject.toml`, `setup.py`, `setup.cfg`, `requirements.txt`, `Pipfile`, `poetry.lock`, `uv.lock`
- PHP: `composer.json`
- Ruby: `Gemfile`
- Java/Kotlin: `build.gradle`, `build.gradle.kts`, `pom.xml`, `settings.gradle.kts`
- .NET/C#: `*.csproj`, `*.sln`, `Directory.Build.props`, `global.json`, `nuget.config`
- Dart/Flutter: `pubspec.yaml`
- Elixir: `mix.exs`
- Swift: `Package.swift`, `*.xcodeproj`, `*.xcworkspace`, `Podfile`
- C/C++: `CMakeLists.txt`, `Makefile`, `meson.build`, `conanfile.txt`, `vcpkg.json`

**Config files:**
- TS/JS: `tsconfig.json`, `jsconfig.json`, `angular.json`, `astro.config.*`, `svelte.config.*`, `nest-cli.json`
- Linter/Formatter: `.eslintrc*`, `eslint.config.*`, `.prettierrc*`, `prettier.config.*`, `biome.json`, `biome.jsonc`, `.rubocop.yml`, `.swiftlint.yml`, `.clang-format`, `.clang-tidy`
- Editor: `.editorconfig`
- Build: `Makefile`, `Justfile`, `Taskfile.yml`, `CMakeLists.txt`
- Container: `docker-compose.yml`, `Dockerfile`
- CI/CD: `.github/workflows/*.yml`, `.gitlab-ci.yml`, `.circleci/config.yml`
- Monorepo: `turbo.json`, `nx.json`, `lerna.json`
- Env: `.env.example`, `.env.sample`
- .NET: `appsettings.json`, `launchSettings.json`
- Android: `AndroidManifest.xml`, `build.gradle.kts` (app-level)
- iOS: `Info.plist`, `*.xcconfig`
- Infra: `terraform/*.tf`, `kubernetes/*.yml`, `helm/Chart.yaml`
- Hooks: `.husky/`, `.pre-commit-config.yaml`, `.claude/hooks`

**Project structure:**
- Glob for top-level directories to understand project layout
- Check for monorepo patterns (`apps/`, `packages/`, `modules/`, `services/`)
- Check for common directories (`src/`, `lib/`, `test/`, `tests/`, `__tests__/`, `spec/`)
- Check for mobile patterns (`android/`, `ios/`, `macos/`, `windows/`, `linux/`)
- Check for .NET patterns (`Controllers/`, `Services/`, `Models/`, `Data/`, `Migrations/`)
- Check for C/C++ patterns (`include/`, `src/`, `build/`, `cmake/`)
- Check for docs/agent patterns (`agent_docs/`, `docs/`, `.claude/`)
- Estimate project size: small (<50 files), medium (50-500), large (500+)

### Step 2: Deep Analysis

Based on what you found, read the relevant files to extract:

1. **Build/Test/Lint commands** — from `scripts` in package.json, Makefile targets, Taskfile tasks, etc.
2. **Code conventions** — from linter configs, tsconfig strictness, formatter settings
3. **Architecture patterns** — monorepo structure, module boundaries, key entry points
4. **Dependencies** — major frameworks, ORMs, test runners, bundlers
5. **Environment setup** — required env vars (from .env.example), Docker dependencies, DB setup
6. **CI/CD pipeline** — what the CI does (helps understand expected workflow)
7. **Pre-commit hooks** — what linters/formatters run automatically (so we don't duplicate in CLAUDE.md)
8. **Existing CLAUDE.md or .claude/ config** — check if there's already a CLAUDE.md to update rather than overwrite

### Step 3: Identify Tech Stack Best Practices

Based on the detected tech stack, include relevant best practices that the project configs don't already cover. Reference the knowledge in [tech-practices.md](tech-practices.md) for common tech stacks.

**Critical rule:** If a project convention contradicts a general best practice, ALWAYS follow the project convention. Note the deviation only if it could cause confusion.

### Step 4: Generate CLAUDE.md

Write the CLAUDE.md file following this structure. Only include sections that have meaningful content — skip empty sections.

```markdown
# [Project Name]

[One-line description if discoverable from README or package.json]

## Commands
- Build: `<command>`
- Dev: `<command>`
- Test: `<command>`
- Test single file: `<command>`
- Lint: `<command>`
- Lint fix: `<command>`
- Format: `<command>`
- Type check: `<command>`
[Add other relevant commands]

## Architecture
- [Brief layout: monorepo? key directories? entry points?]
- [Module/package boundaries if applicable]
- [Key patterns: MVC, hexagonal, feature-based, etc.]

## Code Conventions
- [Language version/strictness settings]
- [Naming conventions: files, variables, components]
- [Import ordering/style if not enforced by linter]
- [Error handling patterns]

## Tech Stack Notes
- [Framework-specific gotchas]
- [ORM/DB usage patterns]
- [API patterns (REST, GraphQL, tRPC)]
- [Auth approach]

## Testing
- [Test framework and patterns]
- [Where tests live]
- [How to run specific test subsets]
- [Test data/fixtures approach]

## Environment Setup
- [Required env vars]
- [External dependencies (Docker, DB, etc.)]
- [Setup steps if non-obvious]

## Pitfalls
- [Known gotchas specific to this project]
- [Commands that must be run in sequence]
- [Files that must stay in sync]
```

## Writing Rules — The Meta-Rules

These rules are **non-negotiable**. They determine whether the CLAUDE.md will actually be effective.

### Rule 1: Instruction Budget (~100 usable instructions)

Claude Code's system prompt already uses ~50 instructions. CLAUDE.md shares the same context, leaving roughly **100-150 instructions** before quality degrades — and it degrades **uniformly across all instructions**, not just the new ones.

- Target **40-80 lines** total
- Absolute maximum: **100 lines**
- Every line must earn its place

### Rule 2: The 80% Rule

**If an instruction isn't relevant to 80%+ of Claude Code sessions in this project, it doesn't belong in CLAUDE.md.**

- Move task-specific instructions to separate files (e.g., `agent_docs/deployment.md`)
- Move directory-specific rules to nested `CLAUDE.md` files in those directories
- Create custom slash commands for specialized workflows

### Rule 3: Don't Duplicate Linters

If the project has linter/formatter configs (ESLint, Prettier, Ruff, Black, rustfmt, clang-format, etc.) or pre-commit hooks:

- **DO NOT** write style rules into CLAUDE.md that linters already enforce
- **DO** write: "Code must pass linting before commit. Pre-commit hooks run automatically."
- **DO** mention the linter tools so Claude knows they exist
- The linter config IS the source of truth for style — not CLAUDE.md

### Rule 4: Progressive Disclosure for Large Projects

For medium-to-large projects (50+ files), keep CLAUDE.md lean by referencing external docs:

```markdown
## Detailed Docs
- API guidelines: see `agent_docs/api-guidelines.md`
- Database patterns: see `agent_docs/database.md`
- Deployment process: see `agent_docs/deploy.md`
```

Claude loads these only when relevant, keeping main context efficient.

### Rule 5: Standard Writing Rules

1. **Be concise** — bullet points, not paragraphs
2. **Be specific** — exact commands, exact paths, exact patterns
3. **No obvious info** — don't state what Claude can infer from reading code
4. **No secrets** — never include actual env values, API keys, tokens
5. **Project rules first** — if tsconfig says `strict: false`, write that. Don't override.
6. **Actionable only** — every line should help Claude write better code or avoid mistakes
7. **Use backticks** — always wrap commands and file paths in backticks

## Step 5: Post-Generation Advice

After writing the CLAUDE.md, output a brief "Next Steps" section to the user (NOT in the file) with relevant suggestions:

**Always suggest:**
- Review the generated file and remove anything not relevant to 80%+ of sessions

**Suggest if applicable:**
- Create `CLAUDE.local.md` for personal preferences (add to `.gitignore`)
- Create nested `<dir>/CLAUDE.md` for directory-specific rules (if monorepo or large project)
- Create `agent_docs/` folder for detailed guidelines referenced from CLAUDE.md (if large project)
- Set up pre-commit hooks if linters exist but no hooks are configured
- Use `# <instruction>` shortcut in Claude Code to add rules on-the-fly as you discover them

## Final Check

Before writing the file, verify:
- [ ] All commands are real (extracted from actual config, not guessed)
- [ ] Conventions match what configs actually enforce
- [ ] No redundant information that Claude would already know
- [ ] No style rules that linters/formatters already handle
- [ ] No sections are empty or contain only generic advice
- [ ] Total instructions stay within budget (~100 lines max)
- [ ] Every instruction passes the 80% relevance test
- [ ] File is configuration, not documentation — no explanatory prose
