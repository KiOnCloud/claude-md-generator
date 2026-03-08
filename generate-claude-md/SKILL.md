---
name: generate-claude-md
description: Analyze the current project and generate an optimal CLAUDE.md file based on project rules and tech stack best practices.
disable-model-invocation: true
argument-hint: "[output-path (default: ./CLAUDE.md)]"
allowed-tools: Read, Glob, Grep, Bash, Write, Edit, Agent
---

# Generate CLAUDE.md

You are an expert at analyzing codebases and creating optimal CLAUDE.md configuration files for Claude Code.

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

**Project structure:**
- Glob for top-level directories to understand project layout
- Check for monorepo patterns (`apps/`, `packages/`, `modules/`, `services/`)
- Check for common directories (`src/`, `lib/`, `test/`, `tests/`, `__tests__/`, `spec/`)
- Check for mobile patterns (`android/`, `ios/`, `macos/`, `windows/`, `linux/`)
- Check for .NET patterns (`Controllers/`, `Services/`, `Models/`, `Data/`, `Migrations/`)
- Check for C/C++ patterns (`include/`, `src/`, `build/`, `cmake/`)

### Step 2: Deep Analysis

Based on what you found, read the relevant files to extract:

1. **Build/Test/Lint commands** — from `scripts` in package.json, Makefile targets, Taskfile tasks, etc.
2. **Code conventions** — from linter configs, tsconfig strictness, formatter settings
3. **Architecture patterns** — monorepo structure, module boundaries, key entry points
4. **Dependencies** — major frameworks, ORMs, test runners, bundlers
5. **Environment setup** — required env vars (from .env.example), Docker dependencies, DB setup
6. **CI/CD pipeline** — what the CI does (helps understand expected workflow)

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
- [Import ordering/style]
- [Error handling patterns]
- [State management approach if frontend]

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

## Writing Rules

1. **Be concise** — bullet points, not paragraphs. Target 40-80 lines total.
2. **Be specific** — write exact commands, exact file paths, exact patterns.
3. **No obvious info** — don't state what Claude can infer from reading code (e.g., "uses React" when package.json has react).
4. **No secrets** — never include actual env values, API keys, tokens.
5. **Project rules first** — if tsconfig says `strict: false`, write that. Don't override with "should use strict".
6. **Actionable only** — every line should help Claude write better code or avoid mistakes.
7. **Use backticks for commands and paths** — always wrap commands and file paths in backticks.

## Final Check

Before writing the file, verify:
- [ ] All commands are real (extracted from actual config, not guessed)
- [ ] Conventions match what configs actually enforce
- [ ] No redundant information that Claude would already know
- [ ] No sections are empty or contain only generic advice
- [ ] File is under 100 lines
