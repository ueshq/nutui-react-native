# Alignment Automation Pipelines

The alignment goal is: make `nutui-react-native` match the current `nutui-react` API, visual language, tokens, and documentation as closely as React Native allows.

The rollout should be organized around five repeatable pipelines. Each pipeline produces structured evidence that the next pipeline can consume.

## 1. Component Matrix Pipeline

Purpose: keep the catalog honest before implementation starts.

Inputs:

- `../nutui-react/src/config.json`
- `../nutui-react/src/packages/**`
- `components/**`
- `sites/config.json`
- `components/index.ts`
- `demo/demoList.ts`

Outputs:

- `docs/rollouts/nutui-react-alignment/matrix.md`
- optional machine-readable JSON inventory
- per-component status: `implemented`, `partial`, `missing`, `rn-only`, `unsupported`, or `renamed-compatible`

Rules:

- Config entries are not proof of implementation.
- A visible component must have implementation, docs, demo, export, and verification.
- RN-only compatibility names must be explicit, not accidental.

## 2. Props And API Pipeline

Purpose: align external API shape without copying DOM or Taro internals.

Per-component output:

1. `nutui-react` props.
2. Current RN props.
3. Missing props.
4. Props that are impossible or need RN substitutes.
5. Compatibility plan.
6. Migration advice.
7. Test cases.

Rules:

- Prefer additive target-compatible props first.
- RN native event names remain primary, for example `onPress`.
- Target compatibility aliases can be supported, for example `onClick`, when the behavior is unambiguous.
- DOM props such as `className` and `nativeType` must either be ignored, mapped, or documented as unsupported.

## 3. Token Pipeline

Purpose: align visual language through tokens instead of copying SCSS.

Inputs:

- `../nutui-react/src/styles/variables*.scss`
- `../nutui-react/src/packages/<component>/*.scss`
- current RN `styles.ts`

Outputs:

- component-local tokens such as `components/button/tokens.ts`
- optional shared token modules such as `components/tokens/**`
- unsupported token notes for CSS-only features
- RN `StyleSheet` updates using numeric values and token references

Rules:

- Convert CSS `px` values into RN numbers.
- Preserve semantic token names where possible.
- Do not add a CSS runtime, styled-components, or SCSS dependency to RN.
- Gradients, pseudo selectors, transitions, and CSS variables require RN-specific mapping or unsupported notes.

## 4. Priority Lane Pipeline

Purpose: keep PRs small and order components by RN migration difficulty.

Priority lanes:

- `P0`: simple primitives and high-value public APIs.
- `P1`: components involving gestures, scroll, keyboard, popup orchestration, or controlled value flows.
- `P2`: components with performance risk, DOM measurement, CSS transition assumptions, media/native dependencies, or platform-specific redesign needs.

Rules:

- Code migration should handle one component per Codex task unless the work is analysis-only.
- Shared dependencies are implemented before dependent components.
- P2 components require an explicit RN design note before implementation.

## 5. Agent Coding Workflow Pipeline

Purpose: make each Codex invocation reviewable and reversible.

Default sequence for one component:

1. Generate or update the per-component diff report.
2. Align props and TypeScript types.
3. Extract component tokens.
4. Update RN implementation using RN primitives.
5. Update docs and demo.
6. Add focused tests.
7. Run scoped verification, then global verification at phase gates.

Standard task prompts are captured in `component-report-template.md`.
