# Focus Tokens

Design tokens file following the Design Tokens Community Group (DTCG) 2025.10 specification.

## Overview

This repository contains design tokens with the following naming conventions:
- **Primitive tokens**: Base design values (colors, fonts, shapes, dimensions)
- **Semantic tokens**: Contextual tokens that reference primitives (breakpoints, modes, typography)

## Structure

### Primitive Tokens
- `primitive/brand-a/color` - Brand A color palette and roles
- `primitive/brand-a/font` - Brand A typography tokens
- `primitive/brand-a/shape` - Brand A shape/border radius tokens
- `primitive/dimension` - Base dimension/spacing tokens

### Semantic Tokens
- `semantic/breakpoint/screen-lg-*` - Large screen breakpoint tokens
- `semantic/font` - Semantic typography tokens
- `semantic/mode/color-dark` - Dark mode color tokens
- `semantic/mode/color-light` - Light mode color tokens

### Components
- `components/section-message` - Section message component tokens

## Specification Compliance

This file adheres to the [DTCG 2025.10 specification](https://www.designtokens.org/tr/2025.10/).

## Usage

The tokens file can be used with design token tools such as:
- Style Dictionary
- Theo
- Design tokens plugins for Figma, Sketch, etc.

## File Format

The tokens are stored in JSON format following the DTCG specification structure with:
- `$type` - Token type (color, dimension, typography, etc.)
- `$value` - Token value (can be a direct value or reference using `{namespace.path}` syntax)
- `$description` - Optional description of the token
- `$extensions` - Optional extensions for tool-specific metadata

## Notes

- Brand B tokens have been removed from this file
- All references use the `primitive.*` and `semantic.*` naming convention
- The file includes theme configurations in the `$themes` section
- Metadata including `tokenSetOrder` is maintained for tool compatibility

