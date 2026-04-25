# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

`@nutui/nutui-react-native` is a JD-style mobile UI component library for React Native. It targets iOS, Android, and Web (via `react-native-web`/Expo). Node `16.18.1` (see [.nvmrc](.nvmrc)); the toolchain is built around Yarn 1.

## Common commands

| Task | Command |
| --- | --- |
| Install everything (root + demo) | `yarn bootstrap` |
| Run the Expo demo on Web | `yarn dev` |
| Run the Expo demo on iOS | `yarn dev:ios` (Android: `yarn --cwd demo android`) |
| Lint (ESLint + Prettier) | `yarn lint` (auto-fix: `yarn lint --fix`) |
| Type-check | `yarn typecheck` (this runs `generate:docs` first because it writes `sites/docs.ts`) |
| Tests (Jest, RN preset) | `yarn test` — single file: `yarn test path/to/file.test.tsx`; single name: `yarn test -t "pattern"` |
| Doc site (dev) | `yarn doc` (community variant: `yarn doc:jd`) |
| Build the lib (commonjs + module + d.ts via `react-native-builder-bob`) | `yarn prepack` |

[lefthook.yml](lefthook.yml) wires `pre-commit` to run ESLint + `tsc --noEmit` on staged files, and `commit-msg` to enforce Conventional Commits via commitlint. Don't bypass these — fix the underlying issue.

## Architecture

### Component layout (`components/<name>/`)

Every component follows the same shape — copy this when adding a new one:

- `<name>.tsx` — implementation. Public surface is re-exported from `index.ts` (e.g. [components/button/index.ts](components/button/index.ts)).
- `styles.ts` — a function `(theme) => StyleSheet.create({ ... })`. Use the `pt`/`px` helpers from [components/utils/](components/utils/) for sizing rather than raw numbers (the design system targets JD's 750-wide spec).
- `demo.tsx` — interactive demo that the Expo app and the doc site both import. Strings go through `useTranslate` from [components/hooks/useTranslate.ts](components/hooks/useTranslate.ts) keyed by short hashes; new demos must include `zh-CN`, `zh-TW`, and `en-US` entries to match the three doc files.
- `commutiy/doc.md`, `doc.zh-TW.md`, `doc.en-US.md` — community/general docs. (Note the misspelling `commutiy` is the canonical directory name across the repo.)
- `jd/doc*.md` — JD-internal variant of the docs, surfaced via `yarn doc:jd`.

The package's `react-native` entry is [components/index.ts](components/index.ts) (used in dev/Metro), while npm consumers get the bob-built `lib/{commonjs,module,typescript}` outputs. When adding a component, register it in **both** `components/index.ts` **and** `sites/config.json` (with `show: true`) so the doc generator picks it up.

### Shared infrastructure

- [components/configprovider/](components/configprovider/) — top-level `ConfigProvider` exposing `useConfig()` (theme, locale). Most components consume it; wrap test/demo trees in it.
- [components/hooks/](components/hooks/) — `useLocale` and `useTranslate` for i18n. Locale dictionaries live under `components/configprovider/locales/`.
- [components/utils/](components/utils/) — sizing (`pt`, `px`, `deviceWidth`, `iphone-x`, `statusBarHeight`), style filters (`filter-size-margin-container`, `filter-text-style`, etc. — used to split a single `style` prop into View/Text-safe subsets), color helpers (gradient detection via `isLinearGradient`, `getColor`), and `style-to-obj` for parsing string styles. Reuse these instead of re-deriving sizing or color logic in a component.
- Overlay/portal-style components (Toast, Dialog, Popup, ActionSheet, Notify) rely on `react-native-root-portal` — your app root needs the corresponding `RootSiblingParent` for them to render.
- `expo-linear-gradient` and `react-native-linear-gradient` are **peer deps**, not bundled. Components that use gradients (e.g. Button) should branch on platform via the helpers in `utils/color`.

### Demo app (`demo/`)

A standalone Expo project with its own `package.json` and `node_modules`. It imports the library through the workspace name `@nutui/nutui-react-native/<component>/demo`, so changes in `components/` are picked up live without rebuild. iOS/Android changes that touch native deps require a rebuild of the demo app. The demo registry is [demo/demoList.ts](demo/demoList.ts) — add your component's demo here too.

### Doc site (`sites/` + `vite/`)

A Vite + React app that renders the per-component Markdown. The pipeline is:

1. `scripts/generate-nutui.js` (or `generate-jd-nutui.js`) reads [sites/config.json](sites/config.json) and writes `sites/docs.ts` with imports of each component's `commutiy/` (or `jd/`) Markdown via Vite's `?raw` loader.
2. `vite/config.ts` (selected by `vite.config.ts`) serves/builds the site.

`yarn typecheck` invokes `generate:docs` first because `sites/docs.ts` is gitignored/generated and `tsc` will otherwise fail on missing imports.

## Conventions worth knowing

- **Commits:** Conventional Commits (`feat:`, `fix:`, `refactor:`, `docs:`, `test:`, `chore:`); enforced by commitlint.
- **Style prop splitting:** React Native is strict about which style keys belong on `View` vs `Text`. When a component accepts a single `style` prop, route it through the appropriate `filter-*` util before applying — see [components/button/button.tsx](components/button/button.tsx) for the pattern.
- **Sizing:** prefer `pt(n)` / `px(n)` from [components/utils/](components/utils/) over raw numbers so layouts scale consistently across devices.
- **Directory name `commutiy`:** typo, but it's the canonical name everywhere (config, scripts, generators). Don't "fix" it without updating all consumers.
