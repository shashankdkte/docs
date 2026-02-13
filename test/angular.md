Below is a *production-first* roadmap for mastering **modern Angular** at an expert level (roughly “top 5%”). It’s organized as **parent topics → child topics → grandchild topics**, and for each area you’ll see:
* **Where it shows up in real apps** (enterprise dashboards, e‑commerce, data-heavy UIs, etc.)
* **How to implement it** (concrete patterns, APIs, and “what to build”)
* **Benefits** (why teams use it)
* **Challenges** (what trips up experienced devs)
* **Project ideas** (portfolio-grade)
* **Open-source ideas** (libraries, schematics, builders, tooling)
> Notes on “modern Angular”: the roadmap leans into the current Angular direction: **standalone-first**, **signals-based reactivity**, **built-in template control flow**, **deferrable views**, **SSR + hydration + incremental hydration**, and **zoneless** capabilities. These are all covered in Angular’s official docs/roadmap. ([Angular][1])
---
## 0) Prerequisites that separate “good” from “elite”
### Parent: TypeScript mastery for framework-scale apps
* **Child: Type design for public APIs**
* Grandchild: generics for reusable components/services
* Grandchild: discriminated unions for UI state machines
* Grandchild: branded types for IDs and money/currency safety
* **Child: Strictness & correctness**
* Grandchild: `strict`, `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes`
* Grandchild: `tsconfig` layering for monorepos
**Production example**: designing a reusable “DataGrid” library where column definitions are type-safe and row actions are inferred.
**Practical implementation**
* Define a typed `ColumnDef` and make sorting/filtering operators typed.
* Use unions for UI state: `{kind:'loading'|'error'|'ready'}`.
**Benefits**: fewer runtime bugs, safer refactors across huge codebases.
**Challenges**: type complexity can slow development and confuse teams if you don’t standardize patterns.
**Project**: build a mini “component kit” (table, filters, forms) with strict typing.
**OSS angle**: publish a typed Angular utility library (e.g., typed table helpers, typed form builders).
---
### Parent: RxJS fundamentals (even in a signals-first world)
* **Child: Observable literacy**
* Grandchild: cold vs hot, multicasting, Subjects (and when *not* to use them)
* Grandchild: cancellation semantics and flattening operators (`switchMap`, `concatMap`, `exhaustMap`)
* **Child: Error handling**
* Grandchild: retry with backoff, circuit-breaker patterns, “fail open” vs “fail closed”
**Production example**: search suggestions with debouncing + cancellation + caching.
**Implementation**: `valueChanges.pipe(debounceTime, distinctUntilChanged, switchMap(query=>http(...)))`
**Benefits**: handles asynchronous workflows reliably.
**Challenges**: leaks and “ghost subscriptions” if you don’t consistently manage lifecycles.
---
## 1) Tooling & build pipeline: become the person who fixes CI, not fears it
### Parent: Angular CLI & workspace architecture
* **Child: Workspace configuration**
* Grandchild: `angular.json` targets (build/test/serve)
* Grandchild: environment configs + file replacements
* **Child: Modern build system (esbuild/Vite pipeline)**
* Grandchild: migration strategies + builder selection
* Grandchild: diagnosing bundle sizes and build perf regressions
**Production example**: a 300k+ LOC enterprise UI where local builds and CI time are a real cost center.
**Practical implementation**
* Move older workspaces to the newer builders when possible (Angular has official migration guidance). ([Angular][2])
* Set budgets; track bundle size per route; use build stats to regress-test.
**Benefits**: much faster builds, better dev feedback loops, more predictable bundling.
**Challenges**
* Legacy Webpack customizations (loaders/plugins) often don’t translate directly.
* “It builds locally but not in CI” due to Node versions, caching, or platform differences.
**Projects**
* Create an app + library workspace and set up: build budgets, source-map policies, and CI cache strategies.
* Add “bundle regression” checks in CI.
**Open-source ideas**
* Write a **custom CLI builder** that injects build metadata (git SHA, build time, feature flags) into the output. Angular documents how builders integrate with workspace config and how to create your own. ([Angular][3])
---
### Parent: Code quality gates that scale teams
* **Child: Linting & rules**
* Grandchild: Angular + TS lint rules
* Grandchild: custom ESLint rules for your org’s architecture constraints
* **Child: Formatting + commit discipline**
* Grandchild: consistent formatting; pre-commit hooks; conventional commits
**Production example**: preventing cross-layer imports (feature → feature) in a modular monorepo.
**Implementation**
* Write ESLint rules/plugins (ESLint provides tutorials for custom rules and plugins). ([ESLint][4])
**Benefits**: architectural consistency; faster onboarding; fewer “style wars”.
**Challenges**: rule fatigue; false positives; maintaining custom rules as Angular evolves.
**OSS angle**: publish “angular-architecture-eslint” plugin enforcing feature boundaries.
---
## 2) Modern Angular architecture: standalone-first + sane boundaries
### Parent: Standalone components as the default mental model
* **Child: Standalone building blocks**
* Grandchild: standalone components/directives/pipes
* Grandchild: incremental migration from NgModules
* **Child: Composition at scale**
* Grandchild: feature-based foldering
* Grandchild: domain-driven modules *without* NgModule overuse
**Production example**: multi-team enterprise apps where teams own vertical slices (billing, catalog, identity).
**Practical implementation**
* Prefer standalone components and route-level providers.
* Migrate gradually; Angular provides guidance for migrating to standalone. ([Angular][5])
**Benefits**: simpler mental model, less boilerplate, clearer dependency trees.
**Challenges**: legacy libraries and patterns may still expect modules; migration requires discipline to avoid hybrid chaos.
**Projects**
* “Enterprise Portal” with 6–8 feature areas, each owning routes, providers, and state.
* Enforce boundaries via lint rules.
**OSS**
* Create a “feature-slice starter” template repo and maintain schematics to generate new slices.
---
## 3) Templates & UI composition: where Angular apps are won or lost
### Parent: Modern template control flow
* **Child: Built-in control flow**
* Grandchild: `@if / @else`
* Grandchild: `@for` with tracking
* Grandchild: `@switch`
* **Child: Migration + consistency**
* Grandchild: migrating from `*ngIf/*ngFor/*ngSwitch`
* Grandchild: removing `CommonModule` dependency in standalone contexts
**Production example**: complex admin UIs with conditional panels, dynamic tables, and role-based UI.
**Practical implementation**
* Use built-in control flow where possible; it’s built into templates (no need to import `CommonModule` for it), and Angular provides a migration schematic. ([Angular][6])
* Prefer `@for (...; track ...)` with stable IDs for large lists to avoid DOM churn.
**Benefits**: cleaner templates, fewer imports/boilerplate, often easier to statically analyze.
**Challenges**
* Track expressions must be correct, or you’ll get subtle UI bugs.
* Teams mixing old/new syntax without conventions hurts readability.
---
### Parent: Deferrable views and intentional loading
* **Child: `@defer` blocks**
* Grandchild: `@placeholder`, `@loading`, `@error`
* Grandchild: triggers (`on viewport`, `on interaction`, etc.)
* **Child: Bundle + UX strategy**
* Grandchild: defer “below the fold” components
* Grandchild: defer expensive charts/editors
**Production example**: an analytics dashboard where initial load must be instant, but charts can load progressively.
**Practical implementation**
```html
@defer (on viewport) {
} @placeholder {
}
```
Deferrable views are designed to cut initial bundle and improve Core Web Vitals by deferring non-critical code. ([Angular][7])
**Benefits**: better LCP/TTI, smaller initial payload, more intentional UX.
**Challenges**
* Loading states become a UX design problem.
* If you defer the wrong thing, you get “janky” interactions or late layout shifts.
**Project**
* “E-commerce category page” with deferred recommendation widgets, reviews, and analytics.
---
### Parent: Component APIs with signals: `input()`, `output()`, `model()`
* **Child: Signal-based inputs**
* Grandchild: `input()` optional inputs
* Grandchild: `input.required()` required inputs
* Grandchild: input transforms (parse/coerce)
* **Child: Signal-based outputs**
* Grandchild: `output()` and `emit`
* Grandchild: programmatic subscriptions
* **Child: Two-way binding without boilerplate**
* Grandchild: `model()` input/output pair
Angular recommends signal-based `input()` for new projects (while `@Input` remains supported). ([Angular][8])
`model()` is a stable API for a writable signal exposed as an input/output pair. ([Angular][9])
`output()` is the functional output declaration API. ([Angular][10])
**Production example**: a reusable “FilterPanel” component used across many screens:
* `input()` for configuration
* `model()` for selected filters (two-way)
* `output()` for analytics events (“filterApplied”)
**Implementation sketch**
```ts
import { Component, input, model, output } from '@angular/core';
@Component({ /* ... */ })
export class FilterPanel {
config = input.required();
selected = model({}); // [(selected)] binding
analytics = output(); // (analytics)="..."
}
```
**Benefits**: simpler component contracts, more reactive composition, less boilerplate for two-way bindings.
**Challenges**
* Migrating older patterns (`@Input` setters, `EventEmitter`) across a big codebase requires standards.
* Teaching teams how to avoid over-coupling state with `model()` everywhere.
**Projects**
* Build a component library of 10–15 reusable widgets using `input()/output()/model()` consistently.
**OSS**
* Publish a “signal-component-patterns” starter kit + migrations; Angular even provides automated migrations for signal inputs. ([Angular][11])
---
## 4) Dependency Injection: power, not magic
### Parent: DI patterns that matter in enterprises
* **Child: Provider scopes & lifetimes**
* Grandchild: root vs route vs component providers
* Grandchild: isolating state per route using route-level providers
* **Child: Tokens & multi-providers**
* Grandchild: feature flags via injection tokens
* Grandchild: extensibility via multi-providers (plugin architectures)
**Production example**: a “plugin” system where multiple teams contribute menu items, dashboards, and permissions independently.
**Practical implementation**
* Use a multi-provider token like `APP_PLUGINS` and let each feature provide its plugin object.
* Aggregate in a shell component.
**Benefits**: extensibility, testability, clean runtime composition.
**Challenges**
* Circular dependencies, unexpected singleton state, test complexity when providers are scattered.
**Project**
* “Extensible Admin Shell” where features register routes/menu/actions via multi-providers.
**OSS**
* A tiny library showing “Angular plugin architecture via DI” + schematics to register plugins automatically.
---
## 5) Routing & navigation: treat it as an application platform
### Parent: Router as a composition + data-loading layer
* **Child: Route design**
* Grandchild: feature routes, nested routes, route-level providers
* Grandchild: preloading strategies for UX
* **Child: Guards & resolvers**
* Grandchild: auth + role checks
* Grandchild: data prefetch to eliminate spinners
* **Child: Rendering modes (SSR/CSR/SSG)**
* Grandchild: per-route rendering decisions
Angular supports multiple rendering modes (SSR/CSR/Prerender) via server routing configuration. ([Angular][12])
**Production example**
* E-commerce:
* Product detail pages SSR/prerender for SEO
* Account pages CSR
* Admin pages CSR behind auth
**Implementation**
* Use resolvers or signal resources for prefetching.
* Route-level providers isolate feature stores and services.
**Benefits**: better perceived performance, cleaner feature isolation, SEO control.
**Challenges**
* Guard chains can become a maze.
* SSR/auth combos require careful token handling and redirects.
**Projects**
* “Hybrid rendered storefront”: SSR for catalog/product, CSR for account/admin.
---
## 6) Reactivity & state: master both Signals *and* RxJS
### Parent: Signals (core reactive primitive)
* **Child: Core primitives**
* Grandchild: `signal()` for mutable state
* Grandchild: `computed()` for derived state
* Grandchild: `effect()` for side effects
* **Child: Side effects done right**
* Grandchild: cleanup and cancellation patterns
* Grandchild: avoid effects for pure derivations
* **Child: Async data with `resource`**
* Grandchild: loading/error/data as signals
* Grandchild: request invalidation + refetching policies
Signals track state usage granularly so Angular can optimize rendering updates. ([Angular][13])
Effects run based on signals read, and Angular supports cleanup functions to cancel work when effects re-run or are destroyed. ([Angular][14])
The `resource` API exists to manage async dependencies in signal-based code and is marked experimental. ([Angular][15])
**Production example**
* A trading dashboard:
* signals for UI state (selected instrument, expanded panels)
* resources for data (quote stream snapshot, positions)
* effects for “sync URL with selected instrument” + analytics
**Implementation patterns**
* Keep **signals for local/UI state and derivations**.
* Use **resources** for request-driven async data where you want synchronous access patterns.
* Use **effects** for bridging to “non-reactive worlds” (logging, storage, imperative APIs), with cleanup for cancellations.
**Benefits**
* Predictable reactive graphs
* Less change detection work (especially when combined with OnPush/zoneless)
* Easier to reason about than ad-hoc Subjects everywhere
**Challenges**
* Overusing effects leads to spaghetti.
* Teams must agree on boundaries: *signals vs Observables vs store libraries*.
**Project**
* “Real-time ops dashboard” with:
* local UI signals
* async resources for lists and details
* derived computed metrics
* effects for persistence (localStorage) and telemetry
**OSS**
* Publish a small “signal-store” library with patterns for:
* feature slices
* entity caches
* optimistic updates
* devtools hooks
---
### Parent: RxJS interop (glue that keeps large apps sane)
* **Child: Observable → Signal**
* Grandchild: `toSignal()` for synchronous reactive access to an Observable stream
* **Child: Signal → Observable**
* Grandchild: `toObservable()` for bridging into RxJS pipelines
* **Child: Lifecycle-safe subscriptions**
* Grandchild: `takeUntilDestroyed()` to prevent leaks
Angular documents RxJS interop utilities (`toSignal`, etc.). ([Angular][16])
`toSignal` returns a Signal tracking an Observable subscription. ([Angular][17])
`toObservable` exposes a Signal as an Observable and propagates values using an effect. ([Angular][18])
`takeUntilDestroyed` completes the Observable when the calling context is destroyed. ([Angular][19])
**Production example**: integrating with legacy RxJS-heavy libraries (websocket streams, ngrx effects, etc.) while new code uses signals.
**Implementation sketch**
```ts
import { toSignal } from '@angular/core/rxjs-interop';
readonly user = toSignal(this.userService.user$, { initialValue: null });
```
**Benefits**: gradual modernization; avoids “rewrite everything”.
**Challenges**: mixing paradigms without standards leads to duplicated state and hard-to-debug flows.
**Project**
* Take a mid-size sample app and refactor:
* UI state → signals
* async streams remain RxJS
* use `toSignal/toObservable` for boundaries
---
## 7) Forms: complex validation + performance under pressure
### Parent: Reactive forms at enterprise scale
* **Child: Typed reactive forms**
* Grandchild: strongly typed `FormGroup`/`FormControl`
* Grandchild: typed reusable validators
* **Child: Dynamic forms**
* Grandchild: schema-driven form generation
* Grandchild: conditional controls + async validators
* **Child: Signal-based forms (emerging)**
* Grandchild: learn the direction + evaluate for new apps
Angular v21 announced experimental Signal Forms (signals-based forms experience). ([Angular Blog][20])
**Production examples**
* Banking onboarding: multi-step forms, cross-field validation, async checks, drafts.
* Admin backoffice: large forms with conditional sections and role-based fields.
**Practical implementation**
* Build a `FormFactory` service that creates typed form groups per domain model.
* Centralize validation error mapping into reusable UI components.
* Use incremental rendering / deferred sections for huge forms.
**Benefits**: predictable validation, testable flows, maintainability.
**Challenges**
* Form performance (lots of controls) can degrade if you trigger excessive updates.
* Keeping server-side validation consistent with client rules.
**Projects**
* “Multi-step onboarding wizard” with draft saving, server validation integration, and accessibility.
**OSS**
* Publish a “typed validators” library or a schema-to-reactive-forms generator.
---
## 8) HTTP + integration: build robust data layers (not “just call HttpClient”)
### Parent: HTTP architecture & policies
* **Child: HttpClient usage patterns**
* Grandchild: typed API clients
* Grandchild: pagination, filtering, cancellation
* **Child: Interceptors**
* Grandchild: auth token injection
* Grandchild: retries/backoff
* Grandchild: caching, logging, correlation IDs
Angular’s docs describe interceptors as middleware for retrying, caching, logging, authentication, etc. ([Angular][21])
**Production example**: a SaaS app where every request needs:
* auth
* tenant context
* correlation ID
* standardized error mapping
* caching for “reference data” (countries, roles, feature flags)
**Implementation**
* Create a layered API:
1. low-level `HttpClient`
2. typed “API client” services per bounded context
3. a query/cache layer (signals resource or RxJS caching)
* Use interceptors for cross-cutting concerns:
* auth headers
* error normalization
* retry policy (only for idempotent calls)
* caching for GETs
**Benefits**: consistent behavior, fewer bugs, clearer responsibilities.
**Challenges**
* Caching correctness (invalidation!)
* SSR caching differences (server vs client)
* Avoiding “interceptor spaghetti” (too many interceptors doing too much)
**Projects**
* “Enterprise directory” app with advanced search, caching, and offline-ish behavior.
**OSS**
* Publish an interceptor toolkit: retry-with-backoff, cache-with-invalidation, typed error mapping.
---
### Parent: SSR data reuse (TransferState + caching)
* **Child: TransferState**
* Grandchild: server prefetch → reuse on client
* **Child: SSR-safe caching**
* Grandchild: avoid leaking user data across requests
* Grandchild: per-request caches
Angular has a TransferState concept for transferring key-value state from server to client (documented in Angular API docs). ([Angular][22])
**Production example**: product pages SSR fetch product details; client should not refetch immediately.
**Implementation**
* Cache SSR-fetched GET responses into TransferState (commonly via an interceptor) and hydrate client caches from it.
**Challenges**
* Correct cache scoping on server to avoid cross-user leaks
* Ensuring hydration doesn’t mismatch DOM/state
---
## 9) Rendering & performance: where top 5% engineers live
### Parent: Change detection & rendering mechanics
* **Child: OnPush + immutability discipline**
* Grandchild: `ChangeDetectionStrategy.OnPush` for predictable updates
* Grandchild: stable references, trackBy for lists
* **Child: Zoneless Angular**
* Grandchild: what breaks when Zone.js is removed
* Grandchild: event + async update strategies
* **Child: Hydration & incremental hydration**
* Grandchild: full hydration
* Grandchild: incremental hydration boundaries using `@defer` triggers
* Grandchild: event replay considerations
Angular has an official guide for **zoneless** mode and notes that OnPush is recommended for compatibility (not required, but recommended). ([Angular][23])
Angular’s docs describe hydration and what it does (reusing server DOM, transferring state, etc.). ([Angular][24])
Incremental hydration builds on hydration + deferrable views; you can add hydration triggers on `@defer` blocks to define hydration boundaries. ([Angular][25])
Angular’s roadmap states incremental hydration shipped as developer preview in v19 and became stable in v20 (Q2 2025). ([Angular][1])
**Production example**: news site / content platform:
* SSR for fast first paint
* incremental hydration for comments, recommendations, and rich widgets
* defer non-critical components
**Practical implementation**
1. Start with SSR + hydration.
2. Identify expensive interaction zones (comments, live widgets).
3. Wrap them in `@defer` with explicit hydration triggers:
* hydrate on interaction (user intent)
* hydrate on viewport (scroll intent)
**Benefits**
* Faster real-user performance, better CWV
* More control over interactivity cost
* Great for large pages (e-commerce PDPs, dashboards)
**Challenges**
* Hydration mismatch bugs can be subtle.
* Requires careful “what runs on server vs client” thinking.
* Team discipline: performance becomes a product requirement, not an afterthought.
**Project**
* “SEO-first storefront”:
* SSR product pages
* defer recommendations/reviews
* incremental hydration for interactive sections
**OSS**
* Build an “Angular performance cookbook” repo with runnable examples:
* `@defer` patterns
* incremental hydration triggers
* caching + TransferState patterns
* profiling scripts
---
## 10) Testing & reliability: ship confidently in large teams
### Parent: Multi-layer testing strategy
* **Child: Unit tests**
* Grandchild: services, pure utilities, state reducers
* Grandchild: RxJS marble tests for complex streams
* **Child: Component/integration tests**
* Grandchild: test rendering + interactions + DI overrides
* Grandchild: contract tests for feature boundaries
* **Child: E2E**
* Grandchild: Playwright/Cypress user flows
* Grandchild: performance assertions (LCP thresholds in CI)
* **Child: SSR/hydration testing**
* Grandchild: validate server render output + client hydration stability
**Production example**: regulated industries (fintech/health) with strict release quality.
**Implementation**
* Make your test pyramid explicit:
* 70% unit
* 20% component/integration
* 10% E2E
* Add “golden path” E2E flows per domain.
* Include SSR render tests for critical routes.
**Benefits**: fewer regressions, faster refactors, confidence adopting new Angular features.
**Challenges**
* Slow flaky E2E if you try to test everything end-to-end.
* TestBed complexity if your architecture is not modular.
**Project**
* Add contract tests and SSR tests to your portfolio app; show CI badges and coverage reports.
**OSS**
* Contribute reproductions + failing tests to Angular ecosystem repos when you hit edge cases.
---
## 11) Security: know what Angular does for you—and what it doesn’t
### Parent: Web security in Angular apps
* **Child: Angular built-in protections**
* Grandchild: XSS prevention, sanitization boundaries
* Grandchild: safe handling of HTML/URLs
* **Child: App-level security**
* Grandchild: authentication (OIDC/OAuth2)
* Grandchild: authorization (RBAC/ABAC)
* Grandchild: secure storage + CSRF strategy
* **Child: SSR security**
* Grandchild: per-request isolation; secrets handling; headers
Angular has official security best practices describing built-in protections against common vulnerabilities (like XSS) and points to OWASP guidance. ([Angular][26])
**Production example**: enterprise SaaS with embedded rich text, file uploads, and role-based UI.
**Implementation**
* Enforce “no bypassSecurityTrust* except in audited wrappers”.
* Centralize auth/session handling; never scatter token logic across components.
* Add CSP headers where feasible.
**Challenges**
* Developers often rely on “Angular sanitizes everything” and miss business-logic security.
* SSR introduces new leak vectors if caches are global.
**Project**
* “Customer support portal” with rich text rendering, uploads, and strict RBAC.
**OSS**
* Publish a “secure-by-default” Angular starter: CSP + lint rules that prevent unsafe patterns.
---
## 12) Accessibility & internationalization: enterprise-grade UX
### Parent: A11y as a first-class engineering concern
* **Child: Keyboard navigation, focus management**
* Grandchild: roving tabindex patterns
* Grandchild: focus traps in dialogs
* **Child: ARIA correctness**
* Grandchild: label relationships
* Grandchild: live regions for async updates
* **Child: i18n**
* Grandchild: pluralization, date/number formatting
* Grandchild: RTL layouts
* Grandchild: translation extraction pipeline
**Production example**: government/education software requiring WCAG compliance.
**Implementation**
* Build a component library where accessibility is baked in (and tested).
* Add automated a11y checks in CI (axe-based scanning).
**Challenges**
* A11y bugs often look “fine” visually.
* i18n affects layout and can break responsive design.
**Project**
* “Accessible component library” + demo site with screen-reader notes.
**OSS**
* Contribute accessibility improvements to existing Angular component libs.
---
## 13) Enterprise architecture: scale code, teams, and releases
### Parent: Modularization & monorepo strategy
* **Child: Domain boundaries**
* Grandchild: “feature slice” architecture
* Grandchild: shared vs core vs UI libraries
* **Child: Versioning & releases**
* Grandchild: semantic versioning for internal libs
* Grandchild: migration guides + codemods
**Production example**: multiple apps (admin, customer portal, partner portal) sharing libraries.
**Implementation**
* Maintain shared libraries with strict API boundaries.
* Add “ng update” style migrations internally (via schematics).
**Challenges**
* Over-sharing “utility” libraries creates spaghetti dependencies.
* Teams need governance: what goes where, who owns what.
**Project**
* Build 2 apps in one workspace + shared libraries; enforce boundaries.
**OSS**
* Create a schematics collection that:
* generates feature slices
* registers routes/providers
* updates navigation config automatically
---
## 14) Library/tooling engineering: the “top 5% unlock”
If you can **build and maintain Angular libraries + schematics + builders**, you’re operating at an expert ecosystem level.
### Parent: Building and publishing Angular libraries
* **Child: Library structure**
* Grandchild: public API surface (`public-api.ts`)
* Grandchild: peer dependencies and Angular version compatibility
* **Child: Packaging**
* Grandchild: Angular CLI uses **ng-packagr** to create publishable packages. ([Angular][27])
* Grandchild: secondary entry points for large libs
**Production example**: a private design system library used by 10+ apps.
**Implementation**
* Use `ng generate library` to scaffold a library. ([Angular][28])
* Add storybook/docs site (optional) and strict CI.
**Benefits**: reuse, consistency, speed, reduced duplication.
**Challenges**
* API design and long-term compatibility are hard.
* Breaking changes are expensive; you need migration tooling.
---
### Parent: Schematics (codify your architecture)
* **Child: `ng-add` and generators**
* Grandchild: auto-install + auto-configure patterns
* Grandchild: code mods for migrations
* **Child: Schematics collections**
* Grandchild: project file edits, AST transforms, options schemas
Angular explicitly supports creating schematics collections for libraries (including `ng-add`). ([Angular][29])
**Production example**: installing your internal “TelemetryModule” should auto:
* add providers
* add interceptor
* add env config entries
* update app shell template
**Benefits**: consistent setup, fewer manual mistakes, faster onboarding.
**Challenges**
* AST transforms require care; brittle migrations can break user projects.
* Versioning: each release must ship migrations.
**OSS ideas**
* Create schematics for:
* adding SSR/hydration
* adding interceptors + typed API client templates
* generating feature slices with route-level providers
---
### Parent: Custom Angular CLI builders
* **Child: Builder fundamentals**
* Grandchild: how builders integrate with workspace targets
* Grandchild: custom build steps (codegen, asset stamping, analysis)
Angular’s docs explain CLI builders and how you can create your own. ([Angular][3])
**Production example**
* A builder that:
* generates an OpenAPI client before build
* fails build if bundle budget regresses
* injects environment metadata
**Benefits**: automation, consistent pipelines, less tribal knowledge.
**Challenges**: maintaining compatibility across Angular versions and build system changes.
**OSS**
* Publish a “build-metadata” builder or “openapi-codegen” builder.
---
## 15) Portfolio projects that prove expert-level Angular
Build *at least two* of these end-to-end (with tests + CI + docs), and you’ll touch nearly every expert topic above:
1. **Hybrid-rendered E-commerce**
* SSR/prerender for product/catalog routes ([Angular][12])
* `@defer` for reviews/recommendations ([Angular][7])
* incremental hydration boundaries for interactive sections ([Angular][25])
* caching via interceptors ([Angular][21])
* performance budgets in CI
2. **Enterprise Admin + Plugin System**
* multi-provider DI plugin model
* route-level providers per feature
* strict architecture lint rules
* state with signals/resources + RxJS interop ([Angular][13])
3. **Design System Library**
* publishable Angular library (ng-packagr) ([Angular][27])
* include schematics for `ng-add` and component generators ([Angular][29])
* a11y-first components + a11y tests
4. **Real-time Operations Dashboard**
* websocket/SSE streams (RxJS)
* signals for UI state/derived metrics ([Angular][13])
* `takeUntilDestroyed` for safe subscriptions ([Angular][19])
* deferred charts for performance ([Angular][7])
---
## 16) Open-source contribution paths (practical + realistic)
### Parent: Start contributing without needing “framework core” access
* **Child: Documentation and examples**
* Grandchild: improve docs, add runnable examples, fix edge cases
* **Child: Reproduction repositories**
* Grandchild: minimal repro + tests + clear issue reports
* **Child: Ecosystem packages**
* Grandchild: small libraries, builders, schematics, ESLint plugins
### Concrete OSS ideas (Angular-specific)
1. **A library**
* “signal-store” patterns
* typed form helpers
* caching interceptor toolkit
Use Angular’s library creation flow (CLI + ng-packagr). ([Angular][27])
2. **A schematics collection**
* `ng-add` for your library and a generator for standard feature slices
Angular supports schematics for libraries and explains how to structure collections. ([Angular][29])
3. **A custom builder**
* OpenAPI client generation + build-time checks
Angular documents CLI builders and how to create them. ([Angular][3])
4. **An ESLint plugin**
* enforce modern control flow migration or architecture boundaries
ESLint has official guidance for creating custom rules/plugins. ([ESLint][4])
---
## What “top 5% Angular” looks like in practice
You’re operating at expert level when you can reliably do most of this:
* Design app architecture that scales across teams (feature isolation, DI composition)
* Use **signals/resources/effects** appropriately (no reactive spaghetti) ([Angular][13])
* Bridge RxJS ↔ signals cleanly (`toSignal`, `toObservable`, `takeUntilDestroyed`) ([Angular][17])
* Ship **SSR + hydration**, and selectively adopt **incremental hydration + @defer** for performance ([Angular][12])
* Debug performance regressions with evidence (profiling, budgets, CWV metrics)
* Build internal/external libraries with **schematics and builders**, not just components ([Angular][27])
---
If you want, I can also turn this roadmap into:
* a **12-week schedule** (what to learn/build each week),
* a **skills checklist** you can use in interviews/performance reviews,
* or a **project blueprint** (architecture diagram + folder structure + implementation milestones) for one of the portfolio apps above.
[1]: https://angular.dev/roadmap?utm_source=chatgpt.com "Angular Roadmap"
[2]: https://angular.dev/tools/cli/build-system-migration?utm_source=chatgpt.com "Migrating to new build system"
[3]: https://angular.dev/tools/cli/cli-builder?utm_source=chatgpt.com "Angular CLI builders"
[4]: https://eslint.org/docs/latest/extend/custom-rule-tutorial?utm_source=chatgpt.com "Custom Rule Tutorial - ESLint - Pluggable JavaScript Linter"
[5]: https://angular.dev/reference/migrations/standalone?utm_source=chatgpt.com "Standalone"
[6]: https://angular.dev/reference/migrations/control-flow?utm_source=chatgpt.com "Migration to Control Flow syntax"
[7]: https://angular.dev/guide/templates/defer?utm_source=chatgpt.com "Deferred loading with @defer"
[8]: https://angular.dev/guide/components/inputs?utm_source=chatgpt.com "Accepting data with input properties"
[9]: https://angular.dev/api/core/model?utm_source=chatgpt.com "model"
[10]: https://angular.dev/api/core/output?utm_source=chatgpt.com "output"
[11]: https://angular.dev/reference/migrations/signal-inputs?utm_source=chatgpt.com "Migration to signal inputs"
[12]: https://angular.dev/guide/ssr?utm_source=chatgpt.com "Server-side and hybrid-rendering"
[13]: https://angular.dev/guide/signals?utm_source=chatgpt.com "Signals • Overview • Angular"
[14]: https://angular.dev/guide/signals/effect?utm_source=chatgpt.com "Side effects for non-reactives APIs"
[15]: https://angular.dev/guide/signals/resource?utm_source=chatgpt.com "Async reactivity with resources"
[16]: https://angular.dev/ecosystem/rxjs-interop?utm_source=chatgpt.com "RxJS interop with Angular signals"
[17]: https://angular.dev/api/core/rxjs-interop/toSignal?utm_source=chatgpt.com "toSignal"
[18]: https://angular.dev/api/core/rxjs-interop/toObservable?utm_source=chatgpt.com "toObservable"
[19]: https://angular.dev/api/core/rxjs-interop/takeUntilDestroyed?utm_source=chatgpt.com "takeUntilDestroyed"
[20]: https://blog.angular.dev/announcing-angular-v21-57946c34f14b?utm_source=chatgpt.com "Announcing Angular v21"
[21]: https://angular.dev/guide/http/interceptors?utm_source=chatgpt.com "Intercepting requests and responses"
[22]: https://v17.angular.io/api/platform-browser/TransferState?utm_source=chatgpt.com "TransferState"
[23]: https://angular.dev/guide/zoneless?utm_source=chatgpt.com "Zoneless"
[24]: https://angular.dev/guide/hydration?utm_source=chatgpt.com "Hydration"
[25]: https://angular.dev/guide/incremental-hydration?utm_source=chatgpt.com "Incremental Hydration"
[26]: https://angular.dev/best-practices/security?utm_source=chatgpt.com "Security"
[27]: https://angular.dev/tools/libraries/creating-libraries?utm_source=chatgpt.com "Creating Libraries"
[28]: https://angular.dev/cli/generate/library?utm_source=chatgpt.com "generate library"
[29]: https://angular.dev/tools/cli/schematics?utm_source=chatgpt.com "Generating code using schematics"
