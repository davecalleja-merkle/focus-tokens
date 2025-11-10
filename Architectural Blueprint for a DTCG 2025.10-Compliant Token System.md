

# **Architectural Blueprint for a DTCG 2025.10-Compliant Primitive Token System**

## **Section 1: Foundational Architecture: The Three-Tier Token Model**

### **1.1. The Three Tiers of Tokenization**

A scalable, modern design system organizes tokens into a three-tiered architecture. This model is essential for managing complexity, enabling theming, and ensuring a clear separation of concerns. This structure is a recognized industry best practice, utilized by mature design systems such as Microsoft's Fluent 2 1 and advocated by frameworks like Terrazzo.4

1. **Tier 1: Primitives (Global/Base):** This is the foundational layer and the single source of truth for all raw, non-semantic values in the system.5 These tokens are context-agnostic; their names describe the value itself (e.g., color.blue.500, space.100), not its application.7 They represent the complete, available "palette" of design options.  
2. **Tier 2: Semantics (Alias/Purpose):** This layer provides context and intent.8 Semantic tokens do not contain raw values; instead, they *alias* (reference) tokens from the primitive tier. Their names describe their *purpose* or *role* in the UI (e.g., color.background.interactive.default might alias "{primitive.color.brand.primary.500}").4 This layer is where design decisions and theming logic are encoded.10  
3. **Tier 3: Components (Specific):** This optional, highly specific tier applies tokens to a single component, often as a scoped override. However, as noted in the Terrazzo style guide, this layer can exponentially increase token complexity and should be avoided where possible by building more robust semantic logic.4

This architecture will focus on a robust Tier 1 (Primitives) and a flexible Tier 2 (Semantics), establishing the flexible foundation requested.

### **1.2. The Role of Primitives as the "Source of Truth"**

The primitive tier functions as the central, canonical source of all available style values.5 These tokens represent the raw ingredients, not the final recipe.11 The core principle of this architecture is the abstraction enabled by aliasing.

By building the entire semantic layer from aliases that reference the primitive layer, the system gains immense flexibility. This separation of "value" (primitive) from "role" (semantic) allows for sweeping, system-wide changes to be made with minimal effort.

For example, to implement a dark mode, the semantic token color.background.surface.default would simply change its alias from "{primitive.color.utility.neutral.100}" (in the light theme) to "{primitive.color.utility.neutral.900}" (in the dark theme). The primitive tokens themselves, and the components that consume the semantic token, remain unchanged. This abstraction is the fundamental mechanism that enables theming, multi-brand support, and long-term maintainability.12

### **1.3. Tooling Primitives: Tokens Studio Setup**

This three-tier architecture maps directly to the "Token Sets" paradigm within the Tokens Studio plugin.13 To correctly implement this model, a specific setup is required:

1. All primitive tokens defined in this report (for color, space, typography, etc.) should be consolidated into a single Token Set (or a folder of sets), for example, primitives.json.  
2. This primitives set must be marked as **"Set as Source"** within the Tokens Studio UI.15  
3. Setting this status makes all tokens within primitives available for aliasing (e.g., for use in semantic-light and semantic-dark sets) but prevents them from being directly applied to designs. This tooling-level constraint enforces the correct architecture, compelling designers to use the semantic layer and preventing the direct application of raw, non-semantic values.

## **Section 2: DTCG 2025.10 Schema: Grouping Primitive Tokens**

### **2.1. The Anatomy of a DTCG Token Group**

The Design Tokens Community Group (DTCG) 2025.10 specification defines a group as any JSON object that does *not* contain a $value property.16 Groups are the primary mechanism for organizing tokens and creating the token's path. For instance, the token primitive.color.brand.primary.500 is defined by a 500 token nested within a primary group, which is nested within a brand group, and so on.

Groups can contain several reserved properties (prefixed with $) to add metadata and functionality 16:

* $description: A plain string describing the group's purpose. This is highly recommended for documentation.  
* $type: A powerful feature that establishes a default $type for all child tokens within the group that do not declare their own. For example, setting "$type": "color" on the primitive.color group will automatically type all nested color tokens.  
* $extends: The official DTCG 2025.10 mechanism for theming, which allows one group to inherit and override tokens from another.20 This is detailed further in Section 8\.  
* $deprecated: A boolean or string value to mark a group as deprecated, which aids in managing the token lifecycle.16

### **2.2. Recommended Primitive Group Structure & Naming**

The primitive layer should be organized by category. A single primitive root group will contain all base token categories.

For naming conventions, the requested "Ordered Scales" (DTCG 7.2.1.2.1) strategy will be applied. This approach uses a numerical scale (e.g., 50, 100, 200...900) for any value that exists on a ramp. This is a well-established best practice seen in systems like Microsoft Fluent (e.g., Global.Color.Blue.60 2) and Contentful's Forma 36\.21

This numerical, ordered scale is inherently non-semantic and provides a clear, unambiguous set of options, superior to abstract names like "small" or "large".7 This scaling methodology will be applied consistently across all relevant primitive types, including color, space, size, radius, font.size, and line-height, to create a unified and predictable "scale language" for the entire system.

### **2.3. Example Primitive JSON Structure (Root Level)**

The following JSON object demonstrates the recommended root-level grouping for the primitive set. Subsequent sections will provide the specific values for each group.

JSON

{  
  "primitive": {  
    "$description": "Base primitive tokens. The non-semantic, context-agnostic source of truth for all raw style values.",  
    "color": {  
      "$description": "Base color palette. Defined in OKLCH with sRGB-clamped hex fallbacks for design tools.",  
      "$type": "color",  
      "brand": {  
        "primary": {  
          "100": { /\*... $value object... \*/ },  
          "200": { /\*... $value object... \*/ },  
          /\*... 9-step scale... \*/  
          "900": { /\*... $value object... \*/ }  
        },  
        "secondary": {  
          "100": { /\*... $value object... \*/ },  
          /\*... 9-step scale... \*/  
          "900": { /\*... $value object... \*/ }  
        },  
        "accent": {  
          "100": { /\*... $value object... \*/ },  
          /\*... 9-step scale... \*/  
          "900": { /\*... $value object... \*/ }  
        }  
      },  
      "utility": {  
        "success": {  
          "100": { /\*... $value object... \*/ },  
          /\*... 9-step scale... \*/  
          "900": { /\*... $value object... \*/ }  
        },  
        "critical": {  
          "100": { /\*... $value object... \*/ },  
          /\*... 9-step scale... \*/  
          "900": { /\*... $value object... \*/ }  
        },  
        "warning": {  
          "100": { /\*... $value object... \*/ },  
          /\*... 9-step scale... \*/  
          "900": { /\*... $value object... \*/ }  
        },  
        "info": {  
          "100": { /\*... $value object... \*/ },  
          /\*... 9-step scale... \*/  
          "900": { /\*... $value object... \*/ }  
        },  
        "neutral": {  
          "100": { /\*... $value object... \*/ },  
          /\*... 9-step scale... \*/  
          "900": { /\*... $value object... \*/ }  
        },  
        "transparent": {  
          "white-10": { /\*... $value object... \*/ },  
          "black-10": { /\*... $value object... \*/ }  
          /\*... other opacities... \*/  
        }  
      }  
    },  
    "space": {  
      "$description": "Base dimension scale for spacing, padding, margins. Based on a 4px grid.",  
      "$type": "dimension",  
      "0":   { "$value": { "value": 0, "unit": "px" } },  
      "100": { "$value": { "value": 4, "unit": "px" } },  
      "200": { "$value": { "value": 8, "unit": "px" } },  
      "300": { "$value": { "value": 12, "unit": "px" } },  
      "400": { "$value": { "value": 16, "unit": "px" } }  
      /\*... scale continues... \*/  
    },  
    "breakpoint": {  
      "$description": "Dimension tokens for responsive design breakpoints.",  
      "$type": "dimension",  
      "sm": { "$value": { "value": 576, "unit": "px" } },  
      "md": { "$value": { "value": 768, "unit": "px" } },  
      "lg": { "$value": { "value": 992, "unit": "px" } },  
      "xl": { "$value": { "value": 1200, "unit": "px" } }  
    },  
    "size": {  
      "$description": "Base dimension scale for sizing (e.g., width, height). Aliases the 'space' scale for consistency.",  
      "100": { "$value": "{primitive.space.100}" },  
      "200": { "$value": "{primitive.space.200}" }  
      /\*... scale continues... \*/  
    },  
    "radius": {  
      "$description": "Base dimension scale for border-radius.",  
      "$type": "dimension",  
      "100": { "$value": { "value": 4, "unit": "px" } },  
      "200": { "$value": { "value": 8, "unit": "px" } },  
      "300": { "$value": { "value": 16, "unit": "px" } },  
      "full": { "$value": { "value": 9999, "unit": "px" } }  
    },  
    "font": {  
      "family": {  
        "$type": "fontFamily",  
        "sans": { "$value": \["Inter", "system-ui", "sans-serif"\] },  
        "serif": { "$value": },  
        "mono": { "$value": }  
      },  
      "weight": {  
        "$type": "fontWeight",  
        "300": { "$value": 300 },  
        "400": { "$value": 400 },  
        "500": { "$value": 500 },  
        "700": { "$value": 700 }  
      },  
      "size": {  
        "$type": "dimension",  
        "100": { "$value": { "value": 12, "unit": "px" } },  
        "200": { "$value": { "value": 14, "unit": "px" } },  
        "300": { "$value": { "value": 16, "unit": "px" } }  
        /\*... scale continues... \*/  
      }  
    },  
    "line-height": {  
      "$description": "Unitless line-height scale.",  
      "$type": "number",  
      "100": { "$value": 1.25 },  
      "200": { "$value": 1.5 },  
      "300": { "$value": 1.75 }  
    },  
    "transition": {  
      "duration": {  
        "$type": "duration",  
        "100": { "$value": "100ms" },  
        "200": { "$value": "200ms" },  
        "300": { "$value": "300ms" }  
      },  
      "ease": {  
        "$type": "cubicBezier",  
        "linear": { "$value":  },  
        "in-out": { "$value": \[0.42, 0, 0.58, 1\] }  
      }  
    },  
    "opacity": {  
      "$type": "number",  
      "50": { "$value": 0.5 },  
      "100": { "$value": 1 }  
    }  
  }  
}

