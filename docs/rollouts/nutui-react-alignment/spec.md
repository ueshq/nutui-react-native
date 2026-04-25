# NutUI React Alignment Specification

## 1. Executive Summary

- Initiative type: convergence.
- Primary repo: `/home/afu/dev/nutui-react-native`.
- Read-only reference repo: `/home/afu/dev/nutui-react`.
- Primary decision: make the `nutui-react-native` API, visual language, tokens, and documentation match the current `../nutui-react` version as closely as React Native allows.
- Definition of success: the React Native package exposes a documented, tested, and demoable subset that intentionally matches `../nutui-react` API, visuals, token names, and docs where the platform allows it; unsupported or deferred gaps are explicit, measurable, and not hidden behind stale config entries.

## 2. Problem And Current State

### 2.1 Problem Statement

`@nutui/nutui-react-native` has drifted from the sibling `../nutui-react` project. The drift is visible in component coverage, config metadata, demo coverage, documentation shape, generated entry points, verification, and shared utility infrastructure. The current repository already contains some target component names in `sites/config.json`, but many of those entries have no corresponding `components/<name>/` implementation. That makes alignment hard to reason about and makes future component work easy to duplicate or mis-sequence.

### 2.2 Current State

- Current package: `@nutui/nutui-react-native` version `0.0.8`.
- Target package: `@nutui/nutui-react-taro` version `3.0.19`.
- Current runtime/tooling: `.nvmrc` pins Node `16.18.1`; repository scripts assume Yarn 1 and `react-native-builder-bob`.
- Target runtime/tooling: `../nutui-react/package.json` requires `pnpm >=10.0.0`; CI uses Node `20`; no target `.nvmrc` was present.
- Current component root: `components/<name>/`, with React Native implementations, `styles.ts`, `demo.tsx`, and `commutiy/` plus `jd/` docs.
- Target component root: `../nutui-react/src/packages/<name>/`, with H5 and Taro implementations, SCSS, per-platform demos, docs, generated package entry files, tests, and shared `src/utils`, `src/hooks`, `src/locales`, and `src/types`.
- Current doc generator only emits `sites/docs.ts` from `sites/config.json` and `components/<name>/commutiy/*.md`.
- Target generator emits package entry files, style imports, docs, and online-edit SCSS metadata from `src/config.json`.
- Current `components/index.ts` is manually maintained.
- Current `tsconfig.json` includes only `vite/*.ts` and `vite.config.ts`, so the root `yarn typecheck` path does not yet prove component type safety.
- Current repository has CI jobs for lint, typecheck, test, and bob build, but there are currently no component test files under `components/`.

### 2.3 Baseline Evidence

Inventory commands were run from `/home/afu/dev/nutui-react-native`.

- Component directories, excluding `hooks`, `utils`, and `portal`: current `66`, target `106`.
- Target package directories missing from current components: `43`.
- Current component directories not present in target packages: `sidenavbaritem`, `subsidenavbar`, `timepannel`.
- Current `sites/config.json` package entries: `93`; target `src/config.json` package entries: `105`.
- Target config names missing in current config: `avatarcropper`, `calendarcard`, `datepickerview`, `hoverbutton`, `hoverbuttonitem`, `loading`, `lottie`, `pickerview`, `resultpage`, `safearea`, `segmented`, `sidebar`, `sidebaritem`, `space`, `tour`.
- Current config names not in target config: `sidenavbar`, `sidenavbaritem`, `subsidenavbar`.
- Current config entries without a component directory: `28`.
- Test files: current `0`, target package tests `135`.
- Component docs: current `155` Markdown docs, target `352` Markdown docs.
- Component demos: current `49` component `demo.tsx` files, target `87`.
- Demo registry: `demo/demoList.ts` has `56` title entries, while current config has `50` `show: true` entries; visible config entries missing from the demo list are `configprovider`, `dialog`, and `row`.

Target package directories missing from React Native, grouped by target config area:

- `layout`: `layout`, `safearea`, `space`, `sticky`.
- `nav`: `hoverbutton`, `hoverbuttonitem`, `sidebar`, `sidebaritem`, `tabpane`, `tabs`.
- `dentry`: `address`, `calendarcard`, `cascader`, `datepicker`, `datepickerview`, `form`, `formitem`.
- `dataentry`: `menu`, `menuitem`, `numberkeyboard`, `picker`, `pickerview`, `range`, `shortpassword`, `signature`, `uploader`.
- `feedback`: `infiniteloading`, `loading`, `popover`, `pulltorefresh`, `resultpage`.
- `exhibition`: `animate`, `animatingnumbers`, `audio`, `circleprogress`, `imagepreview`, `lottie`, `segmented`, `tour`, `video`.
- `business`: `avatarcropper`, `barrage`, `watermark`.

## 3. Goals, Non-Goals, And Success Metrics

### 3.1 Goals

- Produce an explicit parity matrix between `components/` and `../nutui-react/src/packages/`.
- Define which NutUI React props, behaviors, docs, and demos are portable to React Native, which need RN-specific equivalents, and which are unsupported.
- Align public props first, not implementation internals: read the web/Taro component contract, design the RN contract, then implement with RN primitives.
- Extract visual alignment into typed tokens such as `components/<name>/tokens.ts` or shared `components/tokens/**` modules.
- Make `sites/config.json`, `components/index.ts`, component docs, and demo registration reflect real implementation state.
- Add or update missing components in dependency-safe slices, starting with low-risk primitives and shared dependencies before high-complexity widgets.
- Raise verification coverage from no component tests to focused tests for newly aligned or newly added components.
- Preserve existing RN import paths and platform behavior unless a compatibility decision explicitly says otherwise.

### 3.2 Non-Goals

- Do not migrate this repository wholesale to pnpm, Node 20, Vite 5, Vitest, or the target repo build system as part of the first alignment pass.
- Do not blindly copy DOM, SCSS, or Taro-specific implementation details into React Native components.
- Do not introduce CSS runtime, styled-components, or SCSS runtime to achieve token parity.
- Do not remove existing public exports such as `SideNavBar`, `SubSideNavBar`, or `SideNavBarItem` without a compatibility bridge and migration note.
- Do not edit `../nutui-react`; it is a read-only source of truth.
- Do not attempt pixel-perfect SCSS parity where RN layout, text rendering, gestures, or native modules require different implementation choices.

### 3.3 Success Metrics

- Catalog parity metric: every package in `../nutui-react/src/config.json` is classified as `complete`, `partial`, `missing`, `config-only`, `risky`, `unsupported`, or `renamed-compatible`.
- API parity metric: every changed component has a report covering target props, current RN props, missing props, unsupported/substitute props, migration advice, and tests.
- Token parity metric: every changed component has token notes or `tokens.ts`; CSS-only features are listed in `unsupportedTokens` or the component report.
- Visual parity metric: P0 components use extracted tokens for size, spacing, radius, color, and disabled/loading state where RN supports them.
- Registry integrity metric: no `show: true` entry points to a missing component directory, missing docs, or missing demo.
- Component coverage metric: all newly implemented components include `index.ts`, implementation, `styles.ts` when applicable, `demo.tsx`, community docs in `zh-CN`, `zh-TW`, and `en-US`, and focused tests where behavior is non-trivial.
- Verification metric: `yarn lint`, `yarn typecheck`, `yarn test --passWithNoTests`, and `yarn prepack` pass after each phase gate that changes code.
- Documentation metric: docs state RN-specific API differences from NutUI React rather than implying unsupported DOM/Taro behavior exists.

## 4. Principles And Target State

### 4.1 Design Principles

- Source-compatible where practical: visual and behavioral props should converge with NutUI React names and defaults when they make sense in RN.
- Platform-correct where necessary: RN event names, gesture handling, native modules, accessibility, and style objects may differ from DOM/Taro contracts.
- Additive migration first: prefer adding target-compatible props or aliases before removing existing RN props.
- Token-first visual alignment: extract stable semantic values from NutUI React SCSS and translate them into RN-safe TypeScript tokens before reshaping component styles.
- One component per coding task: implementation tasks should stay small enough for focused review and rollback.
- Dependency-aware sequencing: implement shared primitives before components that depend on them.
- Evidence over aspiration: config, docs, and demos should only mark a component visible when the implementation is real and verified.
- Keep the current repository conventions: Yarn 1, Node 16.18.1, `components/<name>/`, `commutiy/`, Expo demo, Vite docs, and bob build remain the baseline unless an approved ADR changes them.

