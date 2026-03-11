# Tech Stack Best Practices Reference

Use this as a reference when generating CLAUDE.md. Only include practices that are **relevant to the detected stack** and **not already covered by project configs**.

> Last updated: March 2026

---

## JavaScript / TypeScript

### General
- Prefer `const` over `let`, avoid `var`
- Use optional chaining (`?.`) and nullish coalescing (`??`) over manual checks

### Node.js
- Must run `npm install` / `pnpm install` after pulling if lockfile changed
- Use `node --run <script>` (Node 22+) as faster alternative to `npm run`

### Bun
- All-in-one runtime: `bun install`, `bun run`, `bun test`, `bun build` — no separate tools needed
- `bun run <file>` to execute TS/JS directly without compilation step
- `bunx` replaces `npx` — runs packages without installing
- Built-in SQLite support via `bun:sqlite`
- Can run HTML files directly with auto-bundling and HMR for frontend dev
- `bun install` is significantly faster than npm/yarn/pnpm — uses binary lockfile (`bun.lockb`)
- Compatible with most Node.js APIs — check `bun.com` for compatibility gaps

### TypeScript
- If `strict: true`, never use `any` — prefer `unknown` with type narrowing
- Run type check separately from build: `tsc --noEmit`
- After changing types in shared packages (monorepo), rebuild dependent packages

---

## React

### React 19+ (Current Stable)
- **Server Components by default** — add `'use client'` only when interactivity is needed
- `use()` hook to integrate promises directly into render flow with Suspense
- `useActionState` for tracking async action state (pending, fulfilled, rejected)
- `useOptimistic` for optimistic UI updates
- `useFormStatus` for form state without prop drilling
- `ref` is now a regular prop — `forwardRef` is deprecated, remove it from new code
- **React Compiler** (stable since Oct 2025) auto-optimizes — `useMemo`/`useCallback` are often unnecessary
- `<form action={fn}>` supports async functions for form handling
- Directives: `'use client'` for client components, `'use server'` for Server Actions
- Use Suspense + streaming for progressive hydration
- `useTransition` for non-urgent updates to keep UI responsive

### React 19.2+
- `<Activity>` component for managing background UI — keeps state alive when hidden
- `useEffectEvent` for stable event handlers in effects

---

## Next.js

### Next.js 16+ (Current Stable)
- **Turbopack is default bundler** for both dev and production — no config needed
- **Cache Components** with `use cache` directive + Partial Pre-Rendering (PPR)
- Async Request APIs are fully enforced — synchronous access removed since v16
- `next dev` uses Turbopack by default (no `--turbo` flag needed)
- `next build` now uses Turbopack for production builds

### Next.js 16.1
- Turbopack file system caching for faster dev restarts
- Built-in bundle analyzer
- Better Node.js debugging support

### App Router Patterns
- Default to Server Components; add `'use client'` only when needed
- Use `loading.tsx` and `error.tsx` for route-level states
- Data fetching in Server Components, mutations via Server Actions
- After adding/renaming routes, restart dev server if changes aren't picked up

### Pages Router (Legacy)
- Use `getServerSideProps` for dynamic data, `getStaticProps` for static
- API routes in `pages/api/` — keep thin, delegate to services

---

## Vue

### Vue 3.5 (Current Stable)
- Reactive Props Destructure is stable — destructured `defineProps` vars are reactive
- `onWatcherCleanup()` for cleanup callbacks in watchers
- Lazy Hydration for async components via `hydrate` option in `defineAsyncComponent()`
- Prefer `<script setup>` over Options API
- Use `defineProps` and `defineEmits` for component contracts
- Composables go in `composables/` directory

### Vue 3.6 (Beta)
- **Vapor Mode** — new compilation strategy matching Solid/Svelte 5 performance
- Major reactivity refactor based on `alien-signals` — better perf and memory

---

## Nuxt

