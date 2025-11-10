# Fix Semantic Mode Token Sets for Token Studio

## Problem
Token Studio is not recognizing `semantic.mode-light` and `semantic.mode-dark` as separate themes because they are nested under the `semantic` object. Token Studio requires theme token sets to be at the **top level** of the JSON file.

## Current Structure (Incorrect)
```json
{
  "primitive": { ... },
  "semantic": {
    "mode-light": { ... },
    "mode-dark": { ... }
  },
  "component": { ... }
}
```

## Reference Structure (Correct)
The reference file (`tokens-scales-starter.json`) uses:
- `alias/mode/color-light` as a **top-level key**
- `alias/mode/color-dark` as a **top-level key**

Each contains a wrapper object (e.g., `"alias": { "color": { ... } }`) that creates the token path.

## Required Structure (For Token Studio)
```json
{
  "primitive": { ... },
  "semantic/mode-light": {
    "semantic": {
      "color": { ... },
      "border": { ... },
      "font": { ... },
      "typescale": { ... }
    }
  },
  "semantic/mode-dark": {
    "semantic": {
      "$extends": "{semantic/mode-light}",
      "color": { ... }  // Only overrides
    }
  },
  "component": { ... },
  "$tokenSetOrder": [ ... ]
}
```

## Implementation Steps

1. **Extract `semantic.mode-light` to top-level `semantic/mode-light`**
   - Move `semantic.mode-light` content to a new top-level key `semantic/mode-light`
   - Wrap content in a `semantic` object to maintain token paths (e.g., `semantic.color.background.body`)
   - Location: Currently at `semantic.mode-light`, move to top-level `semantic/mode-light`

2. **Extract `semantic.mode-dark` to top-level `semantic/mode-dark`**
   - Move `semantic.mode-dark` content to a new top-level key `semantic/mode-dark`
   - Wrap content in a `semantic` object
   - Preserve `$extends: "{semantic/mode-light}"` reference
   - Location: Currently at `semantic.mode-dark`, move to top-level `semantic/mode-dark`

3. **Remove empty `semantic` object**
   - After extracting mode-light and mode-dark, remove the now-empty `semantic` object
   - Or keep it if there are other semantic tokens (but there aren't currently)

4. **Update token references**
   - Update any references from `{semantic.mode-light.*}` to `{semantic.*}` (since the wrapper creates the path)
   - Update any references from `{semantic.mode-dark.*}` to `{semantic.*}`
   - The `$extends` reference should remain as `{semantic/mode-light}` (token set reference)

5. **Add `$tokenSetOrder` array**
   - Add a `$tokenSetOrder` array at the end of the file listing all token sets
   - Include: `primitive`, `semantic/mode-light`, `semantic/mode-dark`, `component`
   - This helps Token Studio understand the token set structure

6. **Verify token paths**
   - Ensure token paths like `semantic.color.background.body` work correctly
   - The wrapper structure `semantic/mode-light: { semantic: { color: { ... } } }` creates paths like `semantic.color.*`
   - This matches how the reference file works with `alias/mode/color-light: { alias: { color: { ... } } }` creating `alias.color.*`

## Files to Modify
- `/Users/dave.calleja/repos/focus-tokens/focus-tokens.json`
  - Lines 2044-3290: `semantic` section (extract mode-light and mode-dark)
  - End of file: Add `$tokenSetOrder` array

## Key Points
- Token Studio requires **top-level token sets** for theme switching
- Use slash notation (`semantic/mode-light`) for token set names
- Wrap content in a namespace object (`semantic`) to create correct token paths
- Maintain `$extends` for dark mode inheritance
- Add `$tokenSetOrder` for Token Studio metadata


