# create-lottie

A Claude Code skill for generating valid Lottie JSON animation files from natural language descriptions.

## Overview

This skill enables Claude to create Lottie animations programmatically by converting text descriptions into spec-compliant Lottie JSON files. It handles shapes, colors, transforms, keyframe animations, and effects according to the Lottie specification v5.12.2.

## Usage

Invoke the skill using the `/create-lottie` command followed by your animation description:

```
/create-lottie a red circle that bounces up and down
```

```
/create-lottie spinning blue star with a yellow glow effect
```

```
/create-lottie loading animation with three dots fading in sequence
```

## What It Can Do

### Visual Elements
- **Shapes**: Rectangles, ellipses, stars, polygons, custom paths
- **Colors**: Full RGB/RGBA support with opacity control
- **Effects**: Gradients, strokes, fills, shadows (via effects layer)
- **Text**: Basic text layers with styling

### Animations
- **Transforms**: Position, rotation, scale, skew
- **Property animations**: Opacity, color changes, morphing
- **Easing**: Linear, ease-in, ease-out, ease-in-out, custom bezier curves
- **Timing**: Configurable frame rate and duration

### Advanced Features
- **Layer composition**: Multiple layers with proper z-ordering
- **Visibility control**: Layers can appear/disappear at specific times
- **Anchor points**: Control rotation and scaling origins
- **Masks**: Shape-based layer masking

## Examples

### Simple Static Shape
```
/create-lottie a 200x200 red square centered on a 512x512 canvas
```
Creates a static red square with no animation.

### Basic Animation
```
/create-lottie a green circle that moves from left to right over 3 seconds
```
Creates a smooth horizontal movement animation with ease-in-out timing.

### Complex Multi-Element Animation
```
/create-lottie three colored dots (red, blue, yellow) that fade in one after another, then all fade out together. 2 seconds total.
```
Creates a sequenced animation with multiple elements and timing coordination.

### Logo Animation
```
/create-lottie a checkmark that draws itself in, starting from the bottom. Green color, 1.5 seconds duration.
```
Creates a path-based drawing animation (stroke reveal effect).

## Technical Details

### Output Format
- **File format**: JSON (`.json` extension)
- **Lottie version**: 5.12.2
- **Default canvas**: 512x512 pixels
- **Default frame rate**: 60 fps
- **Color format**: `[R, G, B, A]` with values 0-1

### File Structure
Generated files follow the standard Lottie specification:
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

### Layer Types
- **Type 0**: Precomposition
- **Type 1**: Solid color
- **Type 2**: Image
- **Type 3**: Null object (for parenting)
- **Type 4**: Shape layer (most common)
- **Type 5**: Text layer

## Testing Your Animation

After generation, test your Lottie file using:

1. **LottieFiles Preview**: https://lottiefiles.com/preview
2. **Lottie Editor**: https://lottiefiles.com/editor

## Tips for Best Results

### Be Specific
- Specify colors, sizes, and durations explicitly
- Describe the animation behavior clearly (ease-in, bounce, linear)
- Mention canvas size if different from default (512x512)

### Start Simple
- Begin with basic shapes and movements
- Add complexity incrementally
- Test each version before adding more features

### Animation Guidelines
- **Duration**: 1-5 seconds for most UI animations
- **Frame rate**: 60fps for smooth motion, 30fps for simpler animations
- **Easing**: Use ease-in-out for natural motion, linear for mechanical effects
- **Layers**: Keep under 10 layers for optimal performance

### Performance Considerations
- Fewer keyframes = better performance
- Avoid excessive vector detail in paths
- Use precompositions for repeated elements
- Consider file size for mobile applications

## Customization

You can customize various aspects by specifying them in your description:

- **Canvas size**: "on a 1920x1080 canvas"
- **Frame rate**: "at 30 frames per second"
- **Duration**: "2.5 seconds long"
- **Colors**: "bright red (#FF0000)" or "semi-transparent blue"
- **Timing**: "ease-in animation" or "with bounce effect"

## Reference Documentation

For detailed Lottie specification information, see:
- `resources/lottie-spec-reference.md` - Complete field specifications
- [Official Lottie Docs](https://lottiefiles.github.io/lottie-docs/)
- [Lottie Spec on GitHub](https://github.com/airbnb/lottie-web)

## Limitations

- Cannot import external images or fonts (uses embedded data only)
- Complex path morphing may require manual refinement
- No audio support (Lottie limitation)
- Generated animations are code-based, not design-tool exports
- May not support all advanced After Effects features

## Integration

This skill is particularly useful for:
- Creating test assets for the playground apps
- Rapid prototyping of UI animations
- Generating animation test cases

## Troubleshooting

### Animation doesn't play
- Check `ip` (in-point) and `op` (out-point) values
- Verify frame numbers are within range
- Ensure animated properties have `"a": 1` flag

### Colors look wrong
- Remember: Lottie uses 0-1 range, not 0-255
- Check alpha channel (4th value in color array)
- Verify color format: `[R, G, B, A]`

### Shapes not visible
- Check layer opacity (`o` property)
- Verify fill/stroke has proper opacity
- Ensure layer is within visibility range (`ip`/`op`)

### Animation timing off
- Verify frame rate (`fr`) matches expectations
- Check keyframe timing values (`t` property)
- Ensure easing curves are properly defined

## Contributing

To improve this skill:
1. Update `SKILL.md` for behavior changes
2. Expand `resources/lottie-spec-reference.md` for new features
3. Test with real-world animation scenarios
4. Document edge cases and limitations