### Nuxt 4+ (Current Stable)
- New `app/` directory structure — migrate from root-level organization
- Improved TypeScript support with project-based context separation
- `#server` alias for clean server-directory imports with import protection
- `useFetch` / `useAsyncData` for SSR-safe data fetching
- Server routes in `server/api/` — file-based routing
- Auto-imports: components, composables, and utils

### Nuxt 4.3 (Latest)
- `inlineStyles` works with webpack and rspack for critical CSS inlining
- Route rule layouts and ISR payload extraction
- Opt-in to v5 features via settings

---

## Angular

### Angular 20 (Current Stable)
- **Signals** are the primary reactive primitive — prefer over RxJS for component state
- Standalone components are the default — NgModules only for legacy
- `inject()` function preferred over constructor injection
- Type checking for host bindings (validated in Angular 20)
- Template literals, exponentiation operator, `in` keyword, `void` operator in templates
- Nullish coalescing + logical operators require explicit parentheses

### Angular 19+ Features
- `linkedSignal` and `resource` API for async reactivity
- `httpResource` and `rxResource` for data fetching
- Incremental Hydration with `@defer` syntax — load/hydrate on trigger
- Use `OnPush` change detection for performance-critical components
- `ng generate` for scaffolding, `ng test` for unit tests, `ng e2e` for E2E

---

## Astro

### Astro 5.x (Current Stable, v5.18)
- Static by default — use `client:*` directives only when interactivity is needed
- `client:load` (immediate), `client:idle` (after idle), `client:visible` (when visible)
- **Content Layer** — load content from any source with type-safe schemas
- **Server Islands** — combine cached static content with dynamic personalized content
- `astro:env` module for type-safe environment variable definitions
- Content Collections in `src/content/` with Zod validation
- `astro dev`, `astro build`, `astro preview` for dev/build/preview
- SSR requires adapter (`@astrojs/node`, `@astrojs/vercel`, etc.)
- Markdown/MDX pages in `src/pages/` get file-based routing

### Astro 6.0 (Beta)
- Check upgrade guide if migrating

---

## Svelte / SvelteKit

### Svelte 5 (Current Stable)
- **Runes system**: `$state`, `$derived`, `$effect` — replace legacy reactive declarations
- `$props()` to declare component props (replaces `export let`)
- `$state.eager(value)` for immediate UI updates
- Snippet blocks `{#snippet}` replace slots
- Async components — `await` directly in component scripts
- **Remote Functions** — type-safe server communication without GraphQL/tRPC
- `createContext` for typed context passing
- 15-30% smaller bundles with runes vs legacy

### SvelteKit 2.43+
- `+page.svelte` for UI, `+page.server.ts` for server-side load/actions
- Form actions for mutations, `load` functions for data fetching
- OpenTelemetry traces via `instrumentation.server.ts`
- File upload streaming in remote functions
- Experimental async SSR via `experimental.async` option
- Native WebSocket support (experimental)
- `svelte-kit sync` after changing route structure

---

## NestJS

### NestJS 11 (Current Stable)
- `nest generate resource <name>` creates full CRUD module
- Module-based architecture: each feature gets its own module
- **JSON logging** built into default ConsoleLogger — easier log aggregation
- `ParseDatePipe` for date handling, `IntrinsicException` for non-logged exceptions
- Microservice transporters improved — `unwrap()` method for direct client access
- Microservice options can be provided from DI container
- `ConfigService#get` reads config factories before `process.env` (changed order)
- `skipProcessEnv` option for configuration encapsulation
- `@nestjs/cqrs` supports request-scoped providers and strongly-typed commands/events
- Use DTOs with `class-validator` for request validation
- Guards for auth, Interceptors for response transformation, Pipes for validation
- `npm run start:dev` for watch mode, `npm run start:debug` for debug

---

## Python