### 4.2 Target State

- `docs/rollouts/nutui-react-alignment/` contains the spec, implementation plan, automation pipelines, matrix, token strategy, component report template, and per-component reports.
- A repeatable inventory command reports drift between current RN and target NutUI React.
- Alignment tooling lives under `tools/align/**` or `scripts/**`, with preference for small commands that can scan, diff props, extract tokens, and generate reports independently.
- `sites/config.json` mirrors target grouping where useful, but every RN-specific divergence is explicit.
- `components/index.ts` and demo/doc registration are generated or checked to prevent silent drift.
- Existing components are reviewed against target docs and behavior, then updated one component at a time according to priority.
- Missing components are added in dependency order.
- Component styles consume component-local or shared RN token modules instead of copied SCSS.
- Tests and type checks cover component code, not only the Vite docs shell.

## 5. Workflows Or Capability Slices

The rollout is organized as five automation pipelines. Details live in `alignment-pipelines.md`.

### 5.1 Component Matrix Pipeline

- Trigger: target repo updates or a new alignment phase starts.
- Happy path: scan both repositories, produce `matrix.md`, and classify every target component by API status, style status, priority, and note.
- Important edge cases: renamed components, RN-only compatibility aliases, target packages that require web-only APIs, and config entries that are hidden but still exported.
- Acceptance notes: every target package has a status and owner batch; every implementation task links to a component report.

### 5.2 Props And API Pipeline

- Trigger: a component is selected from the matrix.
- Happy path: generate a report with target props, current RN props, missing props, unsupported/substitute props, migration advice, and tests.
- Important edge cases: target `className`, `nativeType`, CSS variables, DOM events, Taro entry files, refs, slots, and web-only demos.
- Acceptance notes: RN-native props such as `onPress` remain primary; compatibility aliases such as `onClick` are added only when behavior is clear.

### 5.3 Token Pipeline

- Trigger: a component has visual or style drift.
- Happy path: extract NutUI React SCSS variables into TS tokens, document unsupported CSS-only features, and update RN `StyleSheet` code to consume tokens.
- Important edge cases: gradients, pseudo selectors, transitions, CSS variable cascading, shorthand padding, and RN `pt`/`px` scaling.
- Acceptance notes: visual alignment happens through tokens, not copied SCSS or DOM class names.

### 5.4 Priority Lane Pipeline

- Trigger: the matrix is ready for implementation sequencing.
- Happy path: process P0 components first, then P1, then P2 design notes or deferrals.
- Important edge cases: P1 components need gestures, scroll, keyboard, or popup orchestration; P2 components may need RN redesign instead of direct parity.
- Acceptance notes: implementation tasks should handle one component at a time; grouped batches are planning lanes, not a license for broad diffs.

### 5.5 Agent Coding Workflow Pipeline

- Trigger: an individual component is ready for code changes.
- Happy path: report -> API migration -> token extraction -> RN implementation -> docs/demo -> focused tests -> scoped verification.
- Important edge cases: components needing native media, file upload, cropper, Lottie, portal positioning, scroll measurement, or animation.
- Acceptance notes: each PR-sized task is reviewable, reversible, and grounded in a component report.

## 6. Functional Requirements

### 6.1 Required Capability: Inventory And Parity Matrix

- Description: compare current RN component directories, config entries, exports, docs, demos, and tests against target NutUI React.
- Inputs: `components/`, `sites/config.json`, `components/index.ts`, `demo/demoList.ts`, `../nutui-react/src/config.json`, `../nutui-react/src/packages/`.
- Outputs: Markdown report and machine-readable JSON summary.
- Business rules: target repo is read-only; current config cannot be treated as proof of implementation.
- Error handling: report missing target repo, unparsable config, missing docs, missing demos, and missing exports as actionable failures.

### 6.2 Required Capability: API Compatibility Decisions

- Description: document portable props, RN-specific aliases, deprecated props, and unsupported target features.
- Inputs: target docs and implementation per component.
- Outputs: per-component API notes in docs or an alignment ADR.
- Business rules: RN event and style contracts win over web/Taro contracts when they conflict.
- Error handling: unresolved decisions block `show: true` registration for newly added components.