## **Section 3: Complete DTCG 2025.10 Primitive Token Specification**

This section provides the definitive specification and JSON structure for every primitive token type (excluding color, which is detailed in Section 4\) as defined by the DTCG 2025.10 specification.22 Adhering to these standard types is critical for ensuring that transformation tools like Style Dictionary and platforms like iOS and Android can natively understand and interpret the token data.

**Table 1: DTCG 2025.10 Primitive Token Types**

| $type | $value Structure | Example JSON Token |
| :---- | :---- | :---- |
| **dimension** | An object with value (number) and unit (string: "px" or "rem"). | "100": { "$type": "dimension", "$value": { "value": 4, "unit": "px" } } |
| **fontFamily** | A string or an array of strings (for fallbacks). | "sans": { "$type": "fontFamily", "$value": \["Inter", "Helvetica", "sans-serif"\] } |
| **fontWeight** | A number (1-999) or a string alias (e.g., "regular", "bold"). *Recommendation:* Use numbers. | "400": { "$type": "fontWeight", "$value": 400 } |
| **duration** | A string with a time unit (ms or s). | "fast": { "$type": "duration", "$value": "150ms" } |
| **cubicBezier** | A 4-element array of numbers \[x1, y1, x2, y2\]. | "ease-in": { "$type": "cubicBezier", "$value": \[0.4, 0, 1, 1\] } |
| **number** | A plain number (integer or float). | "150": { "$type": "number", "$value": 1.5 } |
| **color** | (See Section 4\) | (See Section 4\) |

---

### **3.1. dimension**

* **DTCG Specification:** Represents a one-dimensional distance value, used for properties like spacing, sizing, border-radius, and border-width.22  
* **Value Structure:** The $value *must* be an object containing two keys: value (a number) and unit (a string).22  
* **Units Constraint:** The DTCG 2025.10 specification *only* sanctions "px" and "rem" as valid units.22 This is a strict constraint. Build tools are responsible for translating "px" to pt for iOS or dp for Android.  
* **Primitive Scale Example (for primitive.space):** This scale is based on a 4px grid, which provides fine-grained control for UI elements while still being compatible with 8-point grid systems.  
  JSON  
  "space": {  
    "$type": "dimension",  
    "$description": "Base spacing scale, 4px grid.",  
    "0":   { "$value": { "value": 0, "unit": "px" } },  
    "50":  { "$value": { "value": 2, "unit": "px" } },  
    "100": { "$value": { "value": 4, "unit": "px" } },  
    "200": { "$value": { "value": 8, "unit": "px" } },  
    "300": { "$value": { "value": 12, "unit": "px" } },  
    "400": { "$value": { "value": 16, "unit": "px" } },  
    "500": { "$value": { "value": 20, "unit": "px" } },  
    "600": { "$value": { "value": 24, "unit": "px" } },  
    "700": { "$value": { "value": 32, "unit": "px" } },  
    "800": { "$value": { "value": 48, "unit": "px" } },  
    "900": { "$value": { "value": 64, "unit": "px" } }  
  }

### **3.2. fontFamily**

* **DTCG Specification:** Represents a font family or a stack of font families.23  
* **Value Structure:** The $value can be a single string or, for best practice, an array of strings. The array format allows for defining a "font stack" with fallbacks, from most-preferred to least-preferred.23  
* **Primitive Group Example (for primitive.font.family):**  
  JSON  
  "family": {  
    "$type": "fontFamily",  
    "sans": {  
      "$value":,  
      "$description": "Primary sans-serif font."  
    },  
    "serif": {  
      "$value":,  
      "$description": "Primary serif font."  
    },  
    "mono": {  
      "$value":,  
      "$description": "Primary monospace font."  
    }  
  }

### **3.3. fontWeight**

* **DTCG Specification:** Represents a font's weight.23  
* **Value Structure:** The $value can be a string alias (e.g., "regular", "bold"). However, using a number (from 1 to 999\) is the authoritative recommendation, as it maps directly to CSS font-weight, is unambiguous, and aligns with the "Ordered Scale" naming strategy.23  
* **Primitive Group Example (for primitive.font.weight):**  
  JSON  
  "weight": {  
    "$type": "fontWeight",  
    "300": { "$value": 300, "$description": "Light" },  
    "400": { "$value": 400, "$description": "Regular" },  
    "500": { "$value": 500, "$description": "Medium" },  
    "700": { "$value": 700, "$description": "Bold" }  
  }

### **3.4. duration**

* **DTCG Specification:** Represents a length of time, typically for animations and transitions.23  
* **Value Structure:** The $value is a string composed of a number and a unit, either ms (milliseconds) or s (seconds).23  
* **Primitive Group Example (for primitive.transition.duration):**  
  JSON  
  "duration": {  
    "$type": "duration",  
    "100": { "$value": "100ms", "$description": "Quickest (e.g., fade)" },  
    "200": { "$value": "200ms", "$description": "Quick (e.g., slide)" },  
    "300": { "$value": "300ms", "$description": "Default" },  
    "400": { "$value": "400ms", "$description": "Slow (e.g., modal)" }  
  }

### **3.5. cubicBezier**

* **DTCG Specification:** Represents a cubic Bézier easing function.23  
* **Value Structure:** The $value is a 4-element array of numbers, \[x1, y1, x2, y2\], which corresponds to the cubic-bezier(x1, y1, x2, y2) CSS function.23 Standard keyword values should also be stored by their explicit array values.  
* **Primitive Group Example (for primitive.transition.ease):**  
  JSON  
  "ease": {  
    "$type": "cubicBezier",  
    "linear":    { "$value": , "$description": "linear" },  
    "in":        { "$value": \[0.42, 0, 1, 1\], "$description": "ease-in" },  
    "out":       { "$value": \[0, 0, 0.58, 1\], "$description": "ease-out" },  
    "in-out":    { "$value": \[0.42, 0, 0.58, 1\], "$description": "ease-in-out" }  
  }

### **3.6. number**

* **DTCG Specification:** Represents a unitless number, which can be an integer or a float.23  
* **Purpose:** This type is distinct from dimension and is the correct choice for values that must *not* have units, such as line-height (where a unitless value scales with font-size) and opacity.  
* **Primitive Group Example (for primitive.line-height):**  
  JSON  
  "line-height": {  
    "$type": "number",  
    "100": { "$value": 1.25, "$description": "Tight (e.g., headings)" },  
    "200": { "$value": 1.5, "$description": "Default (e.g., body text)" },  
    "300": { "$value": 1.75, "$description": "Loose (e.g., lead)" }  
  }

### **3.7. breakpoint (Dimension)**

* **Purpose:** To provide a flexible and comprehensive set of primitives, the dimension type should also be used to define responsive breakpoints. Grouping them separately from space improves clarity.  
* **Naming:** Using T-shirt sizes (sm, md, lg) is a common and clear naming convention for breakpoints.  
* **Primitive Group Example (for primitive.breakpoint):**  
  JSON  
  "breakpoint": {  
    "$type": "dimension",  
    "$description": "Breakpoint values for responsive media queries.",  
    "sm": { "$value": { "value": 576, "unit": "px" } },  
    "md": { "$value": { "value": 768, "unit": "px" } },  
    "lg": { "$value": { "value": 992, "unit": "px" } },  
    "xl": { "$value": { "value": 1200, "unit": "px" } }  
  }

## **Section 4: Strategic Deep Dive: The color Token and OKLCH-Figma Compatibility**

This section addresses the specific and complex requirement of using the modern OKLCH color space while maintaining compatibility with design tools like Figma that do not natively support it.

### **4.1. The Problem: Modern Color vs. Legacy Tooling**

The request specifies using OKLCH as the preferred color space. This is a forward-looking choice. OKLCH is perceptually uniform (meaning a 10% change in lightness *looks* like a 10% change), making color scales predictable and accessible. It can also access the wide P3 color gamut, unlike sRGB.

The conflict arises with tooling. Figma, as of 2025, primarily supports sRGB (Hex, HSL, RGB).24 Pasting an oklch() function value into Figma's color picker will result in an error or an incorrect conversion.26 This creates a critical gap between the design source of truth (Figma) and the code source of truth (the tokens).

### **4.2. The Solution: The DTCG 2025.10 color Object**

The DTCG 2025.10 specification was designed with this exact problem in mind. The $value of a color token is *not* a simple hex string. Instead, it is an **object** designed to hold both a high-fidelity color definition and a fallback for sRGB-clamped systems.27

This color object structure is the authoritative solution.27 It explicitly provides a hex key to serve as the sRGB fallback, which is precisely what is needed for Figma compatibility.

**The Authoritative color Token Structure:**

JSON