### General (3.13-3.14)
- Use type hints for function signatures
- Prefer f-strings over `.format()` or `%`
- Use `pathlib.Path` over `os.path`
- **Free-threaded mode** (no GIL) available — enable with `PYTHON_GIL=0` for true parallelism
- JIT compilation (experimental) — groundwork for significant perf improvements
- Template string literals (Python 3.14)
- Deferred evaluation of annotations (Python 3.14)
- Use `uv` as modern package/venv manager — much faster than pip
- Use `Ruff` for linting/formatting — replaces flake8, isort, black in one tool

### Django 5.2 (Current Stable)
- Always run `python manage.py makemigrations` after model changes
- Run `python manage.py migrate` to apply migrations
- Keep views thin, business logic in services/models
- Use `select_related()` / `prefetch_related()` to avoid N+1 queries
- Async views supported but ORM async is still limited — use `sync_to_async` wrapper
- Settings: split into `base.py`, `local.py`, `production.py`

### Django REST Framework (DRF)
- Use `ModelSerializer` for standard CRUD, custom `Serializer` for complex logic
- ViewSets + Routers for RESTful endpoints, APIView for custom endpoints
- Use `permission_classes` and `authentication_classes` on views
- Filtering: use `django-filter` with `FilterSet` classes

### FastAPI
- Use Pydantic models for request/response validation
- Dependency injection via `Depends()`
- Async endpoints for I/O-bound operations
- Use `APIRouter` for route organization
- Server-Sent Events (SSE) support for streaming
- `uvicorn main:app --reload` for development

### Flask
- Use Blueprints for modular route organization
- Application factory pattern for configuration

### Celery
- Define tasks with `@shared_task` decorator
- `celery -A <app> worker` to start, `celery -A <app> beat` for scheduled tasks
- Set `task_always_eager = True` in test settings

### Poetry / pip / uv
- `uv sync` to install deps from `pyproject.toml` (preferred, fastest)
- `uv pip install` / `uv venv` as faster alternative to pip/venv
- `poetry install` or `pip install -e .` for dev setup
- `poetry lock` after changing `pyproject.toml`

---

## .NET / C#

### .NET 10 (Current LTS, Nov 2025)
- `dotnet build` to compile, `dotnet test` to test, `dotnet run` to execute
- `dotnet restore` after changing `.csproj` dependencies
- Use `nullable` context — handle nulls explicitly
- Prefer records for immutable data types
- Use `async`/`await` for all I/O — never `.Result` or `.Wait()`

### ASP.NET Core 10
- **Minimal API validation** built-in — no third-party lib needed for basic validation
- **OpenAPI 3.1** document generation support
- **Passkey/WebAuthn** authentication built into ASP.NET Core Identity
- **Server-Sent Events** via `TypedResults.ServerSentEvents` in both Minimal APIs and controllers
- Empty form strings auto-map to `null` for nullable types with `[FromForm]`
- Use minimal APIs for simple endpoints, Controllers for complex ones
- `builder.Services.Add*()` for DI registration
- Use `IOptions<T>` pattern for configuration binding

### Spring Boot 4 / .NET Migration Note
- If on .NET 9, consider upgrading to .NET 10 LTS for long-term support (until Nov 2028)

### Entity Framework Core
- `dotnet ef migrations add <name>` after model changes
- `dotnet ef database update` to apply
- Use `AsNoTracking()` for read-only queries
- Use `Include()` / `ThenInclude()` to avoid N+1

### Blazor
- Blazor scripts now treated as static web assets with compression/fingerprinting
- Async JS interop on `IJSRuntime` with `CancellationToken` support
- Blazor Server: real-time via SignalR
- Blazor WASM: runs in browser, use `HttpClient` for API calls

### Testing (.NET)
- xUnit: `[Fact]` for single tests, `[Theory]` with `[InlineData]` for parameterized
- Integration tests: use `WebApplicationFactory<T>` for in-memory test server

---

## Rust

### Rust 2024 Edition (Stable since 1.85.0)
- `cargo build`, `cargo test`, `cargo clippy`, `cargo fmt`
- **Async closures**: `async || {}` syntax now supported
- Commit `rust-toolchain.toml` at repo root for consistent toolchain
- Use `cargo fix` for edition migration — often automated
- After adding deps to `Cargo.toml`, `cargo build` to fetch

