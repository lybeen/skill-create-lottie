---
name: create-lottie
description: Generate valid Lottie JSON animation files from text descriptions. Creates spec-compliant animations with shapes, colors, transforms, and effects.
disable-model-invocation: true
argument-hint: [animation description]
---

# Lottie JSON Animation Generator

Generate a valid Lottie JSON animation file based on the user's description.

## Task

Create a Lottie animation JSON file for: **$ARGUMENTS**

## Instructions

1. **Analyze the request**: Break down the animation description into:
   - Visual elements (shapes, colors, images)
   - Animation behaviors (movement, rotation, scaling, fading)
   - Timing (duration, frame rate)
   - Special effects if needed

2. **Design the structure**:
   - Determine canvas size (default 512x512 unless specified)
   - Calculate frame rate (default 60fps for smooth animations)
   - Decide animation duration in frames
   - Plan layer hierarchy and shape composition

3. **Generate the JSON** following this structure:

```json
{
  "v": "5.12.2",
  "fr": 60,
  "ip": 0,
  "op": 180,
  "w": 512,
  "h": 512,
  "nm": "Animation Name",
  "ddd": 0,
  "assets": [],
  "layers": [...]
}
```

4. **Key requirements**:
   - **Version**: Always use `"v": "5.12.2"`
   - **Colors**: Use `[R, G, B, A]` format with values 0-1 (e.g., red = `[1, 0, 0, 1]`)
   - **Opacity**: 0-100 percentage (e.g., `{"a": 0, "k": 100}` for fully opaque)
   - **Scale**: Percentage (100 = 100%, 200 = 200%)
   - **Position/Anchor**: Pixel values matching canvas dimensions
   - **Frame numbers**: `ip` (in-point), `op` (out-point) define visibility range
   - **Layer order**: First in array = top layer visually

5. **Create meaningful animations**:
   - Use **animated properties** (`"a": 1`) with keyframes for movement
   - Add **easing** for smooth motion (not just linear):
     - Ease in-out: `{"o": {"x": [0.333], "y": [0]}, "i": {"x": [0.667], "y": [1]}}`
     - Linear: `{"o": {"x": [0], "y": [0]}, "i": {"x": [1], "y": [1]}}`
   - Include proper `nm` (name), `mn` (match name), `ix` (index) fields

6. **Shape layer anatomy** (ty=4):
```json
{
  "ddd": 0,
  "ind": 1,
  "ty": 4,
  "nm": "Shape Layer",
  "sr": 1,
  "ks": { /* transform */ },
  "ao": 0,
  "shapes": [
    {
      "ty": "gr",
      "it": [
        { /* shape (rc/el/sh) */ },
        { /* fill/stroke */ },
        { "ty": "tr", /* transform */ }
      ],
      "nm": "Group",
      "np": 3,
      "cix": 2,
      "ix": 1,
      "mn": "ADBE Vector Group"
    }
  ],
  "ip": 0,
  "op": 180,
  "st": 0,
  "bm": 0
}
```

7. **Common shapes**:
   - **Rectangle**: `{"ty": "rc", "p": {...}, "s": {...}, "r": {...}}`
   - **Ellipse**: `{"ty": "el", "p": {...}, "s": {...}}`
   - **Star**: `{"ty": "sr", "sy": 1, "pt": {...}, "or": {...}, "ir": {...}}`
   - **Fill**: `{"ty": "fl", "c": {"a": 0, "k": [1,0,0,1]}, "o": {"a": 0, "k": 100}}`
   - **Stroke**: `{"ty": "st", "c": {...}, "o": {...}, "w": {"a": 0, "k": 3}}`

8. **Reference documentation**:
   - For complete field specifications, see `resources/lottie-spec-reference.md`
   - For effects, masks, and advanced features, consult the reference
   - Use proper layer types: 0=precomp, 1=solid, 2=image, 3=null, 4=shape, 5=text

9. **Save the file**:
   - Write to `[animation-name].json` in the current directory
   - Use a descriptive filename based on the animation content
   - Validate JSON syntax before writing

10. **Output**:
    - Show the user the filename and location
    - Provide a brief description of what was created
    - Suggest testing with an online Lottie player (e.g., lottiefiles.com/preview)

## Example Patterns

**Simple static shape**:
```json
{
  "ty": "gr",
  "it": [
    {"ty": "rc", "p": {"a": 0, "k": [256, 256]}, "s": {"a": 0, "k": [200, 200]}, "r": {"a": 0, "k": 0}},
    {"ty": "fl", "c": {"a": 0, "k": [1, 0, 0, 1]}, "o": {"a": 0, "k": 100}, "r": 1},
    {"ty": "tr", "p": {"a": 0, "k": [0, 0]}, "a": {"a": 0, "k": [0, 0]}, "s": {"a": 0, "k": [100, 100]}, "r": {"a": 0, "k": 0}, "o": {"a": 0, "k": 100}}
  ]
}
```

**Animated position** (moves from [100, 100] to [400, 400]):
```json
{
  "p": {
    "a": 1,
    "k": [
      {"t": 0, "s": [100, 100, 0], "i": {"x": [0.667], "y": [1]}, "o": {"x": [0.333], "y": [0]}},
      {"t": 60, "s": [400, 400, 0]}
    ]
  }
}
```

## Tips

- **Keep it simple**: Start with basic shapes and add complexity gradually
- **Test frequently**: Generate and test in a Lottie player to verify
- **Use meaningful names**: Label layers and groups clearly
- **Layer visibility**: Use `ip` and `op` to control when layers appear
- **Performance**: Avoid excessive keyframes; use simpler animations when possible

Now generate the Lottie JSON file!