"my-color-token": {  
  "$type": "color",  
  "$value": {  
    "colorSpace": "oklch",  
    "channels": \[  
      0.73,  // Lightness (L)  
      0.13,  // Chroma (C)  
      23.5   // Hue (H)  
    \],  
    "alpha": 1,  
    "hex": "\#f56524"   
  },  
  "$description": "OKLCH value is the source of truth. 'hex' is the sRGB-clamped fallback for Figma."  
}

In this structure:

* colorSpace and channels define the *true* color in OKLCH. This is what the build process will use to generate CSS oklch() functions for modern browsers.29  
* hex defines the *sRGB-clamped equivalent* of that color. This is what tools like Tokens Studio will read to populate Figma variables.31

### **4.3. The Implementation Workflow (Token Management)**

This color object enables a "single source of truth" workflow:

1. **Generate:** Color scales are generated using an OKLCH-aware tool (e.g., the OkColor Figma plugin 26, or an online generator like oklch.com 24).  
2. **Store:** For *each* color in the scale, *both* the OKLCH channel values and the sRGB-clamped hex fallback are stored in the primitives.json file, using the object structure from 4.2.27  
3. **Tooling (Tokens Studio):** When Tokens Studio is set to "W3C DTCG" mode, it will parse this object. When it "Exports to Figma Variables," it will use the hex value to create the Figma variable.32 This keeps the design tool in sync with an sRGB-compatible version of the color.  
4. **Tooling (Build Process):** The code build process (e.g., Style Dictionary) will be configured to parse the *same* object. For web platforms, it will prioritize the colorSpace and channels to generate modern CSS: var(--color-blue-500): oklch(0.6 0.19 264);. For legacy platforms, it can be configured to use the hex value as a fallback.

This approach perfectly resolves the conflict: Figma remains functional and synced, while the codebase receives the benefits of a modern, wide-gamut color space.

### **4.4. Complete Primitive Color Palette Example (JSON)**

This example combines the "Ordered Scale" naming (50-900) with the authoritative color object structure. The color groups have been restructured as requested into brand and utility categories.

JSON

"color": {  
  "$type": "color",  
  "$description": "Base color palette in OKLCH. 'hex' is the sRGB fallback for Figma.",  
  "brand": {  
    "primary": {  
      "$description": "Primary brand color, Blue. Hue \~260.",  
      "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.97, 0.02, 260.0\], "alpha": 1, "hex": "\#f3f4ff" } },  
      "100": { "$value": { "colorSpace": "oklch", "channels": \[0.94, 0.04, 260.0\], "alpha": 1, "hex": "\#e0e4ff" } },  
      "200": { "$value": { "colorSpace": "oklch", "channels": \[0.88, 0.08, 260.0\], "alpha": 1, "hex": "\#c3cfff" } },  
      "300": { "$value": { "colorSpace": "oklch", "channels": \[0.81, 0.12, 260.0\], "alpha": 1, "hex": "\#a2b1ff" } },  
      "400": { "$value": { "colorSpace": "oklch", "channels": \[0.73, 0.16, 260.0\], "alpha": 1, "hex": "\#8093ff" } },  
      "500": { "$value": { "colorSpace": "oklch", "channels": \[0.65, 0.19, 260.0\], "alpha": 1, "hex": "\#5e76ff" } },  
      "600": { "$value": { "colorSpace": "oklch", "channels": \[0.57, 0.20, 260.0\], "alpha": 1, "hex": "\#435fe1" } },  
      "700": { "$value": { "colorSpace": "oklch", "channels": \[0.49, 0.19, 260.0\], "alpha": 1, "hex": "\#354ecf" } },  
      "800": { "$value": { "colorSpace": "oklch", "channels": \[0.42, 0.17, 260.0\], "alpha": 1, "hex": "\#2c40b3" } },  
      "900": { "$value": { "colorSpace": "oklch", "channels": \[0.35, 0.15, 260.0\], "alpha": 1, "hex": "\#25348f" } }  
    },  
    "secondary": {  
      "$description": "Secondary brand color, Green. Hue \~145.",  
      "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.97, 0.02, 145.0\], "alpha": 1, "hex": "\#f0fdf4" } },  
      "100": { "$value": { "colorSpace": "oklch", "channels": \[0.94, 0.04, 145.0\], "alpha": 1, "hex": "\#dcfce7" } },  
      "200": { "$value": { "colorSpace": "oklch", "channels": \[0.88, 0.08, 145.0\], "alpha": 1, "hex": "\#bbf7d0" } },  
      "300": { "$value": { "colorSpace": "oklch", "channels": \[0.81, 0.12, 145.0\], "alpha": 1, "hex": "\#86efac" } },  
      "400": { "$value": { "colorSpace": "oklch", "channels": \[0.73, 0.16, 145.0\], "alpha": 1, "hex": "\#4ade80" } },  
      "500": { "$value": { "colorSpace": "oklch", "channels": \[0.65, 0.19, 145.0\], "alpha": 1, "hex": "\#22c55e" } },  
      "600": { "$value": { "colorSpace": "oklch", "channels": \[0.57, 0.20, 145.0\], "alpha": 1, "hex": "\#16a34a" } },  
      "700": { "$value": { "colorSpace": "oklch", "channels": \[0.49, 0.19, 145.0\], "alpha": 1, "hex": "\#15803d" } },  
      "800": { "$value": { "colorSpace": "oklch", "channels": \[0.42, 0.17, 145.0\], "alpha": 1, "hex": "\#166534" } },  
      "900": { "$value": { "colorSpace": "oklch", "channels": \[0.35, 0.15, 145.0\], "alpha": 1, "hex": "\#14532d" } }  
    },  
    "accent": {  
      "$description": "Accent brand color, Gold. Hue \~75.",  
      "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.98, 0.02, 75.0\], "alpha": 1, "hex": "\#fefce8" } },  
      "100": { "$value": { "colorSpace": "oklch", "channels": \[0.95, 0.04, 75.0\], "alpha": 1, "hex": "\#fef9c3" } },  
      "200": { "$value": { "colorSpace": "oklch", "channels": \[0.90, 0.08, 75.0\], "alpha": 1, "hex": "\#fef08a" } },  
      "300": { "$value": { "colorSpace": "oklch", "channels": \[0.84, 0.13, 75.0\], "alpha": 1, "hex": "\#fde047" } },  
      "400": { "$value": { "colorSpace": "oklch", "channels": \[0.75, 0.18, 75.0\], "alpha": 1, "hex": "\#facc15" } },  
      "500": { "$value": { "colorSpace": "oklch", "channels": \[0.68, 0.20, 75.0\], "alpha": 1, "hex": "\#eab308" } },  
      "600": { "$value": { "colorSpace": "oklch", "channels": \[0.60, 0.21, 75.0\], "alpha": 1, "hex": "\#ca8a04" } },  
      "700": { "$value": { "colorSpace": "oklch", "channels": \[0.52, 0.20, 75.0\], "alpha": 1, "hex": "\#a16207" } },  
      "800": { "$value": { "colorSpace": "oklch", "channels": \[0.44, 0.18, 75.0\], "alpha": 1, "hex": "\#854d0e" } },  
      "900": { "$value": { "colorSpace": "oklch", "channels": \[0.37, 0.16, 75.0\], "alpha": 1, "hex": "\#713f12" } }  
    }  
  },  
  "utility": {  
    "success": {  
      "$description": "Success color scale, Green. Hue \~145.",  
      "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.97, 0.02, 145.0\], "alpha": 1, "hex": "\#f0fdf4" } },  
      "100": { "$value": { "colorSpace": "oklch", "channels": \[0.94, 0.04, 145.0\], "alpha": 1, "hex": "\#dcfce7" } },  
      "200": { "$value": { "colorSpace": "oklch", "channels": \[0.88, 0.08, 145.0\], "alpha": 1, "hex": "\#bbf7d0" } },  
      "300": { "$value": { "colorSpace": "oklch", "channels": \[0.81, 0.12, 145.0\], "alpha": 1, "hex": "\#86efac" } },  
      "400": { "$value": { "colorSpace": "oklch", "channels": \[0.73, 0.16, 145.0\], "alpha": 1, "hex": "\#4ade80" } },  
      "500": { "$value": { "colorSpace": "oklch", "channels": \[0.65, 0.19, 145.0\], "alpha": 1, "hex": "\#22c55e" } },  
      "600": { "$value": { "colorSpace": "oklch", "channels": \[0.57, 0.20, 145.0\], "alpha": 1, "hex": "\#16a34a" } },  
      "700": { "$value": { "colorSpace": "oklch", "channels": \[0.49, 0.19, 145.0\], "alpha": 1, "hex": "\#15803d" } },  
      "800": { "$value": { "colorSpace": "oklch", "channels": \[0.42, 0.17, 145.0\], "alpha": 1, "hex": "\#166534" } },  
      "900": { "$value": { "colorSpace": "oklch", "channels": \[0.35, 0.15, 145.0\], "alpha": 1, "hex": "\#14532d" } }  
    },  
    "critical": {  
      "$description": "Critical/Error color scale, Red. Hue \~18.",  
      "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.97, 0.02, 18.0\], "alpha": 1, "hex": "\#fff5f5" } },  
      "100": { "$value": { "colorSpace": "oklch", "channels": \[0.94, 0.05, 18.0\], "alpha": 1, "hex": "\#ffe3e3" } },  
      "200": { "$value": { "colorSpace": "oklch", "channels": \[0.88, 0.09, 18.0\], "alpha": 1, "hex": "\#ffc9c9" } },  
      "300": { "$value": { "colorSpace": "oklch", "channels": \[0.82, 0.14, 18.0\], "alpha": 1, "hex": "\#ffa8a8" } },  
      "400": { "$value": { "colorSpace": "oklch", "channels": \[0.74, 0.19, 18.0\], "alpha": 1, "hex": "\#ff8787" } },  
      "500": { "$value": { "colorSpace": "oklch", "channels": \[0.67, 0.22, 18.0\], "alpha": 1, "hex": "\#fa5252" } },  
      "600": { "$value": { "colorSpace": "oklch", "channels": \[0.60, 0.23, 18.0\], "alpha": 1, "hex": "\#f03e3e" } },  
      "700": { "$value": { "colorSpace": "oklch", "channels": \[0.53, 0.21, 18.0\], "alpha": 1, "hex": "\#e03131" } },  
      "800": { "$value": { "colorSpace": "oklch", "channels": \[0.46, 0.19, 18.0\], "alpha": 1, "hex": "\#c92a2a" } },  
      "900": { "$value": { "colorSpace": "oklch", "channels": \[0.38, 0.16, 18.0\], "alpha": 1, "hex": "\#a61e1e" } }  
    },  
    "warning": {  
      "$description": "Warning color scale, Gold. Hue \~75.",  
      "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.98, 0.02, 75.0\], "alpha": 1, "hex": "\#fefce8" } },  
      "100": { "$value": { "colorSpace": "oklch", "channels": \[0.95, 0.04, 75.0\], "alpha": 1, "hex": "\#fef9c3" } },  
      "200": { "$value": { "colorSpace": "oklch", "channels": \[0.90, 0.08, 75.0\], "alpha": 1, "hex": "\#fef08a" } },  
      "300": { "$value": { "colorSpace": "oklch", "channels": \[0.84, 0.13, 75.0\], "alpha": 1, "hex": "\#fde047" } },  
      "400": { "$value": { "colorSpace": "oklch", "channels": \[0.75, 0.18, 75.0\], "alpha": 1, "hex": "\#facc15" } },  
      "500": { "$value": { "colorSpace": "oklch", "channels": \[0.68, 0.20, 75.0\], "alpha": 1, "hex": "\#eab308" } },  
      "600": { "$value": { "colorSpace": "oklch", "channels": \[0.60, 0.21, 75.0\], "alpha": 1, "hex": "\#ca8a04" } },  
      "700": { "$value": { "colorSpace": "oklch", "channels": \[0.52, 0.20, 75.0\], "alpha": 1, "hex": "\#a16207" } },  
      "800": { "$value": { "colorSpace": "oklch", "channels": \[0.44, 0.18, 75.0\], "alpha": 1, "hex": "\#854d0e" } },  
      "900": { "$value": { "colorSpace": "oklch", "channels": \[0.37, 0.16, 75.0\], "alpha": 1, "hex": "\#713f12" } }  
    },  
    "info": {  
      "$description": "Info color scale, Blue. Hue \~260.",  
      "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.97, 0.02, 260.0\], "alpha": 1, "hex": "\#f3f4ff" } },  
      "100": { "$value": { "colorSpace": "oklch", "channels": \[0.94, 0.04, 260.0\], "alpha": 1, "hex": "\#e0e4ff" } },  
      "200": { "$value": { "colorSpace": "oklch", "channels": \[0.88, 0.08, 260.0\], "alpha": 1, "hex": "\#c3cfff" } },  
      "300": { "$value": { "colorSpace": "oklch", "channels": \[0.81, 0.12, 260.0\], "alpha": 1, "hex": "\#a2b1ff" } },  
      "400": { "$value": { "colorSpace": "oklch", "channels": \[0.73, 0.16, 260.0\], "alpha": 1, "hex": "\#8093ff" } },  
      "500": { "$value": { "colorSpace": "oklch", "channels": \[0.65, 0.19, 260.0\], "alpha": 1, "hex": "\#5e76ff" } },  
      "600": { "$value": { "colorSpace": "oklch", "channels": \[0.57, 0.20, 260.0\], "alpha": 1, "hex": "\#435fe1" } },  
      "700": { "$value": { "colorSpace": "oklch", "channels": \[0.49, 0.19, 260.0\], "alpha": 1, "hex": "\#354ecf" } },  
      "800": { "$value": { "colorSpace": "oklch", "channels": \[0.42, 0.17, 260.0\], "alpha": 1, "hex": "\#2c40b3" } },  
      "900": { "$value": { "colorSpace": "oklch", "channels": \[0.35, 0.15, 260.0\], "alpha": 1, "hex": "\#25348f" } }  
    },  
    "neutral": {  
      "$description": "Neutral gray scale. Hue \~250.",  
      "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.99, 0.01, 250.0\], "alpha": 1, "hex": "\#f8f9fa" } },  
      "100": { "$value": { "colorSpace": "oklch", "channels": \[0.98, 0.01, 250.0\], "alpha": 1, "hex": "\#f1f3f5" } },  
      "200": { "$value": { "colorSpace": "oklch", "channels": \[0.94, 0.02, 250.0\], "alpha": 1, "hex": "\#e9ecef" } },  
      "300": { "$value": { "colorSpace": "oklch", "channels": \[0.90, 0.02, 250.0\], "alpha": 1, "hex": "\#dee2e6" } },  
      "400": { "$value": { "colorSpace": "oklch", "channels": \[0.85, 0.02, 250.0\], "alpha": 1, "hex": "\#ced4da" } },  
      "500": { "$value": { "colorSpace": "oklch", "channels": \[0.77, 0.02, 250.0\], "alpha": 1, "hex": "\#adb5bd" } },  
      "600": { "$value": { "colorSpace": "oklch", "channels": \[0.65, 0.02, 250.0\], "alpha": 1, "hex": "\#868e96" } },  
      "700": { "$value": { "colorSpace": "oklch", "channels": \[0.52, 0.02, 250.0\], "alpha": 1, "hex": "\#495057" } },  
      "800": { "$value": { "colorSpace": "oklch", "channels": \[0.39, 0.02, 250.0\], "alpha": 1, "hex": "\#343a40" } },  
      "900": { "$value": { "colorSpace": "oklch", "channels": \[0.22, 0.02, 250.0\], "alpha": 1, "hex": "\#212529" } }  
    },  
    "transparent": {  
      "$type": "color",  
      "$description": "Transparent colors for overlays and shadows.",  
      "white-10": { "$value": { "colorSpace": "srgb", "channels": \[1, 1, 1\], "alpha": 0.1, "hex": "\#ffffff1a" } },  
      "white-25": { "$value": { "colorSpace": "srgb", "channels": \[1, 1, 1\], "alpha": 0.25, "hex": "\#ffffff40" } },  
      "white-50": { "$value": { "colorSpace": "srgb", "channels": \[1, 1, 1\], "alpha": 0.5, "hex": "\#ffffff80" } },  
      "black-10": { "$value": { "colorSpace": "srgb", "channels": , "alpha": 0.1, "hex": "\#0000001a" } },  
      "black-25": { "$value": { "colorSpace": "srgb", "channels": , "alpha": 0.25, "hex": "\#00000040" } },  
      "black-50": { "$value": { "colorSpace": "srgb", "channels": , "alpha": 0.5, "hex": "\#00000080" } }  
    }  
  }  
}

