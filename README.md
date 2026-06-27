# Lifespan

A demographic cellular automaton. Conway's cells are timeless — a live cell is identical to every other live cell, forever. In **Lifespan**, every cell carries a private clock: it is born juvenile, must reach **maturity** before it can help create new life, and eventually dies of **old age**. Crowding doesn't kill; time does.

The result is a class of emergence you can't get from a binary rule. Instead of gliders and still-lifes, the moving objects are **demographic cohorts** — booms, die-offs, and traveling waves of age that sweep across the grid as a color gradient.

[**▶ Live demo**](#) · single self-contained HTML file, no build step, no dependencies.

![juvenile → mature → senescent → dying](https://img.shields.io/badge/cohorts-not%20gliders-c2410c)

## The idea

Standard "Life-like" automata encode their whole rule in two numbers (Conway is `B3/S23`): a dead cell's fate and a live cell's fate, both decided purely by a neighbor count. Every cell is in one of two states, so the only thing that can travel across the grid is a *spatial* pattern.

Lifespan adds an orthogonal axis — an integer **age** per cell, with two thresholds:

| Term | Meaning |
|------|---------|
| **M** — maturity age | A cell only counts as a *parent* once `age ≥ M`. Young cells are alive but infertile. |
| **L** — lifespan | A cell dies once `age > L`, regardless of its neighbors. |
| **B** — birth window | An empty cell becomes a juvenile when it has the right number of *mature* neighbors. |

Because young colonies can't reproduce, the system **self-paces**. A dense cluster can't explode until it grows up, then reproduces in synchrony, then dies in synchrony — producing rolling generational pulses and age-gradients rather than static structures.

## Rules (per step)

For every cell, count its 8 neighbors (toroidal wrap), tracking how many are alive and how many are *mature* (`age ≥ M`):

```
if cell is alive (age > 0):
    if age > L          -> dies of old age      (0)
    elif no live nbrs   -> dies of isolation     (0)
    else                -> ages by one      (age + 1)

if cell is dead (age == 0):
    if mature_nbrs in [B-1 .. B+1] -> born juvenile (1)
    else                           -> stays dead    (0)
```

That's it. The only addition over Conway is that the state is an integer age instead of a bit, and birth is gated on *mature* neighbors rather than any live neighbor.

## Color = age

The whole point is visible in the palette. Cells are tinted by their cohort, so you watch generations move:

- 🟦 **juvenile** — alive but can't reproduce
- 🟨 **mature** — fertile
- 🟧 **aging**
- 🟥 **near death**

## Things to try

- **Open M and L far apart** (e.g. `M=3`, `L=40`) → slow, rolling generational pulses.
- **Bring M and L together** (e.g. `M=5`, `L=6`) → the world flickers between boom and extinction.
- **Nursery seed** → plants a synchronized block of mature cells so you can watch one cohort age and collapse as a single unit.
- **Cohort seed** → a horizontal line of mixed ages that spreads as an age wave.
- **Click / drag** on the grid to paint mature cells live.

## Run it

No build step. Either:

- Open `lifespan.html` directly in a browser, **or**
- Serve the folder: `python3 -m http.server` then visit `http://localhost:8000/lifespan.html`

## Tech

Vanilla HTML/CSS/JS. The grid is a flat `Uint16Array` (age per cell), rendered straight to a `<canvas>` via `putImageData` for speed, with a double-buffered step. ~120×120 toroidal world. No dependencies, no framework.

## License

MIT — do whatever you like. Fork it and mutate the rule.
