# DialKit setup

Read this when installing DialKit or mounting its root. It covers each framework adapter, root placement, and production behavior.

## Installation and basic usage

Supported frameworks: React 18+, Solid 1.6+, Svelte 5.8+, Vue 3.3+, and plain JavaScript. The vanilla adapter has no runtime dependencies.

The examples below use npm. Use the equivalent add command for pnpm, Yarn, or Bun when that is the project's package manager.

### React

```bash
npm install dialkit motion
```

Mount one DialRoot and use the useDialKit hook to bind live values to your interface. In Next.js App Router, use a client component.

```tsx
'use client'

import { DialRoot, useDialKit } from 'dialkit'
import 'dialkit/styles.css'

export default function App() {
  const values = useDialKit('Card', {
    radius: [24, 0, 64],
    color: '#a78bfa',
  })

  return (
    <>
      <div style={{
        borderRadius: values.radius,
        background: values.color,
      }}>
        Card
      </div>
      <DialRoot />
    </>
  )
}
```

### Solid

```bash
npm install dialkit motion
```

Mount one DialRoot and call createDialKit in your component. It returns an accessor: read live values with values().radius.

```tsx
import { createDialKit, DialRoot } from 'dialkit/solid'
import 'dialkit/styles.css'

export default function App() {
  const values = createDialKit('Card', {
    radius: [24, 0, 64],
  })

  return (
    <>
      <div style={{ 'border-radius': values().radius + 'px' }}>Card</div>
      <DialRoot />
    </>
  )
}
```

### Svelte

```bash
npm install dialkit
```

Read live values directly from the reactive object. DialRoot injects the styles automatically, so there’s no CSS import or extra dependency.

```svelte
<script>
  import { createDialKit, DialRoot } from 'dialkit/svelte'

  const values = createDialKit('Card', {
    radius: [24, 0, 64],
  })
</script>

<div style:border-radius={values.radius + 'px'}>Card</div>
<DialRoot />
```

### Vue

```bash
npm install dialkit motion motion-v
```

Mount one DialRoot and use the useDialKit hook. Values are a computed ref: read values.value.radius in script; templates unwrap it automatically.

```vue
<script setup>
import { DialRoot, useDialKit } from 'dialkit/vue'
import 'dialkit/styles.css'

const values = useDialKit('Card', {
  radius: [24, 0, 64],
})
</script>

<template>
  <div :style="{ borderRadius: values.radius + 'px' }">Card</div>
  <DialRoot />
</template>
```

### JavaScript

```bash
npm install dialkit
```

No framework or runtime dependencies. Add an element with id="card" to your page, then subscribe to apply values immediately and whenever they change.

```js
import { createDialKit, createDialRoot } from 'dialkit/vanilla'
import 'dialkit/vanilla/styles.css'

const card = document.querySelector('#card')
const root = createDialRoot()
const kit = createDialKit('Card', {
  radius: [24, 0, 64],
})

kit.subscribe(values => {
  card.style.borderRadius = values.radius + 'px'
})

// When removing the controls from the page:
// kit.destroy()
// root.destroy()
```

## Root placement and production behavior

Mount one DialRoot for the app's control panels. The root supports position ('top-right', 'top-left', 'bottom-right', or 'bottom-left'), theme ('system', 'light', or 'dark'), and mode ('popover' or 'inline').

```tsx
<DialRoot position="top-right" theme="dark" />
```

Framework roots are hidden in production unless productionEnabled is true. Vanilla roots are visible by default; pass productionEnabled: false to hide them. This setting controls the editor UI; it does not remove the code consuming live values.

In vanilla, call kit.destroy() and root.destroy() when removing their controls. Keep the root and controls on the same adapter entry so they share a store.

