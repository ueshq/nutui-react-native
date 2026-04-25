# Token Alignment Strategy

The preferred visual alignment path is token extraction, not SCSS copying.

## Target Shape

Component styles should move toward this shape:

```text
components/
  tokens/
    colors.ts
    spacing.ts
    radius.ts
    typography.ts
  button/
    tokens.ts
    styles.ts
```

An optional future package such as `packages/nutui-tokens` can be introduced only after a workspace/tooling decision. Until then, component-local tokens and `components/tokens/**` keep the change small and compatible with the current Yarn 1 package.

## Extraction Rules

- Read variables from `../nutui-react/src/styles/variables*.scss`.
- Read component SCSS from `../nutui-react/src/packages/<component>/<component>.scss`.
- Keep semantic names close to NutUI React, for example `buttonDefaultHeight` or `buttonPrimaryBackgroundColor`.
- Convert pixel values such as `32px` into numbers such as `32`.
- Convert padding shorthands into RN-safe objects or tuples.
- Keep gradients as structured values only when the current component already supports gradient rendering.
- Put CSS-only features in `unsupportedTokens`.

## Unsupported Token Examples

These should not be copied into RN styles directly:

- CSS pseudo selectors such as `::before` and `::after`.
- CSS transitions and timing functions.
- CSS variables that require runtime cascading.
- `cursor`, `user-select`, `touch-action`, and browser appearance resets.
- Complex selectors whose behavior depends on DOM nesting.

## Button Seed Tokens

The target Button variables include:

```ts
export const buttonTokens = {
  defaultHeight: 32,
  xlargeHeight: 48,
  largeHeight: 40,
  smallHeight: 28,
  miniHeight: 24,
  normalHorizontalPadding: 12,
  largeHorizontalPadding: 16,
  xlargeHorizontalPadding: 24,
  smallHorizontalPadding: 8,
  miniHorizontalPadding: 8,
  defaultBorderWidth: 0.5,
  primaryTextColor: '#ffffff',
  defaultBackgroundColor: 'transparent',
}

export const unsupportedButtonTokens = {
  transition: 'CSS opacity transition must be represented with RN press state or Animated.',
  pseudoMask: 'The active ::before mask must be represented with Pressable pressed state.',
  cssVariableCascade: 'RN cannot consume CSS custom properties without a separate theme bridge.',
}
```

The first implementation batch for Button should decide whether to preserve the existing RN `pt` scaling for dimensions or use target numeric tokens directly. The decision must be documented before visual changes land.
