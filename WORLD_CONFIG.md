# World Configuration

This project renders the Minecraft-style world directly in the DOM using Pug and CSS.

There is no JavaScript managing the world state. Every editable position is generated in `index.pug`, and every possible block type is represented with radio inputs and labels.

## Main configuration

The world size is configured at the top of `index.pug`.

The active preset is selected here:

```pug
- const activeWorldPreset = "default";
```

The available presets are defined here:

```pug
- const worldPresets = {
-   small: { layers: 7, rows: 7, columns: 7 },
-   default: { layers: 9, rows: 9, columns: 9 },
-   large: { layers: 11, rows: 11, columns: 11 },
-   experimental: { layers: 13, rows: 13, columns: 13 }
- };
```

These values mean:

- `layers`: vertical height of the world.
- `rows`: depth of the world.
- `columns`: width of the world.

The default world is:

```text
9 layers * 9 rows * 9 columns = 729 world positions
```

## Recommended presets

Small:

```pug
- const activeWorldPreset = "small";
```

Use this for low-end devices, quick testing or when performance is more important than world size.

Default:

```pug
- const activeWorldPreset = "default";
```

Use this as the balanced preset. It is the recommended option for normal development.

Large:

```pug
- const activeWorldPreset = "large";
```

Use this when you want more build space and are testing on a capable device.

Experimental:

```pug
- const activeWorldPreset = "experimental";
```

Use this only for stress testing. It can become noticeably heavier depending on the browser and device.

## Why size matters

This project is intentionally built without JavaScript, so the page needs to create the full interactive world in HTML.

Each world position creates:

- one radio group
- one `air` cube
- one cube for each placeable block
- labels for all cube faces

That means increasing the world size grows the DOM very quickly.

For example:

```text
7 * 7 * 7 = 343 positions
9 * 9 * 9 = 729 positions
11 * 11 * 11 = 1,331 positions
13 * 13 * 13 = 2,197 positions
```

Since every position includes multiple block options and multiple labels, the real DOM node count is much higher than the position count.

## Recommended rule

Keep the default world at `9 x 9 x 9` unless the goal is specifically to test performance or scalability.

For larger worlds, a future JavaScript version would be more appropriate because it could render only the visible or changed blocks instead of generating every possible block state in advance.
