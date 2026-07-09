---
name: stylex
description: Implement, review, or improve features that use stylex. Use when the project uses stylex CSS. 
---

# Stylex 

Use this skill to build, review, or improve code that uses StyleX CSS.

## Workflow

1. Inspect the project before editing:
   - Check package scripts, build tooling, and existing StyleX configuration.
   - Search for current usage with `rg "stylex|@stylexjs|defineVars|defineConsts|createTheme"`.
   - Prefer the project's existing import, file naming, token, and component patterns.

2. Load the relevant references:
   - Read `references/stylex-authoring.md` for component styles, tokens, themes, type usage, selectors, animations, or code review.
   - Read `references/stylex-installation.md` for dependency, bundler, Next.js, Vite, PostCSS, ESLint, or build setup work.
   - Read both when adding StyleX to a project or diagnosing missing styles.

3. Keep implementation StyleX-native:
   - Use `stylex.create()` for styles and `stylex.props()` to apply them.
   - Use longhand CSS properties and nested conditional values with `default`.
   - Use `defineConsts()` for static shared values and `defineVars()` only for themeable values in `.stylex.ts` or `.stylex.js` files.
   - Avoid mixing `style`, `className`, and `stylex.props()` on the same element unless the existing project has a deliberate compatibility pattern.

4. Verify changes:
   - Run the closest available project checks, such as lint, typecheck, build, or targeted tests.
   - For UI changes, run the app or capture a browser/simulator check when feasible.
   - If setup guidance depends on a newer StyleX release than the local dependency, verify against the installed package docs or official docs before editing.

## Resources

- Authoring guide: `references/stylex-authoring.md`
- Installation guide: `references/stylex-installation.md`
