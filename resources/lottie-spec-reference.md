# Lottie JSON Format Specification Reference

Complete reference for generating valid Lottie animation files (version 5.12.2).

## Table of Contents

1. [Top-Level Animation Object](#top-level-animation-object)
2. [Layer Types](#layer-types)
3. [Shape Elements](#shape-elements)
4. [Animated Properties](#animated-properties)
5. [Transform Object](#transform-object)
6. [Keyframe Easing](#keyframe-easing)
7. [Assets](#assets)
8. [Masks](#masks)
9. [Effects](#effects)
10. [Layer Styles](#layer-styles)
11. [Constants & Enumerations](#constants--enumerations)

---

## Top-Level Animation Object

The root JSON object representing the entire animation.

### Required Fields

```json
{
  "v": "5.12.2",        // Bodymovin version (string)
  "fr": 60,             // Frame rate (number, fps)
  "ip": 0,              // In-point: first frame (number)
  "op": 180,            // Out-point: last frame (number, exclusive)
  "w": 512,             // Width in pixels (number)
  "h": 512,             // Height in pixels (number)
  "nm": "My Animation", // Name (string, optional)
  "ddd": 0,             // 3D flag: 0=2D, 1=3D (0 or 1)
  "assets": [],         // Asset definitions (array)
  "layers": [],         // Layer array (array, required)
  "fonts": {},          // Font list (object, optional)
  "chars": [],          // Character data for text (array, optional)
  "markers": [],        // Named markers (array, optional)
  "meta": {}            // Metadata (object, optional)
}
```

**Duration calculation**: `(op - ip) / fr` seconds
- Example: `ip=0, op=180, fr=60` → 3 seconds

### Markers

```json
{
  "tm": 40,          // Time (frame number)
  "cm": "Marker 1",  // Comment/name
  "dr": 0            // Duration in frames
}
```

---

## Layer Types

All layers have a `ty` field indicating their type:

| `ty` | Type | Description |
|------|------|-------------|
| `0` | Precomposition | References another composition via `refId` |
| `1` | Solid | Solid color rectangle |
| `2` | Image | References an image asset via `refId` |
| `3` | Null | Invisible parent layer |
| `4` | Shape | Vector shape layer (most common) |
| `5` | Text | Text rendering layer |
| `6` | Audio | Audio layer |
| `13` | Camera | 3D camera |

### Common Layer Properties

All layers share these properties:

```json
{
  "ddd": 0,           // 3D layer flag (0 or 1)
  "ind": 1,           // Layer index (integer, unique)
  "ty": 4,            // Layer type (integer)
  "nm": "Layer Name", // Name (string)
  "sr": 1,            // Time stretch (number, 1.0 = normal)
  "ks": {},           // Transform object (see Transform section)
  "ao": 0,            // Auto-orient along path (0 or 1)
  "ip": 0,            // In-point frame (number)
  "op": 180,          // Out-point frame (number)
  "st": 0,            // Start time offset (number)
  "bm": 0,            // Blend mode (integer, see constants)
  "parent": 2,        // Parent layer index (integer, optional)
  "cl": "css-class",  // CSS class (string, optional)
  "hd": false,        // Hidden (boolean)
  "ef": [],           // Effects array (optional)
  "sy": [],           // Layer styles array (optional)
  "masksProperties": [] // Masks array (optional)
}
```

### Shape Layer (ty=4)

```json
{
  "ty": 4,
  "shapes": [       // Array of shape elements
    {
      "ty": "gr",   // Group
      "it": [...]   // Group items
    }
  ]
}
```

### Solid Layer (ty=1)

```json
{
  "ty": 1,
  "sw": 512,           // Solid width (number)
  "sh": 512,           // Solid height (number)
  "sc": "#FF0000"      // Solid color (hex string)
}
```

### Precomp Layer (ty=0)

```json
{
  "ty": 0,
  "refId": "comp_0",  // Asset ID (string)
  "w": 512,           // Width (number)
  "h": 512,           // Height (number)
  "tm": {}            // Time remap (optional animated property)
}
```

### Image Layer (ty=2)

```json
{
  "ty": 2,
  "refId": "image_0"  // Asset ID (string)
}
```

---

## Shape Elements

Shapes define vector graphics. Each shape has a `ty` field:

| `ty` | Name | Description |
|------|------|-------------|
| `"gr"` | Group | Container for shapes + transform |
| `"sh"` | Path | Bezier path |
| `"rc"` | Rectangle | Rectangle shape |
| `"el"` | Ellipse | Ellipse shape |
| `"sr"` | Polystar | Star or polygon |
| `"fl"` | Fill | Solid fill style |
| `"st"` | Stroke | Solid stroke style |
| `"gf"` | Gradient Fill | Gradient fill |
| `"gs"` | Gradient Stroke | Gradient stroke |
| `"tr"` | Transform | Shape transform (must be last in group) |
| `"tm"` | Trim Path | Trim paths modifier |
| `"rd"` | Round Corners | Rounds corners |
| `"rp"` | Repeater | Repeats shapes |
| `"mm"` | Merge | Boolean operations |

### Group (`"gr"`)

```json
{
  "ty": "gr",
  "it": [              // Items array (shapes, styles, transform)
    {"ty": "rc", ...}, // Shape
    {"ty": "fl", ...}, // Fill
    {"ty": "tr", ...}  // Transform (MUST be last)
  ],
  "nm": "Group 1",
  "np": 3,             // Number of properties
  "cix": 2,
  "ix": 1,
  "mn": "ADBE Vector Group",
  "hd": false
}
```

### Rectangle (`"rc"`)

```json
{
  "ty": "rc",
  "d": 1,                          // Direction: 1=CW, 3=CCW
  "p": {"a": 0, "k": [256, 256]},  // Position
  "s": {"a": 0, "k": [200, 100]},  // Size [width, height]
  "r": {"a": 0, "k": 10},          // Corner roundness
  "nm": "Rectangle",
  "mn": "ADBE Vector Shape - Rect",
  "hd": false
}
```

### Ellipse (`"el"`)

```json
{
  "ty": "el",
  "d": 1,
  "p": {"a": 0, "k": [256, 256]},  // Position (center)
  "s": {"a": 0, "k": [200, 200]},  // Size [width, height]
  "nm": "Ellipse",
  "mn": "ADBE Vector Shape - Ellipse",
  "hd": false
}
```

### Polystar (`"sr"`)

```json
{
  "ty": "sr",
  "sy": 1,                          // Star type: 1=star, 2=polygon
  "p": {"a": 0, "k": [256, 256]},   // Position
  "pt": {"a": 0, "k": 5},           // Points count
  "r": {"a": 0, "k": 0},            // Rotation (degrees)
  "or": {"a": 0, "k": 100},         // Outer radius
  "os": {"a": 0, "k": 0},           // Outer roundness
  "ir": {"a": 0, "k": 50},          // Inner radius (star only)
  "is": {"a": 0, "k": 0},           // Inner roundness (star only)
  "d": 1,
  "nm": "Star"
}
```

### Path (`"sh"`)

```json
{
  "ty": "sh",
  "ind": 0,
  "ks": {
    "a": 0,
    "k": {
      "i": [[0, 0], [0, 0]],      // In-tangents (relative to vertex)
      "o": [[0, 0], [0, 0]],      // Out-tangents (relative to vertex)
      "v": [[100, 100], [200, 200]], // Vertices
      "c": true                    // Closed path
    }
  },
  "nm": "Path",
  "mn": "ADBE Vector Shape - Group",
  "hd": false
}
```

### Fill (`"fl"`)

```json
{
  "ty": "fl",
  "c": {"a": 0, "k": [1, 0, 0, 1]},  // Color [R,G,B,A] (0-1)
  "o": {"a": 0, "k": 100},            // Opacity (0-100)
  "r": 1,                              // Fill rule: 1=non-zero, 2=even-odd
  "nm": "Fill",
  "mn": "ADBE Vector Graphic - Fill",
  "hd": false
}
```

### Stroke (`"st"`)

```json
{
  "ty": "st",
  "c": {"a": 0, "k": [0, 0, 0, 1]},  // Color
  "o": {"a": 0, "k": 100},            // Opacity
  "w": {"a": 0, "k": 3},              // Width
  "lc": 2,                             // Line cap: 1=butt, 2=round, 3=square
  "lj": 2,                             // Line join: 1=miter, 2=round, 3=bevel
  "ml": 4,                             // Miter limit
  "nm": "Stroke",
  "mn": "ADBE Vector Graphic - Stroke",
  "hd": false
}
```

### Gradient Fill (`"gf"`)

```json
{
  "ty": "gf",
  "o": {"a": 0, "k": 100},            // Opacity
  "r": 1,                              // Fill rule
  "t": 1,                              // Type: 1=linear, 2=radial
  "s": {"a": 0, "k": [100, 100]},     // Start point
  "e": {"a": 0, "k": [400, 400]},     // End point
  "g": {                               // Gradient
    "p": 3,                            // Number of color stops
    "k": {
      "a": 0,
      "k": [
        0, 1, 0, 0,      // Stop 0: position, R, G, B
        0.5, 0, 1, 0,    // Stop 0.5
        1, 0, 0, 1       // Stop 1
      ]
    }
  },
  "nm": "Gradient Fill"
}
```

### Shape Transform (`"tr"`)

**Must be the last item in every group's `it` array.**

```json
{
  "ty": "tr",
  "p": {"a": 0, "k": [0, 0]},        // Position
  "a": {"a": 0, "k": [0, 0]},        // Anchor point
  "s": {"a": 0, "k": [100, 100]},    // Scale (percentage)
  "r": {"a": 0, "k": 0},              // Rotation (degrees)
  "o": {"a": 0, "k": 100},            // Opacity (0-100)
  "sk": {"a": 0, "k": 0},             // Skew
  "sa": {"a": 0, "k": 0},             // Skew axis
  "nm": "Transform"
}
```

### Trim Path (`"tm"`)

```json
{
  "ty": "tm",
  "s": {"a": 0, "k": 0},     // Start (0-100%)
  "e": {"a": 0, "k": 50},    // End (0-100%)
  "o": {"a": 0, "k": 0},     // Offset (0-360 degrees)
  "m": 1,                     // Multiple shapes: 1=simultaneously, 2=individually
  "nm": "Trim Paths"
}
```

### Repeater (`"rp"`)

```json
{
  "ty": "rp",
  "c": {"a": 0, "k": 5},     // Copies
  "o": {"a": 0, "k": 0},     // Offset
  "m": 1,                     // Composite: 1=above, 2=below
  "tr": {                     // Transform per copy
    "p": {"a": 0, "k": [50, 0]},
    "a": {"a": 0, "k": [0, 0]},
    "s": {"a": 0, "k": [100, 100]},
    "r": {"a": 0, "k": 30},
    "so": {"a": 0, "k": 100}, // Start opacity
    "eo": {"a": 0, "k": 50}   // End opacity
  },
  "nm": "Repeater"
}
```

---

## Animated Properties

Properties can be static or animated.

### Static Property

```json
{
  "a": 0,         // Animated flag: 0 = static
  "k": 100,       // Value (number, array, or object)
  "ix": 2         // Property index (optional)
}
```

Value types:
- **Scalar**: `"k": 100`
- **2D Vector**: `"k": [256, 256]`
- **3D Vector**: `"k": [256, 256, 0]`
- **Color**: `"k": [1, 0, 0, 1]` (RGBA, 0-1 range)

### Animated Property

```json
{
  "a": 1,         // Animated flag: 1 = animated
  "k": [          // Array of keyframes
    {
      "t": 0,           // Time (frame number)
      "s": [100, 100],  // Start value at this keyframe
      "i": {"x": [0.667], "y": [1]},     // In-tangent (easing)
      "o": {"x": [0.333], "y": [0]},     // Out-tangent (easing)
      "to": [10, 0, 0], // Spatial out-tangent (position only)
      "ti": [0, 10, 0]  // Spatial in-tangent (position only)
    },
    {
      "t": 60,
      "s": [400, 400]   // End value (no easing for last keyframe)
    }
  ],
  "ix": 2
}
```

**Hold keyframe** (step, no interpolation):
```json
{
  "t": 30,
  "s": [200, 200],
  "h": 1            // Hold flag
}
```

---

## Transform Object

Every layer has a `ks` property containing its transform.

### 2D Transform

```json
{
  "ks": {
    "o": {"a": 0, "k": 100, "ix": 11},    // Opacity (0-100)
    "r": {"a": 0, "k": 0, "ix": 10},       // Rotation (degrees)
    "p": {"a": 0, "k": [256, 256, 0], "ix": 2}, // Position [x, y, z]
    "a": {"a": 0, "k": [0, 0, 0], "ix": 1},     // Anchor point
    "s": {"a": 0, "k": [100, 100, 100], "ix": 6} // Scale (percentage)
  }
}
```

### 3D Transform (when `ddd: 1`)

```json
{
  "ks": {
    "o": {"a": 0, "k": 100, "ix": 11},
    "rx": {"a": 0, "k": 0, "ix": 8},        // X rotation (degrees)
    "ry": {"a": 0, "k": 0, "ix": 9},        // Y rotation (degrees)
    "rz": {"a": 0, "k": 0, "ix": 10},       // Z rotation (degrees)
    "or": {"a": 0, "k": [0, 0, 0], "ix": 7}, // Orientation [rx, ry, rz]
    "p": {"a": 0, "k": [256, 256, 0], "ix": 2},
    "a": {"a": 0, "k": [0, 0, 0], "ix": 1},
    "s": {"a": 0, "k": [100, 100, 100], "ix": 6}
  }
}
```

### Split Position

Position can be split into separate X/Y animated properties:

```json
{
  "p": {
    "s": true,                    // Split flag
    "x": {"a": 1, "k": [...]},   // X position (animated)
    "y": {"a": 0, "k": 256}      // Y position (static)
  }
}
```

---

## Keyframe Easing

Easing curves are cubic beziers from `[0,0]` to `[1,1]`.

### Easing Handles

- **X axis**: Time (0 = current keyframe, 1 = next keyframe)
- **Y axis**: Value interpolation (0 = current value, 1 = next value)

### Common Easing Presets

**Linear**:
```json
{
  "o": {"x": [0], "y": [0]},
  "i": {"x": [1], "y": [1]}
}
```

**Ease In-Out** (smooth):
```json
{
  "o": {"x": [0.333], "y": [0]},
  "i": {"x": [0.667], "y": [1]}
}
```

**Ease In**:
```json
{
  "o": {"x": [0.42], "y": [0]},
  "i": {"x": [1], "y": [1]}
}
```

**Ease Out**:
```json
{
  "o": {"x": [0], "y": [0]},
  "i": {"x": [0.58], "y": [1]}
}
```

**Multi-dimensional** (per-component easing):
```json
{
  "o": {"x": [0.333, 0.333, 0.333], "y": [0, 0, 0]},
  "i": {"x": [0.667, 0.667, 0.667], "y": [1, 1, 1]}
}
```

---

## Assets

Assets are reusable resources referenced by layers.

### Image Asset

```json
{
  "id": "image_0",          // Unique ID
  "w": 512,                 // Width
  "h": 512,                 // Height
  "u": "images/",           // Path prefix
  "p": "image.png",         // Filename
  "e": 0                    // Embedded: 0=external, 1=base64 data URI
}
```

**Embedded image** (`e: 1`):
```json
{
  "id": "image_0",
  "w": 512,
  "h": 512,
  "u": "",
  "p": "data:image/png;base64,iVBORw0KGgo...",
  "e": 1
}
```

### Precomposition Asset

```json
{
  "id": "comp_0",
  "nm": "Precomp Name",
  "fr": 60,               // Frame rate (optional)
  "layers": [...]         // Layers array (same format as top-level)
}
```

---

## Masks

Masks clip layer content to a shape.

### Mask Object

```json
{
  "inv": false,           // Inverted (boolean)
  "mode": "a",            // Mask mode (see constants)
  "pt": {                 // Mask path (animated bezier)
    "a": 0,
    "k": {
      "i": [[0, 0], ...],
      "o": [[0, 0], ...],
      "v": [[x1, y1], ...],
      "c": true
    }
  },
  "o": {"a": 0, "k": 100, "ix": 3},  // Mask opacity (0-100)
  "x": {"a": 0, "k": 0, "ix": 4},    // Mask expansion
  "nm": "Mask 1"
}
```

### Mask Modes

| Mode | Name | Description |
|------|------|-------------|
| `"n"` | None | No mask |
| `"a"` | Add | Additive mask |
| `"s"` | Subtract | Subtractive mask |
| `"i"` | Intersect | Intersection |
| `"l"` | Lighten | Lighten blend |
| `"d"` | Darken | Darken blend |
| `"f"` | Difference | Difference blend |

---

## Effects

Effects are post-processing applied to layers via the `ef` array.

### Effect Object

```json
{
  "ty": 21,               // Effect type (integer)
  "nm": "Fill Effect",    // Name
  "np": 7,                // Number of parameters
  "mn": "ADBE Fill",      // Match name
  "ix": 1,
  "en": 1,                // Enabled (0 or 1)
  "ef": [...]             // Effect parameters
}
```

### Effect Types

| `ty` | Name | Description |
|------|------|-------------|
| `21` | Fill | Fill all opaque areas with color |
| `22` | Stroke | Stroke effect |
| `23` | Tint | Tint effect |
| `24` | Tritone | Tritone color mapping |
| `25` | Drop Shadow | Drop shadow |
| `26` | Radial Wipe | Radial wipe transition |
| `27` | Displacement Map | Displacement map |
| `28` | Matte3 | Set matte effect |
| `29` | Gaussian Blur | Gaussian blur |
| `30` | Twirl | Twirl distortion |
| `31` | Mesh Warp | Mesh warp |
| `32` | Wavy | Wavy distortion |
| `33` | Spherize | Spherize distortion |
| `34` | Puppet | Puppet deformation |
| `20` | Tint (old) | Legacy tint |
| `0` | Custom | Custom effect (ty=5 is also custom) |

### Effect Value Types

| `ty` | Type | Description |
|------|------|-------------|
| `0` | Slider | Scalar number |
| `1` | Angle | Angle value |
| `2` | Color | Color value |
| `3` | Point | 2D position |
| `4` | Checkbox | Boolean (0 or 1) |
| `6` | Ignored | Placeholder |
| `7` | Drop-down | Enum selection |
| `10` | Layer | Layer reference |

### Example: Fill Effect (ty=21)

```json
{
  "ty": 21,
  "nm": "Fill",
  "ef": [
    {"ty": 10, "nm": "10", "v": {...}},           // Layer (unused)
    {"ty": 7, "nm": "01", "v": {...}},            // Drop-down
    {"ty": 2, "nm": "color", "v": {"a": 0, "k": [1, 0.9, 0, 1]}},
    {"ty": 7, "nm": "03", "v": {...}},            // Drop-down
    {"ty": 0, "nm": "04", "v": {...}},            // Slider
    {"ty": 0, "nm": "05", "v": {...}},            // Slider
    {"ty": 0, "nm": "opacity", "v": {"a": 0, "k": 1}} // Opacity (0-1)
  ]
}
```

### Example: Drop Shadow Effect (ty=25)

```json
{
  "ty": 25,
  "nm": "Drop Shadow",
  "ef": [
    {"ty": 2, "nm": "color", "v": {"a": 0, "k": [0, 0, 0, 1]}},
    {"ty": 0, "nm": "opacity", "v": {"a": 0, "k": 128}},  // 0-255
    {"ty": 1, "nm": "angle", "v": {"a": 0, "k": 135}},
    {"ty": 0, "nm": "distance", "v": {"a": 0, "k": 10}},
    {"ty": 0, "nm": "blur", "v": {"a": 0, "k": 7}}
  ]
}
```

### Example: Gaussian Blur Effect (ty=29)

```json
{
  "ty": 29,
  "nm": "Gaussian Blur",
  "ef": [
    {"ty": 0, "nm": "sigma", "v": {"a": 0, "k": 25}},
    {"ty": 0, "nm": "direction", "v": {"a": 0, "k": 1}}, // 1=both, 2=horiz, 3=vert
    {"ty": 4, "nm": "wrap", "v": {"a": 0, "k": 0}}
  ]
}
```

---

## Layer Styles

Layer styles (`sy` array) apply visual effects similar to Photoshop layer styles.

### Layer Style Object

```json
{
  "ty": 0,              // Style type
  "nm": "Style Name",
  "v": {...}            // Style-specific parameters
}
```

### Layer Style Types

| `ty` | Name | Description |
|------|------|-------------|
| `0` | Stroke | Stroke style |
| `1` | Drop Shadow | Drop shadow |
| `2` | Inner Shadow | Inner shadow |
| `3` | Outer Glow | Outer glow |
| `4` | Inner Glow | Inner glow |
| `5` | Bevel/Emboss | Bevel and emboss |
| `6` | Satin | Satin effect |
| `7` | Color Overlay | Color overlay |
| `8` | Gradient Overlay | Gradient overlay |

### Example: Drop Shadow Style

```json
{
  "ty": 1,
  "nm": "Drop Shadow",
  "c": {"a": 0, "k": [0, 0, 0, 1]},    // Color
  "o": {"a": 0, "k": 0.75},             // Opacity (0-1)
  "a": {"a": 0, "k": 135},              // Angle
  "d": {"a": 0, "k": 5},                // Distance
  "s": {"a": 0, "k": 5},                // Size
  "ch": {"a": 0, "k": 0},               // Choke
  "bl": {"a": 0, "k": 10},              // Blur
  "no": {"a": 0, "k": 0}                // Noise
}
```

---

## Constants & Enumerations

### Blend Modes (`bm`)

| Value | Name |
|-------|------|
| `0` | Normal |
| `1` | Multiply |
| `2` | Screen |
| `3` | Overlay |
| `4` | Darken |
| `5` | Lighten |
| `6` | Color Dodge |
| `7` | Color Burn |
| `8` | Hard Light |
| `9` | Soft Light |
| `10` | Difference |
| `11` | Exclusion |
| `12` | Hue |
| `13` | Saturation |
| `14` | Color |
| `15` | Luminosity |

### Line Cap (`lc`)

| Value | Name |
|-------|------|
| `1` | Butt |
| `2` | Round |
| `3` | Square |

### Line Join (`lj`)

| Value | Name |
|-------|------|
| `1` | Miter |
| `2` | Round |
| `3` | Bevel |

### Fill Rule (`r`)

| Value | Name |
|-------|------|
| `1` | Non-zero winding |
| `2` | Even-odd |

### Gradient Type (`t`)

| Value | Name |
|-------|------|
| `1` | Linear |
| `2` | Radial |

### Matte Mode (`tt`)

| Value | Name |
|-------|------|
| `0` | None |
| `1` | Alpha |
| `2` | Alpha Inverted |
| `3` | Luma |
| `4` | Luma Inverted |

### Composite (`m` on repeater)

| Value | Name |
|-------|------|
| `1` | Above |
| `2` | Below |

### Trim Multiple Shapes (`m` on trim path)

| Value | Name |
|-------|------|
| `1` | Simultaneously |
| `2` | Individually |

### Shape Direction (`d`)

| Value | Name |
|-------|------|
| `1` | Clockwise |
| `3` | Counter-clockwise |

---

## Color Format

**All colors use RGBA arrays with 0-1 range**:
```json
[1, 0, 0, 1]           // Red
[0, 1, 0, 1]           // Green
[0, 0, 1, 1]           // Blue
[1, 0.9, 0, 1]         // Yellow
[0.5, 0.5, 0.5, 1]     // Gray
```

**Opacity** is separate (0-100 percentage):
```json
{"a": 0, "k": 100}     // Fully opaque
{"a": 0, "k": 50}      // 50% transparent
```

---

## Quick Reference Card

### Minimal Valid Animation

```json
{
  "v": "5.12.2",
  "fr": 60,
  "ip": 0,
  "op": 60,
  "w": 512,
  "h": 512,
  "ddd": 0,
  "assets": [],
  "layers": [
    {
      "ddd": 0,
      "ind": 1,
      "ty": 4,
      "nm": "Shape",
      "sr": 1,
      "ks": {
        "o": {"a": 0, "k": 100},
        "r": {"a": 0, "k": 0},
        "p": {"a": 0, "k": [256, 256, 0]},
        "a": {"a": 0, "k": [0, 0, 0]},
        "s": {"a": 0, "k": [100, 100, 100]}
      },
      "ao": 0,
      "shapes": [
        {
          "ty": "gr",
          "it": [
            {
              "ty": "rc",
              "p": {"a": 0, "k": [0, 0]},
              "s": {"a": 0, "k": [100, 100]},
              "r": {"a": 0, "k": 0}
            },
            {
              "ty": "fl",
              "c": {"a": 0, "k": [1, 0, 0, 1]},
              "o": {"a": 0, "k": 100},
              "r": 1
            },
            {
              "ty": "tr",
              "p": {"a": 0, "k": [0, 0]},
              "a": {"a": 0, "k": [0, 0]},
              "s": {"a": 0, "k": [100, 100]},
              "r": {"a": 0, "k": 0},
              "o": {"a": 0, "k": 100}
            }
          ],
          "nm": "Rectangle",
          "np": 3,
          "cix": 2,
          "ix": 1,
          "mn": "ADBE Vector Group"
        }
      ],
      "ip": 0,
      "op": 60,
      "st": 0,
      "bm": 0
    }
  ]
}
```

---

**End of Specification**
