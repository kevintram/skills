# DialKit timelines

Read this when tuning the timing of an animation sequence. For property tracks, groups, and loops, read the [timeline guide](https://github.com/joshpuckett/dialkit/blob/main/docs/timeline.md).

## Animation timelines

Use a timeline when tuning the timing of an animation sequence. Define named clips with at, duration, from, to, and transition, then bind each clip's current values to the interface so it follows the playhead.

```tsx
'use client'

import { DialTimeline, useDialTimeline } from 'dialkit'
import 'dialkit/styles.css'

export default function Card() {
  const timeline = useDialTimeline('Card', {
    enter: {
      at: 0,
      duration: 0.6,
      from: { y: 24, opacity: 0 },
      to: { y: 0, opacity: 1 },
      transition: { type: 'spring', bounce: 0.2 },
    },
  })

  return (
    <>
      <div style={{
        opacity: timeline.enter.current.opacity,
        transform: 'translateY(' + timeline.enter.current.y + 'px)',
      }}>
        Card
      </div>
      <DialTimeline />
    </>
  )
}
```

React and Vue use useDialTimeline. Solid, Svelte, and vanilla use createDialTimeline. Follow the adapter's reactive access pattern: timeline.enter.current in React and Svelte, timeline().enter.current in Solid, timeline.value.enter.current in Vue script, and timeline.values.enter.current or subscribe in vanilla.

Mount DialTimeline once, or createDialTimelineRoot() in vanilla. The timeline dock works independently of DialRoot. Use play(), pause(), replay(), and seek(seconds) to control playback. It supports scrubbing, moving and resizing clips, groups, loops, versions, and persistence.

After tuning, use Copy to transfer the chosen settings into the production animation. Replace sampled current bindings before removing the timeline; hiding the dock alone leaves them running. See the timeline guide for the full API.

