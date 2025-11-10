# Token Studio Plugin Setup Instructions

This guide explains how to configure Token Studio plugin in Figma to see all tokens (semantic light, semantic dark, shadcn light, shadcn dark) with all expected tokens.

## Prerequisites

- Token Studio Pro license (required for Themes feature)
- Figma with Token Studio plugin installed

## Step 1: Configure Token Sets Status

1. Open **Token Studio** plugin in Figma
2. Go to the **Tokens** page
3. For each token set, configure the status using the icon next to the set name:

   - **`primitive`**: Set as **"Set as Source"** (solid circle icon)
     - This makes tokens available for aliasing but prevents direct application
     - Right-click the set → Select "Set as Source"
   
   - **`semantic/mode-light`**: **Enabled** (checkmark icon)
   - **`semantic/mode-dark`**: **Enabled** (checkmark icon)
   - **`semantic/shadcn-light`**: **Enabled** (checkmark icon)
   - **`semantic/shadcn-dark`**: **Enabled** (checkmark icon)
   - **`component`**: **Enabled** (checkmark icon)

## Step 2: Create Theme Groups (Pro Feature Required)

Themes allow you to switch between different token configurations with a single click. We'll create two theme groups: "Mode" for semantic tokens and "ShadCN" for ShadCN tokens.

### Theme Group 1: "Mode" (for semantic tokens)

#### Create "light" theme:

1. Click the **Themes** dropdown at the top of the Tokens page
2. Select **"Manage Themes"**
3. Click **"New Theme"**
4. In the **"Theme Group"** input, enter: `Mode`
5. In the **"Theme"** input, enter: `light`
6. In the token set status section below, configure:
   - ✅ `primitive` → **Set as Source** (solid circle)
   - ✅ `semantic/mode-light` → **Enabled** (checkmark)
   - ✅ `component` → **Enabled** (checkmark)
   - ❌ `semantic/mode-dark` → **Disabled** (X icon)
   - ❌ `semantic/shadcn-light` → **Disabled** (X icon)
   - ❌ `semantic/shadcn-dark` → **Disabled** (X icon)
7. Click **"Save theme"**

#### Create "dark" theme:

1. Click **"New Theme"** again
2. In the **"Theme Group"** dropdown, select: `Mode` (the group you just created)
3. In the **"Theme"** input, enter: `dark`
4. In the token set status section, configure:
   - ✅ `primitive` → **Set as Source** (solid circle)
   - ✅ `semantic/mode-light` → **Enabled** (checkmark) - **Keep enabled for inheritance**
   - ✅ `semantic/mode-dark` → **Enabled** (checkmark) - **Overrides light mode colors**
   - ✅ `component` → **Enabled** (checkmark)
   - ❌ `semantic/shadcn-light` → **Disabled** (X icon)
   - ❌ `semantic/shadcn-dark` → **Disabled** (X icon)
5. Click **"Save theme"**

**Why both mode-light and mode-dark are enabled in dark theme:**
- `semantic/mode-light` provides all non-color tokens (typography, dimensions, etc.)
- `semantic/mode-dark` provides color overrides
- Token Studio uses "last set wins" logic, so dark mode colors override light mode colors
- Non-color tokens come from light mode since they don't exist in dark mode

### Theme Group 2: "ShadCN" (for ShadCN tokens)

#### Create "light" theme:

1. Click **"New Theme"**
2. In the **"Theme Group"** input, enter: `ShadCN` (new group)
3. In the **"Theme"** input, enter: `light`
4. In the token set status section, configure:
   - ✅ `primitive` → **Set as Source** (solid circle)
   - ✅ `semantic/mode-light` → **Enabled** (checkmark) - **Needed for semantic references**
   - ✅ `semantic/shadcn-light` → **Enabled** (checkmark)
   - ✅ `component` → **Enabled** (checkmark)
   - ❌ `semantic/mode-dark` → **Disabled** (X icon)
   - ❌ `semantic/shadcn-dark` → **Disabled** (X icon)
5. Click **"Save theme"**

#### Create "dark" theme:

1. Click **"New Theme"** again
2. In the **"Theme Group"** dropdown, select: `ShadCN` (the group you just created)
3. In the **"Theme"** input, enter: `dark`
4. In the token set status section, configure:
   - ✅ `primitive` → **Set as Source** (solid circle)
   - ✅ `semantic/mode-light` → **Enabled** (checkmark) - **Needed for semantic references**
   - ✅ `semantic/mode-dark` → **Enabled** (checkmark) - **Needed for dark semantic references**
   - ✅ `semantic/shadcn-light` → **Enabled** (checkmark) - **Provides non-color tokens**
   - ✅ `semantic/shadcn-dark` → **Enabled** (checkmark) - **Overrides color tokens**
   - ✅ `component` → **Enabled** (checkmark)
5. Click **"Save theme"**

**Why multiple sets are enabled in ShadCN dark theme:**
- `semantic/mode-light` and `semantic/mode-dark` provide the semantic tokens that ShadCN tokens reference
- `semantic/shadcn-light` provides all ShadCN tokens (color + non-color)
- `semantic/shadcn-dark` provides only color overrides
- Token Studio uses "last set wins", so dark mode ShadCN colors override light mode colors
- Non-color ShadCN tokens (font-*, radius, shadow-*) come from `semantic/shadcn-light`

## Step 3: Switch Between Themes

Use the **Themes** dropdown at the top of the Tokens page to switch between themes:

- **`Mode > light`**: Shows semantic light mode tokens
  - All tokens from `semantic/mode-light`
  - Component tokens

- **`Mode > dark`**: Shows semantic dark mode tokens
  - Color tokens from `semantic/mode-dark` (override light mode)
  - Non-color tokens from `semantic/mode-light` (inherited)
  - Component tokens

- **`ShadCN > light`**: Shows ShadCN light mode tokens
  - All tokens from `semantic/shadcn-light`
  - Semantic references resolve to `semantic/mode-light`
  - Component tokens

- **`ShadCN > dark`**: Shows ShadCN dark mode tokens
  - Color tokens from `semantic/shadcn-dark` (override light mode)
  - Non-color tokens from `semantic/shadcn-light` (inherited)
  - Semantic color references resolve to `semantic/mode-dark`
  - Semantic non-color references resolve to `semantic/mode-light`
  - Component tokens

## Expected Result

After setup, you should see:

✅ All token sets visible in the sidebar
✅ No broken token references (no orange broken link icons)
✅ Color swatches display correctly
✅ Theme switching works smoothly
✅ All tokens resolve correctly when switching themes

## Troubleshooting

### Broken token references (orange broken link icon)
- Verify all token sets are enabled as specified above
- Check that `primitive` is set as "Set as Source"
- Ensure the correct token sets are enabled for each theme

### Colors not displaying
- Verify color token `$value` is a hex string (not an object)
- Check that OKLCH data is in `$extensions.oklch` (not in `$value`)

### Themes not appearing
- Verify you have Token Studio Pro license
- Check that `$metadata.tokenSetOrder` exists in the JSON file
- Ensure theme groups and themes are created as specified above

### Wrong tokens showing in dark theme
- Verify both light and dark token sets are enabled in dark theme
- Check token set order (dark set should be after light set in the enabled list)

## Reference

- [Token Studio Themes Documentation](https://docs.tokens.studio/manage-themes/simple-switch-guide)
- [Token Sets Status Guide](https://docs.tokens.studio/manage-tokens/token-sets)