### 6.3 Required Capability: Token Extraction And Visual Alignment

- Description: extract visual values from NutUI React SCSS into RN-safe TypeScript tokens.
- Inputs: `../nutui-react/src/styles/variables*.scss`, `../nutui-react/src/packages/<name>/*.scss`, current `components/<name>/styles.ts`.
- Outputs: `components/<name>/tokens.ts`, optional shared `components/tokens/**`, unsupported token notes, and token-backed `StyleSheet` updates.
- Business rules: convert `px` to numbers; do not introduce CSS runtime; keep unsupported CSS-only behavior explicit.
- Error handling: ambiguous token scaling decisions, such as target pixels vs current RN `pt`, must be documented before style changes land.

### 6.4 Required Capability: Component Delivery

- Description: implement or align components in dependency-safe batches.
- Inputs: target docs, target demos, current RN patterns, shared utilities, ConfigProvider theme and locale.
- Outputs: RN implementation, docs, demo, tests, exports, config registration.
- Business rules: use `pt` and `px` helpers; split View/Text styles with existing filter utils; keep overlay components compatible with `react-native-root-portal`.
- Error handling: unsupported native capabilities must fail gracefully or remain unregistered until dependency strategy is approved.

### 6.5 Required Capability: Verification

- Description: prove each batch through deterministic local commands.
- Inputs: changed files and package scripts.
- Outputs: command logs and updated evidence notes.
- Business rules: do not rely on manual visual judgment as the only acceptance signal.
- Error handling: failing lint, typecheck, tests, build, or demo import checks block phase completion.

## 7. Delivery Strategy

### 7.1 Proposed Phase Shape

- Phase `00-baseline-contract`: establish the scan/report tooling, matrix, API compatibility rules, token strategy, and verification baseline.
- Phase `01-foundation`: improve generators, registry checks, type/test coverage, token extraction tooling, shared utilities, locales, and docs/demo scaffolding.
- Phase `02-p0-component-alignment`: align P0 components one at a time, starting with Button as the API/token/test pilot.
- Phase `03-missing-foundation-components`: add low-risk missing primitives and P1 navigation foundations such as layout, safe area, tabs, and sidebar equivalents.
- Phase `04-data-entry-stack`: implement P1 picker, form, input-heavy, date, address, and upload-related components in dependency order.
- Phase `05-feedback-media-business`: handle P2/high-risk portal, media, tour, cropper, watermark, table, virtual list, animation, and business components through design notes, implementation, or explicit deferral.
- Phase `06-docs-demo-release`: finalize docs, demo, CI, build, migration notes, and release readiness.

### 7.2 Rollout And Rollback Notes

- Stage changes by hidden or non-visible registration first, then promote `show: true` only after docs/demo/tests pass.
- Add target-compatible props before deprecating old RN props.
- Keep compatibility wrappers for renamed or target-removed current exports until a major-release decision is explicit.
- Keep token changes component-local until shared tokens are proven reusable.
- Roll back by reverting a batch or hiding the affected component in `sites/config.json`; do not leave visible docs pointing to missing implementation.

## 8. Technical Boundaries

### 8.1 Likely Repo Areas Touched

- `components/**`
- `components/**/tokens.ts`
- `components/tokens/**`
- `components/index.ts`
- `components/configprovider/**`
- `components/hooks/**`
- `components/utils/**`
- `tools/align/**`
- `demo/demoList.ts`
- `demo/**` when demo app registration changes
- `sites/config.json`
- `sites/**` and `vite/**` when docs generation changes
- `scripts/**`
- `tsconfig*.json`
- `.github/workflows/**`
- `package.json`

### 8.2 Interfaces, Data, Or Contracts

- Public package imports from `@nutui/nutui-react-native` and `@nutui/nutui-react-native/<component>`.
- Component props and emitted events.
- Component token names and RN token module exports.
- Theme and locale contracts exposed by `ConfigProvider`.
- Generated docs file `sites/docs.ts`.
- Bob build outputs under `lib/`.
- Demo app imports through workspace package subpaths.

### 8.3 Runtime And Environment Assumptions