### Error Handling
- `Result<T, E>` for recoverable, `panic!` only for unrecoverable
- `thiserror` for library error types, `anyhow` for application errors
- Propagate with `?` — avoid `.unwrap()` in production

### Async Rust
- `tokio` is standard runtime — `#[tokio::main]`, `#[tokio::test]`
- Use `async fn` + `.await` — avoid blocking in async context
- `tokio::spawn` for concurrent tasks, `tokio::select!` for racing

### Web Frameworks
- **Axum**: handler functions with extractors, `Router`, `State` for shared state
- **Actix Web**: `#[actix_web::main]`, extractors (`web::Json`, `web::Path`)
- **Rocket**: attribute macros for routes (`#[get]`, `#[post]`)

### Workspace (Monorepo)
- Define members in root `Cargo.toml` `[workspace]`
- Share deps via `[workspace.dependencies]`
- `cargo build -p <package>` for specific crate

---

## Go

### Go 1.26 (Current Stable)
- `go build ./...`, `go test ./...`, `go vet ./...`
- `gofmt` / `goimports` for formatting
- `go mod tidy` after adding imports
- **Green Tea GC** enabled by default — improved garbage collection
- `new(T)` now accepts initial value expression
- Generic types can self-reference in type parameter list
- ~30% reduced cgo overhead
- Slices can be stack-allocated in more situations

### Go 1.25 Features
- `encoding/json/v2` (experimental) — enable via `GOEXPERIMENT=jsonv2`
- `WaitGroup.Go()` method — simplifies goroutine creation + counting

### Go 1.24 Features
- Generic type aliases fully supported
- Swiss Tables-based map implementation — better performance
- Tool directives in `go.mod` — no more `tools.go` hack

### Web Frameworks
- **Gin**: `r.GET("/path", handler)`, `c.Bind()`, `c.JSON()`
- **Echo**: `e.GET("/path", handler)`, middleware via `e.Use()`
- **Fiber**: Express-like, `c.BodyParser()` for parsing
- **Chi**: `r.Route("/path", func(r chi.Router) {...})`
- **stdlib** (Go 1.22+): `http.NewServeMux` with method patterns

### Testing
- `*_test.go` in same package, `go test -v -run TestName`
- `go test -race ./...` for race detection
- Table-driven tests are standard
- `httptest.NewServer` for HTTP handler tests

---

## Ruby

### Ruby on Rails 8.1 (Current Stable)
- `bundle install` after Gemfile changes, `bundle exec` for project gems
- **Built-in authentication generator** — `rails generate authentication`
- **Propshaft** is default asset pipeline (replaces Sprockets)
- **Kamal 2** as default deployment tool
- **Solid Queue** for background jobs (default in Rails 8.1)
- **Local CI** for running tests locally matching CI environment
- **Job Continuations** for resumable background jobs
- Ruby 3.2+ required (Rails 8.0 dropped Ruby 3.1)
- `rails db:migrate` after migration changes
- `rails generate` for scaffolding, `rails routes` to list routes
- Testing: `rails test` (Minitest) or `bundle exec rspec` (RSpec)
- Use concerns for shared model/controller logic

### RSpec
- `describe` for class/method, `context` for scenarios, `it` for expectations
- `let` / `let!` for test data, `before` for setup
- `FactoryBot` for test data generation

---

## C / C++

### C
- Build: `make`, `cmake --build .`, or `gcc -o output source.c`
- `cmake -B build && cmake --build build` for CMake
- `valgrind` for memory leak detection, `clang-format` for formatting
- Always check return values of `malloc`, `fopen`
- `snprintf` over `sprintf`, `strncpy` over `strcpy`
- Use `static` for file-scoped functions

### C++ (C++23 Current, C++26 Upcoming)
- `cmake -B build -DCMAKE_BUILD_TYPE=Release` for CMake builds
- Prefer smart pointers (`unique_ptr`, `shared_ptr`) over raw `new`/`delete`
- RAII for resource management

