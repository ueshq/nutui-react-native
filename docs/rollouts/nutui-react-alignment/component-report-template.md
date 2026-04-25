# Component Alignment Report Template

Each component implementation task starts with a report. The report is intentionally small and structured so the next task can modify code without rediscovering the same context.

## File Naming

Use:

```text
docs/rollouts/nutui-react-alignment/components/<component>.md
```

Examples:

- `components/button.md`
- `components/dialog.md`
- `components/datepicker.md`

## Report Template

```md
# <Component> Alignment Report

## 1. Source Files

- nutui-react:
- nutui-react-native:

## 2. nutui-react Props

| Prop | Type | Default | Notes |
| --- | --- | --- | --- |

## 3. Current RN Props

| Prop | Type | Default | Notes |
| --- | --- | --- | --- |

## 4. Missing Props

| Prop | RN mapping | Priority | Notes |
| --- | --- | --- | --- |

## 5. Unsupported Or Substitute Props

| Prop / Feature | Reason | RN substitute |
| --- | --- | --- |

## 6. Token And Style Gaps

| Token / Style | Target | Current RN | Plan |
| --- | --- | --- | --- |

## 7. Migration Plan

- API changes:
- Visual/token changes:
- Docs/demo changes:
- Compatibility notes:

## 8. Tests To Add

- Test:
- Test:
```

## Prompt: Generate A Component Report

```text
Compare ../nutui-react/src/packages/<component> with current components/<component>.

Output:
1. nutui-react props list
2. Current RN props list
3. Missing props
4. Props that are impossible or need substitutes in RN
5. Compatibility plan
6. Migration advice
7. Tests to add

Do not modify component code. Only create or update:
docs/rollouts/nutui-react-alignment/components/<component>.md
```

## Prompt: Align One Component API

```text
Based on docs/rollouts/nutui-react-alignment/components/<component>.md, update components/<component>.

Requirements:
1. Keep existing exports working.
2. Prefer nutui-react-compatible prop names where they make sense.
3. Keep RN-native events such as onPress as primary.
4. Add compatibility aliases such as onClick only when behavior is unambiguous.
5. Do not use DOM, className, CSS, SCSS, or browser-only APIs.
6. Add or update TypeScript types.
7. Add focused tests.
8. Update docs and demo only for implemented behavior.
```

## Prompt: Extract Component Tokens

```text
Extract tokens for <component> from ../nutui-react SCSS.

Requirements:
1. Generate components/<component>/tokens.ts when component-local tokens are enough.
2. Put shared values in components/tokens/** only when reused by multiple components.
3. Convert px values to numbers.
4. Document CSS-only values in unsupportedTokens.
5. Do not introduce styled-components, CSS runtime, or SCSS runtime.
```

## Prompt: Generate Compatibility Tests

```text
Add React Native tests for <component>.

Cover:
1. Target-compatible props from the report.
2. Existing RN props that must remain compatible.
3. Disabled and loading states when relevant.
4. Event alias behavior when relevant.
5. Icon/slot rendering when relevant.
6. accessibilityRole or accessibilityState when relevant.
```