- Primary runtime remains Node `16.18.1` unless a later tooling ADR changes it.
- Package manager remains Yarn 1 unless a later tooling ADR changes it.
- Expo demo remains the first interactive validation surface for RN/Web behavior.
- Optional native or peer dependencies must be documented before registration.

## 9. External Dependencies And Coordination

- `../nutui-react` is a sibling read-only reference and may move independently.
- Some target capabilities may need RN alternatives or peer dependencies: media playback, upload, image cropper, Lottie, scroll measurement, keyboard overlays, and animation.
- JD/community doc variants need product ownership decisions if docs diverge from target NutUI React.
- Release sequencing may need a compatibility note if target-compatible props change current defaults.

## 10. Hard Rules

- Do not edit `../nutui-react`.
- Do not rename `commutiy` without updating every consumer.
- Do not mark a component `show: true` unless implementation, docs, demo, and exports are present.
- Do not remove existing public RN exports without an explicit compatibility decision.
- Do not copy DOM implementation details; align public props and user-visible behavior, then implement with RN primitives.
- Do not copy DOM, SCSS, or Taro-only implementation details into RN code without adaptation.
- Do not introduce CSS runtime, styled-components, or SCSS runtime for token parity.
- Keep code diffs to one component per implementation task unless the task is tooling or docs-only.
- Run the listed verification commands before marking a batch complete.

## 11. Verification And Evidence

### 11.1 Global Verification Commands

- `yarn lint`
- `yarn typecheck`
- `yarn test --passWithNoTests`
- `yarn prepack`
- `npm run generate:docs`
- `npm run generate:jd:docs`

### 11.2 Evidence To Capture

- Inventory report showing current vs target catalog status.
- Matrix report showing API status, style status, priority, and notes.
- API compatibility ADR or per-component reports.
- Token extraction notes or `tokens.ts` files for changed components.
- Test output for newly added or aligned components.
- Typecheck output that includes component code once the dedicated config is added.
- Bob build output.
- Demo app smoke notes for Expo Web when UI behavior changes.

### 11.3 Batch-Level Verification Guidance

- Prefer targeted tests for touched components before global checks.
- Prefer generated inventory checks for config/export/doc/demo consistency.
- Use `show: false` for incomplete components so docs and demo remain honest.
- Capture unsupported target features explicitly rather than relying on TODO comments.

## 12. Risks, Assumptions, And Open Questions

- Risk: target APIs are DOM/Taro oriented and may not map cleanly to RN props.
- Risk: adding many components without shared primitives first can create duplicate utility code.
- Risk: current root typecheck does not cover components, so enabling real component typecheck may expose large unrelated debt.
- Risk: token extraction can accidentally change RN sizing semantics if target px values are applied without a `pt`/scaling decision.
- Risk: media, upload, cropper, and Lottie components may require dependency decisions outside a single code batch.
- Assumption: alignment means catalog and behavior convergence, not identical implementation or identical toolchain.
- Assumption: token parity means semantic visual convergence, not direct SCSS compatibility.
- Assumption: existing RN exports remain supported through at least the first alignment release.
- Open question: should RN accept target event aliases such as `onClick`, or keep RN-only events and document the mapping?
- Open question: should target config grouping be mirrored exactly, including `dentry` and `dataentry`, or should RN keep its current grouping with compatibility metadata?
- Open question: should JD docs continue to duplicate community docs, or should the generator support fallback to community docs when `jd/` docs are absent?
- Open question: should shared tokens live under `components/tokens/**` permanently, or become a future `packages/nutui-tokens` workspace package?
- Open question: should target px values be used directly in RN, or translated through the current `pt`/`px` scaling helpers?
- Open question: which advanced components require approved native or peer dependencies before implementation?

## 13. Definition Of Done

- A parity matrix exists and is repeatable.
- The five automation pipelines are documented and reflected in the plan.
- All target components are classified and assigned to a phase or explicit non-goal.
- Existing RN components have API parity notes and focused tests for changed behavior.
- Changed components have token notes or token modules, with unsupported CSS-only behavior documented.
- Newly visible components include implementation, docs, demo, exports, and tests where appropriate.
- Config, docs, demo registry, and package exports are internally consistent.
- Global verification commands pass.
- Remaining unsupported or deferred target features are documented with owners and rationale.