#### C++23 Features (Stable)
- `import std;` to import entire standard library
- `std::print` / `std::println` with format strings
- `std::expected` for error handling (alternative to exceptions)
- `std::flat_map` / `std::flat_set` for better cache performance
- `std::mdspan` for multidimensional arrays
- `std::generator` for coroutine-based ranges
- "Deducing this" — explicit `this` parameter in member functions

#### C++26 Features (Preview, `-std=c++2c`)
- **Reflection** — compile-time program introspection
- **Contracts** — preconditions, postconditions, invariants
- `std::execution` for structured concurrency
- Hazard Pointers and RCU for lock-free algorithms

### CMake
- `cmake -B build`, `cmake --build build`, `ctest --test-dir build`
- `set(CMAKE_CXX_STANDARD 23)` for standard version
- `FetchContent` for GitHub deps, `find_package` for system libs

---

## Swift / iOS

### Swift 6 (Current Stable)
- `swift build`, `swift test` (Swift Package Manager)
- Compile-time macros reduce boilerplate
- **Strict concurrency** — compiler enforces data race safety
- Structured concurrency with `async`/`await` (prefer over completion handlers)
- Use `let` over `var`, `guard` for early returns
- Prefer value types (`struct`, `enum`) over `class`
- **Swift Testing** framework (stable) — recommended over XCTest for new code

### SwiftUI (Xcode 17+)
- MVVM with SwiftUI is the default architecture for 2026
- `@State` for local, `@Binding` for child, `@StateObject`/`@ObservedObject` for observable
- `@Environment` for DI, `.task {}` for async work (auto-cancels)
- `NavigationStack` (iOS 16+) over deprecated `NavigationView`
- Xcode 17 previews update instantly with full interaction support
- iOS 17+ minimum recommended for new projects (unlocks SwiftData, newer APIs)

### Xcode CLI
- `xcodebuild -scheme <name> -sdk iphonesimulator build`
- `xcodebuild test -scheme <name> -destination 'platform=iOS Simulator,name=iPhone 16'`

---

## Kotlin / Android

### Kotlin 2.3 (Current Stable)
- `val` over `var` — immutability by default
- Data classes for DTOs, sealed classes for restricted hierarchies
- Null safety: `?.`, `?:`, avoid `!!`
- Coroutines: `suspend fun`, `launch`, `async`/`await`
- K2 compiler is default — faster compilation

### Android / Jetpack Compose
- `./gradlew assembleDebug` to build, `./gradlew test` for tests
- **Compose BOM 2025.05.01+** — autofill, auto-sizing text, visibility tracking, animate bounds
- **Pausable composition** in lazy prefetch enabled by default — reduces jank
- Compose performance now matches Views in scroll benchmarks
- Hilt for DI: `@HiltViewModel`, `@Inject`, `@Module`
- Room for local DB, Retrofit for networking
- `Navigation Compose` for screen navigation
- ProGuard/R8 rules: add keep rules for serialized classes

### Kotlin Multiplatform (KMP)
- Shared code in `commonMain`, platform-specific in `androidMain`/`iosMain`
- `expect`/`actual` for platform-specific implementations

---

## Java

### Java 24+ (Current)
- Use `var` for local type inference (Java 10+)
- Records for immutable data (Java 16+), sealed classes (Java 17+)
- Virtual threads for scalable concurrency (Java 21+)
- Prefer `Optional` over returning null

### Spring Boot 4 (Current, Nov 2025)
- **Modular autoconfiguration** — only relevant modules included, smaller runtime
- **API versioning** built-in
- **GraalVM native image** fully aligned with GraalVM 24 + AOT enhancements
- **Micrometer 2 + OpenTelemetry** starter for traces, logs, metrics
- **Spring Security 7** with built-in multi-factor authentication
- Constructor injection preferred over field injection
- `@Transactional` on service methods that modify data
- `@RestController` for APIs, `@Service` for logic, `@Repository` for data
- Testing: `@SpringBootTest` for integration, `@WebMvcTest` for controller-only