## **Section 5: Primitives as the Foundation for Composite Tokens**

### **5.1. The Concept: Bundling Primitives**

Composite tokens are a "convenience" type defined by the DTCG 2025.10 specification.17 Their purpose is to bundle multiple, related style properties that are almost always applied together. Common examples include a complete typography style (font, size, weight, etc.) or a shadow (offset, blur, color).33

These composite tokens are *not* part of the primitive layer. Instead, they live in the *semantic* layer. Their $value is an object whose properties are *aliases* that reference the primitive tokens defined in Sections 3 and 4\. This structure provides a clean "recipe" that bundles primitives for a specific semantic purpose.33

### **5.2. DTCG 2025.10 Composite Token Types**

The specification defines a finite set of composite types. Building semantic tokens using these types ensures that downstream tools can provide specialized functionality, such as a "shadow" or "typography" picker in a design tool.34

**Table 2: DTCG 2025.10 Composite Token Types & Their Primitive Dependencies**

| $type | $value Structure (Object) | Required Primitives (for Aliasing) |
| :---- | :---- | :---- |
| **typography** | fontFamily, fontWeight, fontSize, lineHeight, etc. | fontFamily, fontWeight, dimension (for fontSize), number (for lineHeight) |
| **shadow** | offsetX, offsetY, blur, spread, color. Can be an array for multiple shadows.30 | dimension (for offsets, blur, spread), color |
| **border** | color, width, style (references a strokeStyle token). | color, dimension (for width), strokeStyle |
| **strokeStyle** | A string (e.g., "solid") OR an object {"dashArray":, "lineCap": "..."}.30 | dimension (for dashArray values) |
| **gradient** | colorSpace, direction, stops (array of color and position). | color (for stops), number (for position) |
| **transition** | duration, delay, timingFunction (references a cubicBezier token). | duration, cubicBezier |

### **5.3. Required Primitives for Composites**

To support composite tokens, the primitive layer must also define the necessary "ingredients." For example, to create a border token, a strokeStyle primitive must exist.

