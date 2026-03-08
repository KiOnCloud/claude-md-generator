# generate-claude-md

A Claude Code skill that analyzes any project and generates an optimal `CLAUDE.md` file — tailored to the project's actual rules, tech stack, and best practices.

## What it does

When invoked, the skill will:

1. **Scan** your project structure, config files, and manifests
2. **Analyze** build commands, linter rules, conventions, architecture
3. **Detect** tech stack and apply up-to-date best practices (March 2026)
4. **Generate** a concise, actionable `CLAUDE.md` that helps Claude Code work effectively in your project

**Key principle:** Project rules always take priority over generic best practices.

## Supported tech stacks

| Category       | Stacks                                                                              |
| -------------- | ----------------------------------------------------------------------------------- |
| Frontend       | React 19+, Next.js 16+, Vue 3.5+, Nuxt 4+, Angular 20, Astro 5, Svelte 5, SvelteKit |
| Backend JS/TS  | NestJS 11, Bun, Node.js                                                             |
| Backend Python | Django 5.2, FastAPI, Flask, Celery                                                  |
| Backend .NET   | ASP.NET Core 10, Blazor, Entity Framework Core                                      |
| Backend Java   | Spring Boot 4, Micronaut, Quarkus                                                   |
| Backend Ruby   | Rails 8.1, Sinatra                                                                  |
| Backend PHP    | Laravel 12, Symfony                                                                 |
| Backend Elixir | Phoenix 1.8, LiveView 1.1                                                           |
| Backend Go     | Go 1.26, Gin, Echo, Fiber, Chi                                                      |
| Systems        | Rust (Edition 2024), C (CMake), C++ (C++23/26)                                      |
| Mobile         | Swift 6 / SwiftUI, Kotlin 2.3 / Jetpack Compose, Flutter 3.41 / Dart 3.11           |
| Database/ORM   | Prisma, Drizzle, TypeORM, SQLAlchemy, Mongoose, EF Core                             |
| Monorepo       | Turborepo, Nx, pnpm Workspaces                                                      |
| DevOps         | Docker, Terraform, Kubernetes                                                       |

## Installation

### Option 1: Global (all projects)

```bash
# Clone the repo
git clone https://github.com/KiOnCloud/claude-md-generator.git

# Copy skill to Claude Code global skills directory
cp -r generate-claude-md ~/.claude/skills/generate-claude-md
```

### Option 2: Per-project

```bash
# From your project root
mkdir -p .claude/skills
cp -r /path/to/generate-claude-md .claude/skills/generate-claude-md
```

## Usage

Open Claude Code in any project and run:

```
/generate-claude-md
```

To specify a custom output path:

```
/generate-claude-md ./docs/CLAUDE.md
```

## Updating

The skill is maintained and updated periodically with the latest framework versions and best practices.

### Via Git

```bash
# Navigate to your skills directory
cd ~/.claude/skills/generate-claude-md

# Pull latest changes
git pull origin main
```

### Manual update

Download the latest version from the repository and replace the files in your skills directory:

```bash
# Remove old version
rm -rf ~/.claude/skills/generate-claude-md

# Clone fresh
git clone https://github.com/KiOnCloud/claude-md-generator.git ~/.claude/skills/generate-claude-md
```

### Keeping in sync across machines

If you use this skill on multiple machines, a simple setup script can help:

```bash
#!/bin/bash
# sync-skill.sh — Run on each machine to install or update

SKILL_DIR="$HOME/.claude/skills/generate-claude-md"
REPO="https://github.com/KiOnCloud/claude-md-generator.git"

if [ -d "$SKILL_DIR/.git" ]; then
  echo "Updating skill..."
  git -C "$SKILL_DIR" pull origin main
else
  echo "Installing skill..."
  mkdir -p "$(dirname "$SKILL_DIR")"
  git clone "$REPO" "$SKILL_DIR"
fi

echo "Done."
```

## File structure

```
generate-claude-md/
├── SKILL.md             # Main skill prompt — analysis and generation logic
├── tech-practices.md    # Best practices reference for 30+ tech stacks
└── README.md            # This file
```

| File                | Purpose                                                                            |
| ------------------- | ---------------------------------------------------------------------------------- |
| `SKILL.md`          | Instructs Claude how to scan, analyze, and generate CLAUDE.md                      |
| `tech-practices.md` | Reference database of best practices per tech stack, used as supplementary context |

## Contributing

To add or update best practices for a tech stack:

1. Edit `tech-practices.md` — follow the existing format
2. If the tech requires new config files to scan, update the discovery section in `SKILL.md`
3. Test by running `/generate-claude-md` on a project using that stack

## License

MIT