### Micronaut
- Compile-time DI — no reflection at runtime
- `./gradlew run` for dev mode

### Quarkus
- `quarkus dev` for dev mode with live reload
- Native compilation: `quarkus build --native`

---

## Flutter / Dart

### Dart 3.11 / Flutter 3.41 (Current Stable)
- **Dot shorthands** syntax for concise code
- **Build hooks** stable — compile native code (C/C++/Rust/Go) alongside Dart
- `dart analyze`, `dart format .`, `dart test`
- Analysis server 2x+ faster on large projects

### Flutter
- `flutter pub get` after changing `pubspec.yaml`
- `flutter run`, `flutter test`, `flutter analyze`
- **WebAssembly** becoming default web compilation target
- **Web hot reload** supported
- Cupertino squircles for native iOS fidelity
- `flutter clean` if build fails unexpectedly
- `flutter gen-l10n` after updating localization ARB files
- State management: follow project's pattern (Riverpod, Bloc, Provider)
- Use `const` constructors/widgets to prevent unnecessary rebuilds
- **GenUI SDK** (alpha) — LLM-powered UI generation with Flutter widgets

### Riverpod
- `ref.watch` in `build()`, `ref.read` in callbacks
- `@riverpod` annotation (codegen) — run `dart run build_runner build` after changes

### Bloc
- Events in → States out, `BlocProvider` for DI, `BlocBuilder`/`BlocListener` for UI

---

## Elixir / Phoenix

### Phoenix 1.8 / LiveView 1.1 (Current Stable)
- `mix deps.get` after changing `mix.exs`
- `mix compile`, `mix test`, `mix format`
- `mix ecto.migrate` after migration changes
- `mix phx.server` to start dev server
- **Scopes** — foundation for multi-tenant, team-based apps
- **AGENTS.md** generated for new apps — LLM-assisted development
- `phx.gen.auth` with **magic link** support out of the box
- Use pattern matching and guard clauses over conditionals
- Pipe operator `|>` for function chaining
- `GenServer` for stateful processes, `Task` for one-off async
- `iex -S mix` for interactive shell
- LiveView: `handle_event` for client events, `assign` for state

---

## PHP / Laravel

### PHP 8.4-8.5 (Current)
- Use `declare(strict_types=1)` at top of files
- Named arguments, match expressions, enums, readonly properties (PHP 8+)
- `composer install` after cloning, `composer update` to update

### Laravel 12 (Current, Feb 2025)
- `php artisan serve` for dev server
- `php artisan migrate` after migration changes
- `php artisan make:*` for scaffolding
- **Built-in health checks** — exposed via default routes
- **PHP 8+ route attributes** — attach routes directly above controller methods
- **Improved job batching** for background processing
- Starter kits for React, Svelte, Vue, Livewire with optional WorkOS AuthKit
- Use Form Requests for validation, Policies for authorization
- Queue jobs: `php artisan queue:work`
- `php artisan tinker` for REPL
- Cache: `php artisan config:cache`, `php artisan route:cache` for production
- Testing: `php artisan test` (Pest or PHPUnit)
- PHP 8.2+ required

### Symfony
- `php bin/console` for commands
- Use Attributes for routing (`#[Route]`), validation, serialization
- Doctrine ORM: `php bin/console doctrine:migrations:diff`
- `php bin/console cache:clear` when config changes aren't reflected

---

## AWS

### Runtime Identification (Critical)
- **Never assume** a service's runtime from language alone — always verify against infra config
- ECS indicators: `taskdef.json`, `ecs-task-definition.json`, CDK `ecs.FargateTaskDefinition`, `appspec.yml` with `ECS` hooks, `Dockerfile` in service folder
- Lambda indicators: SAM `template.yaml` with `AWS::Serverless::Function`, CDK `lambda.Function`, handler file matching SAM `Handler` property, no `Dockerfile` in folder
- Multi-runtime projects are common — a monorepo can have Lambda, ECS, and EC2 services side by side