* **Required Primitives (e.g., in primitives.json):**  
  JSON  
  "border-style": {  
    "solid": {  
      "$type": "strokeStyle",  
      "$value": "solid"  
    },  
    "dashed": {  
      "$type": "strokeStyle",  
      "$value": "dashed"  
    },  
    "custom-dash": {  
      "$type": "strokeStyle",  
      "$value": {  
        "dashArray": \[  
          "{primitive.space.100}",  
          "{primitive.space.200}"  
        \],  
        "lineCap": "round"  
      }  
    }  
  }

## **Section 6: Semantic Token Architecture**

The semantic layer is where design intent is encoded.10 It acts as the "API" for the design system, providing context-aware tokens that developers and designers consume. This layer consists almost entirely of aliases pointing to the primitive layer.

For example, a semantic token color.background.default (the main page background) might point to primitive.color.utility.neutral.50 in a light theme but primitive.color.utility.neutral.900 in a dark theme. The component itself only ever references color.background.default, and the theme switching is handled by changing the alias's target.

Best practices for semantic naming involve describing the token's role or purpose. Common categories include:

* color.background... (for surfaces)  
* color.text... (for typography) 9  
* color.border...  
* color.interactive... (for buttons, links, etc.)  
* typography... (composite tokens for text styles) 33  
* shadow... (composite tokens for elevation) 30

## **Section 7: Semantic Token Specification (Default Light Theme)**

This file, semantic-light.json, defines the default theme for the system. It contains all semantic tokens, including colors, typography, and shadows, as aliases that reference the primitives.json set. This now includes a full set of shadow tokens.

JSON

{  
  "semantic-light": {  
    "$description": "Default light theme. Contains all semantic aliases.",  
    "color": {  
      "background": {  
        "default": {  
          "$value": "{primitive.color.utility.neutral.50}",  
          "$description": "Default page background color."  
        },  
        "surface": {  
          "$value": "{primitive.color.utility.neutral.100}",  
          "$description": "Slightly elevated surface color (e.g., cards)."  
        },  
        "interactive": {  
          "default": { "$value": "{primitive.color.brand.primary.500}" },  
          "hover": { "$value": "{primitive.color.brand.primary.600}" },  
          "pressed": { "$value": "{primitive.color.brand.primary.700}" }  
        },  
        "success": { "$value": "{primitive.color.utility.success.500}" },  
        "critical": { "$value": "{primitive.color.utility.critical.500}" },  
        "warning": { "$value": "{primitive.color.utility.warning.500}" },  
        "info": { "$value": "{primitive.color.utility.info.500}" }  
      },  
      "text": {  
        "default": {  
          "$value": "{primitive.color.utility.neutral.900}",  
          "$description": "Default text color for body copy."  
        },  
        "muted": {  
          "$value": "{primitive.color.utility.neutral.600}",  
          "$description": "Muted text color for secondary information."  
        },  
        "on-interactive": {  
          "$value": "{primitive.color.utility.neutral.50}",  
          "$description": "Text color for use on interactive backgrounds."  
        },  
        "success": { "$value": "{primitive.color.utility.success.700}" },  
        "critical": { "$value": "{primitive.color.utility.critical.700}" },  
        "warning": { "$value": "{primitive.color.utility.warning.700}" },  
        "info": { "$value": "{primitive.color.utility.info.700}" }  
      },  
      "border": {  
        "default": {  
          "$value": "{primitive.color.utility.neutral.300}",  
          "$description": "Default border for containers and separators."  
        },  
        "interactive": {  
          "$value": "{primitive.color.brand.primary.500}"  
        }  
      }  
    },  
    "typography": {  
      "heading-large": {  
        "$type": "typography",  
        "$value": {  
          "fontFamily": "{primitive.font.family.sans}",  
          "fontWeight": "{primitive.font.weight.700}",  
          "fontSize": "{primitive.font.size.600}",  
          "lineHeight": "{primitive.line-height.100}"  
        },  
        "$description": "Large heading style for page titles."  
      },  
      "body-default": {  
        "$type": "typography",  
        "$value": {  
          "fontFamily": "{primitive.font.family.sans}",  
          "fontWeight": "{primitive.font.weight.400}",  
          "fontSize": "{primitive.font.size.300}",  
          "lineHeight": "{primitive.line-height.200}"  
        },  
        "$description": "Default body text style."  
      }  
    },  
    "shadow": {  
      "small": {  
        "$type": "shadow",  
        "$value":,  
        "$description": "Small, subtle shadow for UI controls."  
      },  
      "medium": {  
        "$type": "shadow",  
        "$value":,  
        "$description": "Default shadow for raised elements like cards."  
      },  
      "large": {  
        "$type": "shadow",  
        "$value":,  
        "$description": "Large shadow for modals and popovers."  
      }  
    }  
  }  
}

## **Section 8: Theming with $extends (Dark Theme)**

This section details the official, DTCG 2025.10-compliant architecture for handling themes (e.g., light/dark modes).

The DTCG 2025.10 specification's official mechanism for theming is the **$extends** property, which is applied at a *group* level.20

The $extends property instructs a token group to inherit all tokens and properties from a referenced group. It performs a **deep merge**: the extending group (e.g., semantic-dark) inherits everything from the base group (e.g., semantic-light), and any tokens defined locally in the extending group will *override* inherited tokens at the same path.20

This enables a clean, scalable, and fully spec-compliant architecture, as requested. The semantic-dark.json file only needs to define the tokens that *change* from the light theme, resulting in a minimal and maintainable file. This now includes overrides for shadow tokens, which typically need to be more opaque in dark mode to be visible.

* **semantic-dark.json (Example):**  
  JSON  
  {  
    "semantic-dark": {  
      "$extends": "{semantic-light}",  
      "$description": "Dark theme overrides. Inherits all tokens from semantic-light.",  
      "color": {  
        "background": {  
          "default": {  
            "$value": "{primitive.color.utility.neutral.900}",  
            "$description": "Default page background color."  
          },  
          "surface": {  
            "$value": "{primitive.color.utility.neutral.800}",  
            "$description": "Slightly elevated surface color (e.g., cards)."  
          },  
          "success": { "$value": "{primitive.color.utility.success.400}" },  
          "critical": { "$value": "{primitive.color.utility.critical.400}" },  
          "warning": { "$value": "{primitive.color.utility.warning.400}" },  
          "info": { "$value": "{primitive.color.utility.info.400}" }  
        },  
        "text": {  
          "default": {  
            "$value": "{primitive.color.utility.neutral.100}",  
            "$description": "Default text color for body copy."  
          },  
          "muted": {  
            "$value": "{primitive.color.utility.neutral.400}",  
            "$description": "Muted text color for secondary information."  
          },  
          "success": { "$value": "{primitive.color.utility.success.300}" },  
          "critical": { "$value": "{primitive.color.utility.critical.300}" },  
          "warning": { "$value": "{primitive.color.utility.warning.300}" },  
          "info": { "$value": "{primitive.color.utility.info.300}" }  
        },  
        "border": {  
          "default": {  
            "$value": "{primitive.color.utility.neutral.700}",  
            "$description": "Default border for containers and separators."  
          }  
        }  
      },  
      "shadow": {  
        "small": {  
          "$type": "shadow",  
          "$value":,  
          "$description": "Small shadow for dark mode."  
        },  
        "medium": {  
          "$type": "shadow",  
          "$value":,  
          "$description": "Medium shadow for dark mode."  
        },  
        "large": {  
          "$type": "shadow",  
          "$value":,  
          "$description": "Large shadow for dark mode."  
        }  
      }  
    }  
  }

## **Section 9: Implementation Guide: Tokens Studio**

This section provides an actionable, step-by-step workflow for implementing the defined architecture within Tokens Studio.

### **9.1. Setting Up Your Token Sets**

1. **Set DTCG Format:** In Tokens Studio, navigate to *Settings \> Token Format*. Set the format to **"W3C DTCG"**.14 This is mandatory for the $type and $value object structures defined in this report to be correctly parsed.14  
2. **Create primitives Set:** Create a new Token Set (or folder) named primitives.13 Populate this set with the complete JSON of all primitive tokens (from Sections 3, 4, and 10.2).  
3. **Set as Source:** Click the dot icon next to the primitives set and select **"Set as Source"**.15 The icon will become a solid circle. This action makes all {primitive...} tokens available for aliasing in other sets but prevents them from being applied directly.  
4. **Create semantic-light Set:** Create a new Token Set named semantic-light. This set should be **Enabled** (checkmark). Populate it *only* with semantic aliases and composite tokens, as shown in Section 7\.14  
5. **Create semantic-dark Set:** Create a new Token Set named semantic-dark. This set should be **Disabled** by default. Populate it with the $extends block and the dark mode overrides, as shown in Section 8\.14

### **9.2. Managing Themes and Modes**

1. **Navigate to Themes:** Go to the "Themes" tab in Tokens Studio (this is a Pro feature) \[14, "Themes (pro)", 14\].  
2. **Create Theme Group:** Create a new Theme Group (e.g., "Mode" or "Brand").  
3. **Create "Light" Theme:** Create a new Theme named "Light". Configure this theme to have the following Token Sets active:  
   * primitives (Set as Source)  
   * semantic-light (Enabled)  
4. **Create "Dark" Theme:** Create a new Theme named "Dark". Configure this theme to have the following Token Sets active:  
   * primitives (Set as Source)  
   * semantic-dark (Enabled)  
   * (Note: semantic-light will be implicitly included via the $extends in semantic-dark).  
5. **Export to Figma:** When these themes are applied, Tokens Studio will manage the active token set. When exporting to Figma, the plugin will use the hex fallback values from the color token objects (Section 4.2) to correctly populate Figma's native Variable Collections and Modes.31

