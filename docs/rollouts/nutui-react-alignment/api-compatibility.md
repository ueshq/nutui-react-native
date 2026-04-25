# API Compatibility ADR

## Decision

Align `nutui-react-native` public APIs with the current `nutui-react` component contracts where the behavior makes sense in React Native. Do not copy DOM, Taro, class-name, CSS, or SCSS implementation details into RN.

The migration order is:

1. Read `nutui-react` props and docs.
2. Compare current RN props.
3. Add target-compatible props or aliases.
4. Keep RN-native props and behavior primary.
5. Implement with RN primitives.
6. Document unsupported or substituted behavior.
7. Add focused tests.

## Global Mapping Rules

| nutui-react concept | RN stance | Notes |
| --- | --- | --- |
| `onClick` | compatibility alias | RN primary event remains `onPress`. If supported, call `onClick` from the same guarded press path. Do not expose DOM `MouseEvent`. |
| `className` | unsupported | Use RN `style`, component-specific style props, and tokens. |
| `style` | supported with RN style types | Split View/Text styles where needed. |
| text styling through CSS selectors | RN substitute | Add explicit props such as `textStyle` when a component renders text. |
| `nativeType` | DOM-only | Either omit from RN types or accept and ignore with docs. Component report must decide. |
| CSS variables | token extraction | Convert to TS tokens or theme fields. Do not add CSS runtime. |
| SCSS selectors | implementation reference only | Extract semantic values and states, not selector structure. |
| pseudo selectors | RN substitute | Use `Pressable` state, explicit overlay views, or RN Animated. |
| CSS transitions | RN substitute | Use pressed state or RN Animated only when needed. |
| Taro entry files | reference only | Do not port platform-specific Taro imports directly. |
| refs | RN-specific | Use RN component refs and document differences from DOM element refs. |
| icons as React nodes | prefer support | Existing string icon APIs can remain as compatibility aliases. |
| accessibility | RN-native | Use `accessibilityRole`, `accessibilityState`, and labels where appropriate. |

## Button Seed Contract

The Button pilot should converge on this public shape while preserving existing imports:

```ts
export type ButtonType =
  | 'default'
  | 'primary'
  | 'info'
  | 'success'
  | 'warning'
  | 'danger'

export type ButtonSize =
  | 'xlarge'
  | 'large'
  | 'normal'
  | 'small'
  | 'mini'

export type ButtonShape = 'square' | 'round'

export type ButtonFill = 'solid' | 'outline' | 'dashed' | 'none'
```

RN Button props should prefer:

```ts
export interface ButtonProps {
  type?: ButtonType
  size?: ButtonSize
  shape?: ButtonShape
  fill?: ButtonFill
  color?: string
  block?: boolean
  loading?: boolean
  disabled?: boolean
  icon?: React.ReactNode
  rightIcon?: React.ReactNode
  children?: React.ReactNode
  onPress?: () => void
  onClick?: () => void
  style?: ViewStyle
  textStyle?: TextStyle
}
```

Compatibility decisions:

- Keep `onPress` as the primary RN event.
- Support `onClick` as an alias only through the same disabled/loading guard.
- Keep existing `plain` as an alias for `fill="outline"` unless `fill` is explicitly passed.
- Widen `icon` to `React.ReactNode`; keep string icon behavior if existing demos rely on it.
- Add `rightIcon`.
- Add `xlarge` and `mini`.
- Do not support `className`.
- Treat `nativeType` as unsupported or accepted-and-ignored; the Button implementation batch must choose and document one.

## Implementation Guidance

Use RN primitives for internals:

- `Pressable` for press state when replacing old touchables.
- `Text` for string children.
- `View` for layout.
- `ActivityIndicator` for loading when appropriate.
- `Modal` or the existing portal stack for dialog-like components.
- RN Animated or current animation helpers for motion.

No component should add DOM helpers, CSS selectors, SCSS imports, or browser-only event objects to satisfy target API parity.

## Test Guidance

Each component report should list tests before code changes. For Button, required tests include:

- `type` variants such as `primary`, `default`, and `danger`.
- `size` variants `xlarge`, `large`, `normal`, `small`, and `mini`.
- `disabled` does not trigger `onPress` or `onClick`.
- `loading` does not trigger `onPress` or `onClick`.
- `onClick` compatibility works when enabled.
- `icon` and `rightIcon` render.
- `accessibilityRole="button"` after Pressable migration.