### AWS CDK
- `cdk synth` to generate CloudFormation template, `cdk deploy` to deploy, `cdk diff` to preview changes
- `cdk.json` at project root defines the CDK app entry point
- Stacks in `*Stack.ts` — read these to understand what gets deployed where
- `npm run build` compiles TS before `cdk` commands (check `cdk.json` `app` field)
- Use `cdk context` for environment-specific values (account, region)
- CDK constructs map directly to AWS resources — read construct props to find runtime, memory, timeout

### AWS SAM
- `template.yaml` defines all Lambda functions and their source folders via `CodeUri`
- `sam build` then `sam deploy` (with `--guided` on first deploy)
- `sam local invoke` / `sam local start-api` for local testing
- `samconfig.toml` stores deploy parameters
- `Handler` field in `template.yaml` is the exact entry point — read it before assuming runtime behavior

### AWS Lambda
- Each function has a single handler — check SAM `Handler` or CDK `lambda.Function` `handler` prop
- Cold start is a concern for latency-sensitive workloads — check for provisioned concurrency config
- Layers for shared dependencies — check if project uses Lambda Layers
- Event sources (API Gateway, SQS, SNS, S3, EventBridge) affect how the handler is invoked
- `Lambda@Edge` / `CloudFront Functions` are different from regular Lambda — read event schema carefully

### Amazon ECS / Fargate
- Task definition (`taskdef.json`) defines container image, CPU/memory, env vars, port mappings
- Service definition controls desired count, load balancer, auto-scaling
- `appspec.yml` with `ECS` deployment type → CodeDeploy blue/green deployment
- Containers in same task can communicate via `localhost` — check for multi-container task defs

### AWS CodePipeline / CodeBuild
- `buildspec.yml` defines build phases — read to find actual build commands
- Pipeline stages (Source → Build → Test → Deploy) visible in `pipeline.ts` or console
- Environment variables injected via CodeBuild environment or SSM Parameter Store

---

## DevOps / Infrastructure

### Docker
- Multi-stage builds to reduce image size
- `.dockerignore` to exclude unnecessary files
- `docker compose up` for local dev, `docker compose up --build` to rebuild

### CI/CD
- Tests must pass before merge — check CI config for required checks
- Changes to lockfiles may require cache invalidation

### Terraform
- `terraform init`, `terraform plan`, `terraform apply`
- Always `plan` before `apply`
- `terraform fmt` for formatting
- Never commit `.tfstate` files

### Kubernetes
- `kubectl apply -f <file>` to deploy
- Use Kustomize or Helm for environment-specific configs

---

## Database / ORM

### Prisma
- `npx prisma generate` after schema changes
- `npx prisma migrate dev` for migrations
- `npx prisma db push` for prototyping
- Transactions for multi-table writes

### Drizzle
- `npx drizzle-kit generate` after schema changes
- `npx drizzle-kit push` to apply

### TypeORM
- `typeorm migration:generate` and `typeorm migration:run`
- QueryBuilder for complex queries, Repository for simple CRUD

### SQLAlchemy
- `alembic revision --autogenerate` then `alembic upgrade head`

### Mongoose (MongoDB)
- Define schemas with `mongoose.Schema`, models with `mongoose.model`
- `lean()` for read-only queries (faster, plain objects)
- Index with `{ index: true }` or `schema.index()`

---

## Monorepo Tools

### Turborepo
- `turbo run build` respects dependency graph
- `--filter=<package>` for specific packages
- Add new packages to `turbo.json` pipeline

### Nx
- `nx affected:test` to test only changed packages
- `nx graph` to visualize dependencies

### pnpm Workspaces
- `"@scope/shared": "workspace:*"` for workspace aliases
- `pnpm -r` for all packages, `pnpm --filter <pkg>` for specific
