---
name: dialkit
description: Add or update DialKit controls in web apps, tune animation timelines, apply values copied from DialKit, and remove DialKit before shipping. Use when the user asks to work with DialKit for layout, appearance, springs, easing, or animation timing.
disable-model-invocation: true
---

# DialKit

> Live controls for tuning interfaces. Adjust values, compare versions, and edit animation timing in your running app.

DialKit is an open-source library created by [Josh Puckett](https://joshpuckett.me), author of [Interface Craft](https://interfacecraft.dev/). It is available under the MIT license.

- Website: https://dialkit.dev
- Package: dialkit (npm)
- Source and README: https://github.com/joshpuckett/dialkit
- API reference: https://github.com/joshpuckett/dialkit/blob/main/docs/reference.md
- Timeline guide: https://github.com/joshpuckett/dialkit/blob/main/docs/timeline.md
- Interactive demo: https://dialkit.dev/photostack

## What it is for

DialKit helps designers, developers, and coding agents make interface details feel right. Put live controls beside the interface you are building, connect them to real values in your code, and tune the result by eye instead of repeatedly editing constants and reloading.

Use it to adjust:

- Layout: spacing, padding, dimensions, grid gaps, and corner radii.
- Appearance: colors, shadows, opacity, images, and typography.
- Motion: spring duration, bounce, easing curves, scale, and position.
- Animation sequences: clip timing, overlapping animations, and individual property tracks.
- Alternatives: save versions, compare them, and copy the chosen values back into source code.

DialKit supplies the controls and live values. Your component uses those values in its styles, animation library, or rendering code. It can be added to an existing interface or used while building a new one.

## Workflow for coding agents

1. Inspect the project to identify its framework, package manager, and existing component conventions.
2. Read the DialKit README and API reference for the installed version before using an unfamiliar API.
3. Install the matching adapter dependencies in [references/setup.md](references/setup.md), using the project's package manager.
4. Mount one DialKit root and load the adapter's styles. In Next.js App Router, put interactive DialKit code in a client component.
5. Add a small, useful set of controls to the component being tuned. Start with its existing values and choose sensible ranges and steps.
6. Bind the returned live values to the actual styles or animation properties. Preserve the framework's reactivity rules.
7. Group related controls in folders. Add a replay action when an animation needs to be triggered again.
8. Let the user tune and compare versions. When given copied DialKit values, apply them to the corresponding defaults or production code.
9. Check the interface and run the project's build or relevant checks.

For step 5, see [Choosing controls](#choosing-controls) and [references/controls.md](references/controls.md); for animation sequences, see [references/timeline.md](references/timeline.md). For step 8, see [Applying copied values](#applying-copied-values). When the user is done tuning and wants DialKit gone, follow [Removing DialKit](#removing-dialkit).

## References

- [references/setup.md](references/setup.md): installation and basic usage for React, Solid, Svelte, Vue, and plain JavaScript; root placement and production behavior.
- [references/controls.md](references/controls.md): control definitions, return types, panel options, saved versions, and controllers.
- [references/timeline.md](references/timeline.md): animation timelines, clips, and playback.

## Choosing controls

Expose the parameters the user is actually trying to judge, not every value in the component.

- Keep a panel to roughly 3–8 controls. If more are needed, group them in folders and collapse the secondary ones with `_collapsed: true`.
- Default every control to the component's current value, so the first render looks unchanged.
- Pick ranges that bracket plausible choices rather than every legal value: radius 0–64, spacing 0–48 in steps of 2 or 4, opacity 0–1 in steps of 0.01, scale 0.8–1.2 in steps of 0.01, blur 0–40, durations 0.1–1 seconds.
- Use integer steps for counts such as columns or items, and fractional steps for opacity, scale, and timing.
- Use a select for discrete alternatives such as layout variants, a boolean for on/off states, and a color control for colors instead of separate channel sliders.
- Use a spring or easing control for a transition instead of separate duration and curve sliders. Their returned transition can switch modes, so don't assume it always remains a spring.
- Add a replay action for anything that only animates on mount or on an event, and make it restart the real animation.
- Give each panel a stable `id` and a name that matches the component. Avoid sharing IDs between unrelated panels.

## Applying copied values

The panel's Copy button puts the current values and an instruction for applying them on the clipboard. The timeline's Copy exports tuned timings, transitions, and values with a production handoff comment. Copying does not edit source files; that is this step.

1. Match each copied value to its control by path, including folder nesting such as `shadow.blur`.
2. Update the defaults in the DialKit config. For a slider tuple, change only the first element and keep its min, max, and step; widen the bounds only when the new value falls outside them.
3. If the values belong in production code rather than the config, update those constants or animation settings instead.
4. Leave controls the user did not change as they are, and point out any copied value that has no matching control.
5. Check the interface reflects the new defaults after a reload. With `persist: true`, values saved under `dialkit:${id}` may mask the new defaults; reset the panel (or call `resetValues()`) or clear that storage key before judging.

## Removing DialKit

Hiding the editor in production does not remove the code consuming live values, and hiding the timeline dock leaves its animation running. To remove DialKit from a component:

1. Apply the final values first, following [Applying copied values](#applying-copied-values).
2. Replace each live value binding with a literal constant or the production style. Keep the same units and transforms.
3. Replace timeline `current` bindings with the production animation code, using the copied timings and transitions.
4. Remove the panel and timeline hooks or factory calls, action handlers, and controller usage. In vanilla, remove the `destroy()` calls with them.
5. If no DialKit usage remains in the app, remove `DialRoot`, `DialTimeline`, the stylesheet imports, and the `dialkit` dependency. Keep `motion` or `motion-v` if the app uses them elsewhere.
6. Search the project for remaining `dialkit` imports, then run the build and relevant checks and confirm the interface looks the same as the tuned version.

## Example requests

- Add DialKit to this hover animation so I can tune spring duration, bounce, scale, and shadow blur.
- Add sliders for this grid's gap, padding, column count, and card radius. Group card controls in a folder.
- Add a timeline for this card entrance so I can scrub and adjust when the text and image appear.
- Apply these copied DialKit values to the component's defaults.
- Remove DialKit from this component and keep the values I tuned.

## Further reading

- [README and quick start](https://github.com/joshpuckett/dialkit#readme)
- [Full API reference](https://github.com/joshpuckett/dialkit/blob/main/docs/reference.md)
- [Timeline guide](https://github.com/joshpuckett/dialkit/blob/main/docs/timeline.md)
- [Plain JavaScript example](https://github.com/joshpuckett/dialkit/tree/main/examples/vanilla)
- [Report an issue](https://github.com/joshpuckett/dialkit/issues)
- [MIT license](https://github.com/joshpuckett/dialkit/blob/main/LICENSE)
