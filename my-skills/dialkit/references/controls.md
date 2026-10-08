# DialKit controls

Read this when defining controls, panel options, saved versions, or controllers.

## Defining controls

Define controls with an object. Keys become labels, and returned values preserve the same nesting. Slider tuples are [default, min, max, step?].

```ts
const config = {
  radius: [24, 0, 64],
  gap: [16, 0, 48, 2],
  visible: true,
  title: 'Hello',
  accent: '#a78bfa',
  layout: { type: 'select', options: ['stack', 'grid'] },
  cover: { type: 'image', options: ['/cover.jpg'] },
  position: { type: 'pad' },
  spring: { type: 'spring', visualDuration: 0.3, bounce: 0.2 },
  easing: { type: 'easing', duration: 0.3, ease: [0.25, 0.1, 0.25, 1] },
  replay: { type: 'action' },
  shadow: {
    _collapsed: true,
    blur: [12, 0, 40],
    opacity: [0.15, 0, 1, 0.01],
  },
}
```

- Numbers and slider tuples return numbers; booleans return booleans.
- Text, colors, selects, and images return strings. A pad returns an object with x and y values.
- A nested object creates a folder; read nested values such as values.shadow.blur.
- _collapsed is folder metadata and is omitted from returned values.
- Spring and easing controls return transition configurations. Bind them to the animation you are tuning.
- Actions call the panel's onAction callback with the control path, such as 'replay'.
- For a separate TypeScript config, use satisfies DialConfig with the type imported from your adapter to retain useful inference.

## Panel options and saved versions

React and Vue use useDialKit(name, config, options). Solid, Svelte, and vanilla use createDialKit(name, config, options).

Common options:

- id: a stable panel ID for sharing values across mounts.
- persist: save values and versions in browser storage; defaults to false.
- defaultCollapsed: set to true to start a panel closed.
- onAction: handle an action button's control path.
- shortcuts: assign keyboard shortcuts by control path.

For example, in React:

```tsx
const values = useDialKit('Card', {
  radius: [24, 0, 64],
  replay: { type: 'action' },
}, {
  id: 'card',
  persist: true,
  onAction: (path) => {
    if (path === 'replay') replayAnimation()
  },
})
```

Here replayAnimation is your component's animation restart function.

The panel's + button saves a version. Edits update the selected version. Copy puts current values and an instruction for applying them to the config on the clipboard. Copying values does not edit source files automatically.

For updates from code, use useDialKitController in React or Vue, or createDialKitController in Solid or Svelte. The vanilla createDialKit function already returns a controller. Controllers support setValue, setValues, resetValues, and setOpen.