## **Section 10: Final Guidelines and Schema Compendium**

### **10.1. Summary of Architectural Guidelines**

* **Hierarchy:** Implement a three-tier token system (Primitive, Semantic, Component), but avoid the Component tier where possible to reduce complexity.4 Primitives are the raw values 5, Semantics are the purpose-driven aliases.8  
* **Primitives:** All primitives must be non-semantic. Use the "Ordered Scales" naming convention (e.g., 100-900) for all ramped values, including color, space, font.size, and line-height, to create a unified system.7  
* **Specification:** Adhere strictly to the DTCG 2025.10 types.22 Set all tooling (e.g., Tokens Studio) to "W3C DTCG" format.14  
* **Color Strategy:** Use the DTCG color object.27 Define the source of truth as colorSpace: "oklch" with channels, and provide a sRGB-clamped hex property in the *same object* as a fallback for design tools like Figma.28  
* **Composites:** Use composite types (typography, shadow, border) in the *semantic* layer.33 Their values should *only* be aliases referencing the primitive layer.  
* **Theming:** Use the **$extends** group property for theming.20 This is the official DTCG 2025.10 standard. Do not use the $extensions property for theming logic.18  
* **Tooling:** In Tokens Studio, use a "Source" set for all primitives 15 and "Enabled" sets for semantic themes, managed via the "Themes" tab.14

### **10.2. Complete primitives.json Schema Example**

The following JSON object represents a complete, production-ready primitives.json file. It incorporates all recommended groupings, "Ordered Scale" naming, and the complete set of DTCG 2025.10 primitive types based on your requests.

JSON

