# Button Alignment Report

## 1. Source Files

- nutui-react implementation: `../nutui-react/src/packages/button/button.tsx`
- nutui-react styles: `../nutui-react/src/packages/button/button.scss`
- nutui-react variables: `../nutui-react/src/styles/variables.scss`
- nutui-react-native implementation: `components/button/button.tsx`
- nutui-react-native styles: `components/button/styles.ts`

## 2. nutui-react Props

| Prop | Type | Default | Notes |
| --- | --- | --- | --- |
| `type` | `default` \| `primary` \| `info` \| `success` \| `warning` \| `danger` | `default` | Same type union exists in RN. |
| `size` | `xlarge` \| `large` \| `normal` \| `small` \| `mini` | `normal` | RN currently lacks `xlarge` and `mini`. |
| `shape` | `square` \| `round` | `round` | Same union exists in RN. |
| `fill` | `solid` \| `outline` \| `dashed` \| `none` | `outline` in implementation defaults, documented as `solid` | RN currently uses `plain` instead. |
| `color` | `string` | empty string | Target supports gradient strings; RN already has gradient helper behavior for Button. |
| `block` | `boolean` | `false` | RN has `block`. |
| `loading` | `boolean` | `false` | RN has `loading`, but current implementation uses custom icon behavior rather than `ActivityIndicator`. |
| `disabled` | `boolean` | `false` | RN has `disabled`. |
| `icon` | `React.ReactNode` | `null` | RN currently types `icon` as `string`. |
| `rightIcon` | `React.ReactNode` | `null` | Missing in RN. |
| `nativeType` | `submit` \| `reset` \| `button` | `button` | DOM-only; not meaningful in RN. |
| `onClick` | `(e: MouseEvent<HTMLButtonElement>) => void` | noop | Should be supported as compatibility alias without DOM event object. |
| `style` | `CSSProperties` through `BasicComponent` | none | RN has `style` through `TouchableHighlightProps`. |
| `className` | `string` through `BasicComponent` | empty | Unsupported in RN. |

## 3. Current RN Props

| Prop | Type | Default | Notes |
| --- | --- | --- | --- |
| `type` | `default` \| `primary` \| `info` \| `success` \| `warning` \| `danger` | `default` | Target-compatible. |
| `size` | `large` \| `normal` \| `small` | `normal` | Missing `xlarge` and `mini`. |
| `shape` | `square` \| `round` | `round` | Target-compatible. |
| `color` | `string` | empty string | Existing custom color behavior should remain. |
| `plain` | `boolean` | `false` | Existing RN API; should map to `fill="outline"` for compatibility. |
| `disabled` | `boolean` | `false` | Target-compatible. |
| `block` | `boolean` | `false` | Target-compatible. |
| `icon` | `string` | undefined | Should widen to `ReactNode`; string icon compatibility can remain. |
| `loading` | `boolean` | `false` | Target-compatible. |
| `activeStyle` | `StyleProp<ViewStyle>` | undefined | RN-only interaction style. |
| `children` | `ReactNode` | undefined | Target-compatible. |
| `onPress` | RN press handler from `TouchableHighlightProps` | undefined | RN primary event. |
| `style` | RN style prop | undefined | Should remain for root/container styles. |

## 4. Missing Props

| Prop | RN mapping | Priority | Notes |
| --- | --- | --- | --- |
| `fill` | Add `ButtonFill`; map `outline` to current `plain` behavior. | P0 | Prefer `fill`; keep `plain` as compatibility alias. |
| `rightIcon` | Render after text/content. | P0 | Should accept `ReactNode`. |
| `onClick` | Call after `onPress` guard when not disabled/loading. | P0 | No DOM event object; docs must state RN signature. |
| `size="xlarge"` | Add token and style variant. | P0 | Target height `48`. Need scaling decision. |
| `size="mini"` | Add token and style variant. | P0 | Target height `24`. Need scaling decision. |
| `textStyle` | New RN-specific prop for text styling. | P0 | Prevent View/Text style mixing. |

## 5. Unsupported Or Substitute Props

| Prop / Feature | Reason | RN substitute |
| --- | --- | --- |
| `className` | RN does not support DOM class names. | Use `style` and `textStyle`; document unsupported. |
| `nativeType` | RN Button is not a DOM submit/reset element. | Accept but ignore, or omit from RN public type. ADR must decide. |
| DOM `MouseEvent` | RN press events differ from DOM mouse events. | `onPress?: () => void`; `onClick?: () => void` compatibility alias. |
| CSS pseudo active mask | RN has no `::before`. | Use `Pressable` pressed state or existing highlight behavior. |
| CSS transition | RN has no CSS transition. | Use pressed opacity or RN Animated if required. |

## 6. Token And Style Gaps

| Token / Style | Target | Current RN | Plan |
| --- | --- | --- | --- |
| Default height | `32px` | `pt(76)` for normal | Decide whether Button follows NutUI token numbers directly or RN scaled `pt`. |
| Size variants | `xlarge`, `large`, `normal`, `small`, `mini` | `large`, `normal`, `small` | Add missing variants. |
| Fill variants | `solid`, `outline`, `dashed`, `none` | `plain` boolean and type styles | Implement `fill`, map `plain` to `outline`. |
| Text icon spacing | `4px` default, `6px` xlarge | `pt(20)` indicator margin | Tokenize icon/text spacing. |
| Primary background | gradient token | hard-coded gradient helper | Preserve gradient support but move values into tokens. |
| Disabled colors | tokenized by type/fill | opacity only | Add tokenized disabled styles where practical. |

## 7. Migration Plan

- Add `ButtonFill` and widen `ButtonSize` to include `xlarge` and `mini`.
- Add `rightIcon`, `onClick`, and `textStyle`.
- Keep `plain`; treat it as a compatibility alias for `fill="outline"` unless `fill` is explicitly passed.
- Keep `onPress` as the RN primary event and guard both `onPress` and `onClick` when `disabled` or `loading`.
- Consider replacing `TouchableHighlight` with `Pressable` in a focused implementation batch, but preserve export shape and current styling behavior during the transition.
- Add `components/button/tokens.ts` before major visual changes.
- Update docs to state that `className` and DOM `nativeType` are not RN behavior.

## 8. Tests To Add

- Renders `type="primary"`, `type="default"`, and `type="danger"` without crashing.
- Supports `size="xlarge"`, `size="large"`, `size="normal"`, `size="small"`, and `size="mini"`.
- Does not call `onPress` or `onClick` when `disabled`.
- Does not call `onPress` or `onClick` when `loading`.
- Calls both `onPress` and `onClick` when enabled and pressed.
- Renders `icon` and `rightIcon`.
- Exposes `accessibilityRole="button"` after Pressable migration.
- Applies `textStyle` only to text content.
