# Architecture

CSS Minecraft is intentionally built without JavaScript.

The world state, block selection, block placement, block removal, camera movement and interaction logic are handled with HTML and CSS.

## Core idea

The project renders every possible editable position in the world as HTML.

Each world position is generated in `index.pug` using three nested loops:

```pug
for _, layer in Array(layers)
  for _, row in Array(rows)
    for _, column in Array(columns)
```

Each generated position represents one possible location in the world grid.

The default world is:

```text
9 layers * 9 rows * 9 columns = 729 positions
```

## Block state

Every position contains a radio group.

That radio group includes one option for each block type:

```text
air
stone
grass
dirt
log
wood
leaves
glass
```

Only one radio input can be checked per position.

This means every position always has a selected state, even when it visually looks empty. Empty space is represented by the `air` block.

## Why air exists

`air` is not a visible cube.

It is used as the empty state for a world position.

When a position has `air` selected, CSS hides that position:

```scss
.cubes-container:has(> .cube.air > input[type=radio]:checked) {
  display: none;
}
```

The inventory shows `air` as a pickaxe, so selecting it works like remove mode.

## How building works

Each solid cube face is a label.

The label does not select the current cube. Instead, it points to a neighboring position.

For example:

```pug
<label for="cube-layer-#{layer}-row-#{row + 1}-column-#{column}-#{block}" class="front"></label>
```

That means clicking the front face of a cube selects the chosen block type in the neighboring position.

This creates the illusion of placing a new block on the face that was clicked.

## How removing works

When the pickaxe is selected, the active block type is `air`.

The CSS hides every cube option except `air`.

Clicking a visible cube face then selects `air` for that position, which makes the block disappear.

## Block selection

The selected inventory block is controlled by this radio group:

```pug
<input type="radio" name="block-type" />
```

CSS uses `:has()` to check which block is currently selected:

```scss
.controls:has(.block-chooser > .stone > input[type=radio]:checked) ~ main .cubes-container > .cube:not(.stone) {
  display: none;
}
```

This keeps only the selected block type clickable in the world.

## Camera movement

Camera movement is handled with paused CSS animations.

Each control button changes the animation state while it is active:

```scss
.controls:has(.up:active) ~ main .down {
  animation-play-state: running;
}
```

The animations are intentionally very long, so holding a button creates continuous motion.

## Performance cost

The project creates a lot of DOM nodes.

The total world positions are calculated as:

```text
layers * rows * columns
```

But each position contains several block options and several labels.

This means the real DOM size is much larger than the number of positions.

For example, with the default world:

```text
9 * 9 * 9 = 729 positions
729 positions * 8 block states = 5,832 cube state containers
Each cube state includes inputs and labels
```

Increasing the world size can quickly make the page slower.

## Safe editing rules

When changing the project, avoid breaking these rules:

1. Keep one radio group per world position.
2. Keep `air` as the empty state.
3. Keep the `id` and `for` naming pattern consistent.
4. Keep the selected inventory block connected to the CSS `:has()` selectors.
5. Do not remove face labels unless the placement logic is redesigned.
6. Do not increase world size aggressively unless testing performance.

## CSS-only limitation

This project is a CSS-only experiment.

Reducing the DOM significantly while keeping the same behavior is difficult without JavaScript, because the browser needs all possible states available in the HTML.

A future JavaScript version could improve performance by:

```text
rendering only visible blocks
saving world state in an object
updating only changed positions
using keyboard and mouse controls
persisting worlds in localStorage
exporting and importing builds
```

That kind of change would be a major version because it changes the core idea of the project.