{  
  "primitive": {  
    "$description": "Base primitive tokens. The non-semantic, context-agnostic source of truth for all raw style values. DTCG 2025.10 Compliant.",  
    "color": {  
      "$description": "Base color palette. Defined in OKLCH with sRGB-clamped hex fallbacks for design tools.",  
      "$type": "color",  
      "brand": {  
        "primary": {  
          "$description": "Primary brand color, Blue. Hue \~260.",  
          "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.97, 0.02, 260.0\], "alpha": 1, "hex": "\#f3f4ff" } },  
          "100": { "$value": { "colorSpace": "oklch", "channels": \[0.94, 0.04, 260.0\], "alpha": 1, "hex": "\#e0e4ff" } },  
          "200": { "$value": { "colorSpace": "oklch", "channels": \[0.88, 0.08, 260.0\], "alpha": 1, "hex": "\#c3cfff" } },  
          "300": { "$value": { "colorSpace": "oklch", "channels": \[0.81, 0.12, 260.0\], "alpha": 1, "hex": "\#a2b1ff" } },  
          "400": { "$value": { "colorSpace": "oklch", "channels": \[0.73, 0.16, 260.0\], "alpha": 1, "hex": "\#8093ff" } },  
          "500": { "$value": { "colorSpace": "oklch", "channels": \[0.65, 0.19, 260.0\], "alpha": 1, "hex": "\#5e76ff" } },  
          "600": { "$value": { "colorSpace": "oklch", "channels": \[0.57, 0.20, 260.0\], "alpha": 1, "hex": "\#435fe1" } },  
          "700": { "$value": { "colorSpace": "oklch", "channels": \[0.49, 0.19, 260.0\], "alpha": 1, "hex": "\#354ecf" } },  
          "800": { "$value": { "colorSpace": "oklch", "channels": \[0.42, 0.17, 260.0\], "alpha": 1, "hex": "\#2c40b3" } },  
          "900": { "$value": { "colorSpace": "oklch", "channels": \[0.35, 0.15, 260.0\], "alpha": 1, "hex": "\#25348f" } }  
        },  
        "secondary": {  
          "$description": "Secondary brand color, Green. Hue \~145.",  
          "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.97, 0.02, 145.0\], "alpha": 1, "hex": "\#f0fdf4" } },  
          "100": { "$value": { "colorSpace": "oklch", "channels": \[0.94, 0.04, 145.0\], "alpha": 1, "hex": "\#dcfce7" } },  
          "200": { "$value": { "colorSpace": "oklch", "channels": \[0.88, 0.08, 145.0\], "alpha": 1, "hex": "\#bbf7d0" } },  
          "300": { "$value": { "colorSpace": "oklch", "channels": \[0.81, 0.12, 145.0\], "alpha": 1, "hex": "\#86efac" } },  
          "400": { "$value": { "colorSpace": "oklch", "channels": \[0.73, 0.16, 145.0\], "alpha": 1, "hex": "\#4ade80" } },  
          "500": { "$value": { "colorSpace": "oklch", "channels": \[0.65, 0.19, 145.0\], "alpha": 1, "hex": "\#22c55e" } },  
          "600": { "$value": { "colorSpace": "oklch", "channels": \[0.57, 0.20, 145.0\], "alpha": 1, "hex": "\#16a34a" } },  
          "700": { "$value": { "colorSpace": "oklch", "channels": \[0.49, 0.19, 145.0\], "alpha": 1, "hex": "\#15803d" } },  
          "800": { "$value": { "colorSpace": "oklch", "channels": \[0.42, 0.17, 145.0\], "alpha": 1, "hex": "\#166534" } },  
          "900": { "$value": { "colorSpace": "oklch", "channels": \[0.35, 0.15, 145.0\], "alpha": 1, "hex": "\#14532d" } }  
        },  
        "accent": {  
          "$description": "Accent brand color, Gold. Hue \~75.",  
          "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.98, 0.02, 75.0\], "alpha": 1, "hex": "\#fefce8" } },  
          "100": { "$value": { "colorSpace": "oklch", "channels": \[0.95, 0.04, 75.0\], "alpha": 1, "hex": "\#fef9c3" } },  
          "200": { "$value": { "colorSpace": "oklch", "channels": \[0.90, 0.08, 75.0\], "alpha": 1, "hex": "\#fef08a" } },  
          "300": { "$value": { "colorSpace": "oklch", "channels": \[0.84, 0.13, 75.0\], "alpha": 1, "hex": "\#fde047" } },  
          "400": { "$value": { "colorSpace": "oklch", "channels": \[0.75, 0.18, 75.0\], "alpha": 1, "hex": "\#facc15" } },  
          "500": { "$value": { "colorSpace": "oklch", "channels": \[0.68, 0.20, 75.0\], "alpha": 1, "hex": "\#eab308" } },  
          "600": { "$value": { "colorSpace": "oklch", "channels": \[0.60, 0.21, 75.0\], "alpha": 1, "hex": "\#ca8a04" } },  
          "700": { "$value": { "colorSpace": "oklch", "channels": \[0.52, 0.20, 75.0\], "alpha": 1, "hex": "\#a16207" } },  
          "800": { "$value": { "colorSpace": "oklch", "channels": \[0.44, 0.18, 75.0\], "alpha": 1, "hex": "\#854d0e" } },  
          "900": { "$value": { "colorSpace": "oklch", "channels": \[0.37, 0.16, 75.0\], "alpha": 1, "hex": "\#713f12" } }  
        }  
      },  
      "utility": {  
        "success": {  
          "$description": "Success color scale, Green. Hue \~145.",  
          "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.97, 0.02, 145.0\], "alpha": 1, "hex": "\#f0fdf4" } },  
          "100": { "$value": { "colorSpace": "oklch", "channels": \[0.94, 0.04, 145.0\], "alpha": 1, "hex": "\#dcfce7" } },  
          "200": { "$value": { "colorSpace": "oklch", "channels": \[0.88, 0.08, 145.0\], "alpha": 1, "hex": "\#bbf7d0" } },  
          "300": { "$value": { "colorSpace": "oklch", "channels": \[0.81, 0.12, 145.0\], "alpha": 1, "hex": "\#86efac" } },  
          "400": { "$value": { "colorSpace": "oklch", "channels": \[0.73, 0.16, 145.0\], "alpha": 1, "hex": "\#4ade80" } },  
          "500": { "$value": { "colorSpace": "oklch", "channels": \[0.65, 0.19, 145.0\], "alpha": 1, "hex": "\#22c55e" } },  
          "600": { "$value": { "colorSpace": "oklch", "channels": \[0.57, 0.20, 145.0\], "alpha": 1, "hex": "\#16a34a" } },  
          "700": { "$value": { "colorSpace": "oklch", "channels": \[0.49, 0.19, 145.0\], "alpha": 1, "hex": "\#15803d" } },  
          "800": { "$value": { "colorSpace": "oklch", "channels": \[0.42, 0.17, 145.0\], "alpha": 1, "hex": "\#166534" } },  
          "900": { "$value": { "colorSpace": "oklch", "channels": \[0.35, 0.15, 145.0\], "alpha": 1, "hex": "\#14532d" } }  
        },  
        "critical": {  
          "$description": "Critical/Error color scale, Red. Hue \~18.",  
          "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.97, 0.02, 18.0\], "alpha": 1, "hex": "\#fff5f5" } },  
          "100": { "$value": { "colorSpace": "oklch", "channels": \[0.94, 0.05, 18.0\], "alpha": 1, "hex": "\#ffe3e3" } },  
          "200": { "$value": { "colorSpace": "oklch", "channels": \[0.88, 0.09, 18.0\], "alpha": 1, "hex": "\#ffc9c9" } },  
          "300": { "$value": { "colorSpace": "oklch", "channels": \[0.82, 0.14, 18.0\], "alpha": 1, "hex": "\#ffa8a8" } },  
          "400": { "$value": { "colorSpace": "oklch", "channels": \[0.74, 0.19, 18.0\], "alpha": 1, "hex": "\#ff8787" } },  
          "500": { "$value": { "colorSpace": "oklch", "channels": \[0.67, 0.22, 18.0\], "alpha": 1, "hex": "\#fa5252" } },  
          "600": { "$value": { "colorSpace": "oklch", "channels": \[0.60, 0.23, 18.0\], "alpha": 1, "hex": "\#f03e3e" } },  
          "700": { "$value": { "colorSpace": "oklch", "channels": \[0.53, 0.21, 18.0\], "alpha": 1, "hex": "\#e03131" } },  
          "800": { "$value": { "colorSpace": "oklch", "channels": \[0.46, 0.19, 18.0\], "alpha": 1, "hex": "\#c92a2a" } },  
          "900": { "$value": { "colorSpace": "oklch", "channels": \[0.38, 0.16, 18.0\], "alpha": 1, "hex": "\#a61e1e" } }  
        },  
        "warning": {  
          "$description": "Warning color scale, Gold. Hue \~75.",  
          "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.98, 0.02, 75.0\], "alpha": 1, "hex": "\#fefce8" } },  
          "100": { "$value": { "colorSpace": "oklch", "channels": \[0.95, 0.04, 75.0\], "alpha": 1, "hex": "\#fef9c3" } },  
          "200": { "$value": { "colorSpace": "oklch", "channels": \[0.90, 0.08, 75.0\], "alpha": 1, "hex": "\#fef08a" } },  
          "300": { "$value": { "colorSpace": "oklch", "channels": \[0.84, 0.13, 75.0\], "alpha": 1, "hex": "\#fde047" } },  
          "400": { "$value": { "colorSpace": "oklch", "channels": \[0.75, 0.18, 75.0\], "alpha": 1, "hex": "\#facc15" } },  
          "500": { "$value": { "colorSpace": "oklch", "channels": \[0.68, 0.20, 75.0\], "alpha": 1, "hex": "\#eab308" } },  
          "600": { "$value": { "colorSpace": "oklch", "channels": \[0.60, 0.21, 75.0\], "alpha": 1, "hex": "\#ca8a04" } },  
          "700": { "$value": { "colorSpace": "oklch", "channels": \[0.52, 0.20, 75.0\], "alpha": 1, "hex": "\#a16207" } },  
          "800": { "$value": { "colorSpace": "oklch", "channels": \[0.44, 0.18, 75.0\], "alpha": 1, "hex": "\#854d0e" } },  
          "900": { "$value": { "colorSpace": "oklch", "channels": \[0.37, 0.16, 75.0\], "alpha": 1, "hex": "\#713f12" } }  
        },  
        "info": {  
          "$description": "Info color scale, Blue. Hue \~260.",  
          "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.97, 0.02, 260.0\], "alpha": 1, "hex": "\#f3f4ff" } },  
          "100": { "$value": { "colorSpace": "oklch", "channels": \[0.94, 0.04, 260.0\], "alpha": 1, "hex": "\#e0e4ff" } },  
          "200": { "$value": { "colorSpace": "oklch", "channels": \[0.88, 0.08, 260.0\], "alpha": 1, "hex": "\#c3cfff" } },  
          "300": { "$value": { "colorSpace": "oklch", "channels": \[0.81, 0.12, 260.0\], "alpha": 1, "hex": "\#a2b1ff" } },  
          "400": { "$value": { "colorSpace": "oklch", "channels": \[0.73, 0.16, 260.0\], "alpha": 1, "hex": "\#8093ff" } },  
          "500": { "$value": { "colorSpace": "oklch", "channels": \[0.65, 0.19, 260.0\], "alpha": 1, "hex": "\#5e76ff" } },  
          "600": { "$value": { "colorSpace": "oklch", "channels": \[0.57, 0.20, 260.0\], "alpha": 1, "hex": "\#435fe1" } },  
          "700": { "$value": { "colorSpace": "oklch", "channels": \[0.49, 0.19, 260.0\], "alpha": 1, "hex": "\#354ecf" } },  
          "800": { "$value": { "colorSpace": "oklch", "channels": \[0.42, 0.17, 260.0\], "alpha": 1, "hex": "\#2c40b3" } },  
          "900": { "$value": { "colorSpace": "oklch", "channels": \[0.35, 0.15, 260.0\], "alpha": 1, "hex": "\#25348f" } }  
        },  
        "neutral": {  
          "$description": "Neutral gray scale. Hue \~250.",  
          "50":  { "$value": { "colorSpace": "oklch", "channels": \[0.99, 0.01, 250.0\], "alpha": 1, "hex": "\#f8f9fa" } },  
          "100": { "$value": { "colorSpace": "oklch", "channels": \[0.98, 0.01, 250.0\], "alpha": 1, "hex": "\#f1f3f5" } },  
          "200": { "$value": { "colorSpace": "oklch", "channels": \[0.94, 0.02, 250.0\], "alpha": 1, "hex": "\#e9ecef" } },  
          "300": { "$value": { "colorSpace": "oklch", "channels": \[0.90, 0.02, 250.0\], "alpha": 1, "hex": "\#dee2e6" } },  
          "400": { "$value": { "colorSpace": "oklch", "channels": \[0.85, 0.02, 250.0\], "alpha": 1, "hex": "\#ced4da" } },  
          "500": { "$value": { "colorSpace": "oklch", "channels": \[0.77, 0.02, 250.0\], "alpha": 1, "hex": "\#adb5bd" } },  
          "600": { "$value": { "colorSpace": "oklch", "channels": \[0.65, 0.02, 250.0\], "alpha": 1, "hex": "\#868e96" } },  
          "700": { "$value": { "colorSpace": "oklch", "channels": \[0.52, 0.02, 250.0\], "alpha": 1, "hex": "\#495057" } },  
          "800": { "$value": { "colorSpace": "oklch", "channels": \[0.39, 0.02, 250.0\], "alpha": 1, "hex": "\#343a40" } },  
          "900": { "$value": { "colorSpace": "oklch", "channels": \[0.22, 0.02, 250.0\], "alpha": 1, "hex": "\#212529" } }  
        },  
        "transparent": {  
          "$type": "color",  
          "$description": "Transparent colors for overlays and shadows.",  
          "white-10": { "$value": { "colorSpace": "srgb", "channels": \[1, 1, 1\], "alpha": 0.1, "hex": "\#ffffff1a" } },  
          "white-25": { "$value": { "colorSpace": "srgb", "channels": \[1, 1, 1\], "alpha": 0.25, "hex": "\#ffffff40" } },  
          "white-50": { "$value": { "colorSpace": "srgb", "channels": \[1, 1, 1\], "alpha": 0.5, "hex": "\#ffffff80" } },  
          "black-10": { "$value": { "colorSpace": "srgb", "channels": , "alpha": 0.1, "hex": "\#0000001a" } },  
          "black-25": { "$value": { "colorSpace": "srgb", "channels": , "alpha": 0.25, "hex": "\#00000040" } },  
          "black-50": { "$value": { "colorSpace": "srgb", "channels": , "alpha": 0.5, "hex": "\#00000080" } }  
        }  
      }  
    },  
    "space": {  
      "$description": "Base dimension scale for spacing, padding, margins. Based on a 4px grid.",  
      "$type": "dimension",  
      "0":   { "$value": { "value": 0, "unit": "px" } },  
      "50":  { "$value": { "value": 2, "unit": "px" } },  
      "100": { "$value": { "value": 4, "unit": "px" } },  
      "200": { "$value": { "value": 8, "unit": "px" } },  
      "300": { "$value": { "value": 12, "unit": "px" } },  
      "400": { "$value": { "value": 16, "unit": "px" } },  
      "500": { "$value": { "value": 20, "unit": "px" } },  
      "600": { "$value": { "value": 24, "unit": "px" } },  
      "700": { "$value": { "value": 32, "unit": "px" } },  
      "800": { "$value": { "value": 48, "unit": "px" } },  
      "900": { "$value": { "value": 64, "unit": "px" } }  
    },  
    "breakpoint": {  
      "$type": "dimension",  
      "$description": "Breakpoint values for responsive media queries.",  
      "sm": { "$value": { "value": 576, "unit": "px" } },  
      "md": { "$value": { "value": 768, "unit": "px" } },  
      "lg": { "$value": { "value": 992, "unit": "px" } },  
      "xl": { "$value": { "value": 1200, "unit": "px" } }  
    },  
    "radius": {  
      "$description": "Base dimension scale for border-radius.",  
      "$type": "dimension",  
      "100": { "$value": { "value": 4, "unit": "px" } },  
      "200": { "$value": { "value": 8, "unit": "px" } },  
      "300": { "$value": { "value": 16, "unit": "px" } },  
      "full": { "$value": { "value": 9999, "unit": "px" } }  
    },  
    "font": {  
      "family": {  
        "$type": "fontFamily",  
        "sans": { "$value": \["Inter", "system-ui", "sans-serif"\] },  
        "serif": { "$value": },  
        "mono": { "$value": }  
      },  
      "weight": {  
        "$type": "fontWeight",  
        "300": { "$value": 300 },  
        "400": { "$value": 400 },  
        "500": { "$value": 500 },  
        "700": { "$value": 700 }  
      },  
      "size": {  
        "$type": "dimension",  
        "100": { "$value": { "value": 12, "unit": "px" } },  
        "200": { "$value": { "value": 14, "unit": "px" } },  
        "300": { "$value": { "value": 16, "unit": "px" } },  
        "400": { "$value": { "value": 20, "unit": "px" } },  
        "500": { "$value": { "value": 24, "unit": "px" } },  
        "600": { "$value": { "value": 30, "unit": "px" } }  
      }  
    },  
    "line-height": {  
      "$description": "Unitless line-height scale.",  
      "$type": "number",  
      "100": { "$value": 1.25 },  
      "200": { "$value": 1.5 },  
      "300": { "$value": 1.75 }  
    },  
    "opacity": {  
      "$description": "Opacity scale as a unitless number.",  
      "$type": "number",  
      "10": { "$value": 0.1 },  
      "50": { "$value": 0.5 },  
      "100": { "$value": 1 }  
    },  
    "transition": {  
      "duration": {  
        "$type": "duration",  
        "100": { "$value": "100ms" },  
        "200": { "$value": "200ms" },  
        "300": { "$value": "300ms" }  
      },  
      "ease": {  
        "$type": "cubicBezier",  
        "linear": { "$value":  },  
        "in":     { "$value": \[0.42, 0, 1, 1\] },  
        "out":    { "$value": \[0, 0, 0.58, 1\] },  
        "in-out": { "$value": \[0.42, 0, 0.58, 1\] }  
      }  
    },  
    "border-style": {  
      "$type": "strokeStyle",  
      "solid": { "$value": "solid" },  
      "dashed": { "$value": "dashed" },  
      "dotted": { "$value": "dotted" }  
    }  
  }  
}

