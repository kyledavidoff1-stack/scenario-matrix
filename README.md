# Technology Development Scenarios

A single-page, dependency-free scenario tool. Set fifteen world levers and the chart shows which
of twenty-five technology categories gain or lose under that reality, across a 3, 5, or 10 year horizon.

The whole app is one file: [`index.html`](index.html). No build step, no framework, no network calls.

## Running it

Open `index.html` in a browser. That's it.

To serve it locally instead:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## What's in it

- **15 levers**, each a 0–100 slider between two named poles (e.g. Cost of capital, from
  "Free money" to "High real rates"). Levers are grouped by how fast they bite: fast, medium, slow.
- **25 technology categories**, each with a weight per lever describing how that lever helps or
  hurts it.
- **3 horizons** — 3, 5, and 10 year views.
- **5 presets** — Baseline, Fragmented Rearmament, Cheap Energy Abundance, Grinding Stagnation,
  Locked-down Takeoff.
- **Chart and table views**, a light/dark toggle, and per-bar hover showing the top drivers.
- **A "How this world reads" summary** naming what the current world funds and what it starves.

## How the index is computed

For each category, every lever contributes:

```
deviation = (sliderValue - 50) / 50        // -1 .. +1
contribution = weight × deviation × speedMultiplier[leverSpeed][horizon]
index = 50 × Σ contribution / Σ |weight|   // clamped to -50 .. +50
```

The speed multiplier is what makes the horizon matter — fast levers dominate the 3 year view and
fade by year 10, slow levers barely register at 3 years and peak at 10:

| Lever speed | 3 yr | 5 yr | 10 yr |
| ----------- | ---- | ---- | ----- |
| fast        | 1.00 | 0.80 | 0.45  |
| medium      | 0.40 | 1.00 | 0.85  |
| slow        | 0.12 | 0.45 | 1.00  |

Normalizing by the sum of absolute weights keeps categories with many drivers comparable to
categories with few.

## Reading the output

The weights are editorial judgment, not measured data. The index says whether a world helps or
hurts a category — it is not a market-size estimate. Real scenarios usually land inside ±25,
because no plausible world pushes every lever the same direction.

## Editing the model

Everything lives in the `<script>` block of `index.html`:

- `LEVERS` — lever id, display name, pole labels, and speed (`fast` / `medium` / `slow`).
- `TECHS` — one entry per category: display name plus a map of lever id to weight. Positive weight
  means the category benefits as that slider moves toward its high pole; negative means it suffers.
- `MULT` — the speed-by-horizon multipliers in the table above.
- `PRESETS` — named slider configurations. `base` is empty, meaning every lever sits at 50.

Adding a category is a single row in `TECHS`; adding a lever means a row in `LEVERS` plus whatever
weights you want to give it across `TECHS`.