#### **Works cited**

1. Design Tokens · microsoft/fluentui-apple Wiki \- GitHub, accessed November 8, 2025, [https://github.com/microsoft/fluentui-apple/wiki/Design-Tokens](https://github.com/microsoft/fluentui-apple/wiki/Design-Tokens)  
2. Token naming reference | Fluent UI token pipeline \- Microsoft Open Source, accessed November 8, 2025, [https://microsoft.github.io/fluentui-token-pipeline/naming.html](https://microsoft.github.io/fluentui-token-pipeline/naming.html)  
3. Design tokens \- Fluent 2 Design System, accessed November 8, 2025, [https://fluent2.microsoft.design/design-tokens](https://fluent2.microsoft.design/design-tokens)  
4. DS Styleguide Terrazzo, accessed November 8, 2025, [https://terrazzo.app/docs/guides/styleguide/](https://terrazzo.app/docs/guides/styleguide/)  
5. Update 1: Tokens, variables, and styles – Figma Learn \- Help Center, accessed November 8, 2025, [https://help.figma.com/hc/en-us/articles/18490793776023-Update-1-Tokens-variables-and-styles](https://help.figma.com/hc/en-us/articles/18490793776023-Update-1-Tokens-variables-and-styles)  
6. Design tokens \- Base design system \- Uber, accessed November 8, 2025, [https://base.uber.com/6d2425e9f/p/33fa5e-design-tokens](https://base.uber.com/6d2425e9f/p/33fa5e-design-tokens)  
7. Design tokens | Design good practices, accessed November 8, 2025, [https://goodpractices.design/articles/design-tokens](https://goodpractices.design/articles/design-tokens)  
8. Unraveling the Magic of Semantic and Primitive Tokens in UI Design: Why Figma Embraced the Revolution | by Sepanta Pouya | Bootcamp | Medium, accessed November 8, 2025, [https://medium.com/design-bootcamp/unraveling-the-magic-of-semantic-and-primitive-tokens-in-ui-design-why-figma-embraced-the-6c564d8a20d7](https://medium.com/design-bootcamp/unraveling-the-magic-of-semantic-and-primitive-tokens-in-ui-design-why-figma-embraced-the-6c564d8a20d7)  
9. Design Tokens | components.build, accessed November 8, 2025, [https://www.components.build/design-tokens](https://www.components.build/design-tokens)  
10. Design tokens \- Backbase Design System, accessed November 8, 2025, [https://designsystem.backbase.com/design-tokens](https://designsystem.backbase.com/design-tokens)  
11. Design tokens explained (and how to build a design token system) \- Contentful, accessed November 8, 2025, [https://www.contentful.com/blog/design-token-system/](https://www.contentful.com/blog/design-token-system/)  
12. Design Tokens specification reaches first stable version \- W3C, accessed November 8, 2025, [https://www.w3.org/community/design-tokens/2025/10/28/design-tokens-specification-reaches-first-stable-version/](https://www.w3.org/community/design-tokens/2025/10/28/design-tokens-specification-reaches-first-stable-version/)  
13. Setting up Studio, accessed November 8, 2025, [https://documentation.tokens.studio/getting-started/setting-up-studio](https://documentation.tokens.studio/getting-started/setting-up-studio)  
14. Tokens Studio for Figma: Tokens Studio Plugin for Figma, accessed November 8, 2025, [https://docs.tokens.studio/](https://docs.tokens.studio/)  
15. Token Sets \- Tokens Studio for Figma, accessed November 8, 2025, [https://docs.tokens.studio/manage-tokens/token-sets](https://docs.tokens.studio/manage-tokens/token-sets)  
16. Design Tokens Format Module 2025.10, accessed November 8, 2025, [https://www.designtokens.org/tr/2025.10/format/\#group-structure](https://www.designtokens.org/tr/2025.10/format/#group-structure)  
17. Design Tokens Format Module 2025.10, accessed November 8, 2025, [https://www.designtokens.org/tr/drafts/format/](https://www.designtokens.org/tr/drafts/format/)  
18. Design Tokens Format Module 2025.10, accessed November 8, 2025, [https://www.designtokens.org/tr/2025.10/format/\#extensions](https://www.designtokens.org/tr/2025.10/format/#extensions)  
19. DTCG W3C | Token Group Metadata \- Description and Type \- Tokens Studio, accessed November 8, 2025, [https://feedback.tokens.studio/p/token-group-metadata-description-and-type](https://feedback.tokens.studio/p/token-group-metadata-description-and-type)  
20. Design Tokens Format Module 2025.10, accessed November 8, 2025, [https://www.designtokens.org/tr/2025.10/format/\#extending-groups](https://www.designtokens.org/tr/2025.10/format/#extending-groups)  
21. @contentful/f36-tokens \- npm, accessed November 8, 2025, [https://www.npmjs.com/package/@contentful/f36-tokens](https://www.npmjs.com/package/@contentful/f36-tokens)  
22. Design Tokens Format Module 2025.10, accessed November 8, 2025, [https://www.designtokens.org/tr/2025.10/format/\#types](https://www.designtokens.org/tr/2025.10/format/#types)  
23. Token Types Terrazzo, accessed November 8, 2025, [https://terrazzo.app/docs/reference/tokens/](https://terrazzo.app/docs/reference/tokens/)  
24. Lets ditch the RGB & HSL for OKLCH new format for designers | by Dhiraj Ingle \- Medium, accessed November 8, 2025, [https://medium.com/@dhiiraj4ux/lets-ditch-the-rgb-hsl-for-oklch-new-format-for-designers-27b65c603e96](https://medium.com/@dhiiraj4ux/lets-ditch-the-rgb-hsl-for-oklch-new-format-for-designers-27b65c603e96)  
25. Introduction to OKLCH \- Caleb Durenberger, accessed November 8, 2025, [https://calebduren.com/posts/oklch](https://calebduren.com/posts/oklch)  
26. Exploring the OKLCH ecosystem and its tools \- Evil Martians, accessed November 8, 2025, [https://evilmartians.com/chronicles/exploring-the-oklch-ecosystem-and-its-tools](https://evilmartians.com/chronicles/exploring-the-oklch-ecosystem-and-its-tools)  
27. DTCG format update on color tokens and support for color spaces. \- Tokens Studio, accessed November 8, 2025, [https://feedback.tokens.studio/p/dtcg-format-update-on-color-tokens-and-support-for-color](https://feedback.tokens.studio/p/dtcg-format-update-on-color-tokens-and-support-for-color)  
28. Tokens Studio: Feedback, accessed November 8, 2025, [https://feedback.tokens.studio/](https://feedback.tokens.studio/)  
29. Design Tokens Color Module 2025.10, accessed November 8, 2025, [https://www.designtokens.org/tr/drafts/color/](https://www.designtokens.org/tr/drafts/color/)  
30. OKLCH Color Picker & Converter, accessed November 8, 2025, [https://oklch.com/](https://oklch.com/)  
31. Expanded support for color spaces \- Tokens Studio, accessed November 8, 2025, [https://feedback.tokens.studio/p/expanded-support-for-color-spaces](https://feedback.tokens.studio/p/expanded-support-for-color-spaces)  
32. Color \- Tokens Studio for Figma, accessed November 8, 2025, [https://docs.tokens.studio/manage-tokens/token-types/color](https://docs.tokens.studio/manage-tokens/token-types/color)  
33. Design Tokens Format Module 2025.10, accessed November 8, 2025, [https://www.designtokens.org/tr/2025.10/format/\#composite-design-token](https://www.designtokens.org/tr/2025.10/format/#composite-design-token)  
34. Design Tokens Format Module 2025.10, accessed November 8, 2025, [https://www.designtokens.org/tr/2025.10/format/\#composite-types](https://www.designtokens.org/tr/2025.10/format/#composite-types)  
35. Design Tokens Community Group \- W3C, accessed November 8, 2025, [https://www.w3.org/community/design-tokens/](https://www.w3.org/community/design-tokens/)  
36. Modes \+ Theming Terrazzo, accessed November 8, 2025, [https://terrazzo.app/docs/guides/modes/](https://terrazzo.app/docs/guides/modes/)