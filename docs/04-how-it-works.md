<sub>**04 · HOW IT WORKS**</sub>

# Under the surface

*You don't need to understand any of this to make something like it; I didn't write a line of it. But knowing the moving parts makes your feedback sharper, so here is the whole machine, explained for designers.*

---

## The big picture

Every frame (60 or 120 times a second) runs the same loop:

```
pointer ─▶ physics (240 steps/s) ─▶ pass 1: shape ─▶ pass 2: light ─▶ pass 3: smooth ─▶ screen
```

| Stage | Plain English | Where in the code |
|:--|:--|:--|
| **Physics** | Move every tendril point a tiny step, then pull them back into shape | `step()`, `stepStrand()` |
| **Pass 1 · Shape** | For each pixel, find how tall the scene is there and which way the surface faces | `FRAG_SCENE` |
| **Pass 2 · Light** | Colour each pixel: rubber, goo, silver, hand, or floor, with reflections and shadows | `FRAG_SHADE` |
| **Pass 3 · Smooth** | Soften jagged edges (FXAA) | `FRAG_FXAA` |

---

## 1 · Shapes made of distance

Nothing in the scene is a mesh or an image. Every object is a **signed distance field (SDF)**: a function that answers "how far is this point from the surface?" Negative means inside.

- **The slab** is a 2D pill shape extruded upward with a 3 px rounded bevel.
- **Each tendril** is a chain of 13 tapered capsules (tubes that change radius along their length).
- **The cursor** is the Figma SVG rasterised once, converted to a distance field, then extruded into a thin slab. Blending the arrow's and the hand's distance fields is what makes the shape **morph**.

The magic ingredient is **smooth minimum**. A normal union of two shapes leaves a hard crease; a smooth minimum melts them together with a fillet. That fillet is why tendrils look like they grow *out of* the surface rather than being stuck onto it, and why neighbouring strands web together.

```glsl
float smin(float a, float b, float k) {
  float h = max(k - abs(a - b), 0.0) / k;
  return min(a, b) - h * h * k * 0.25;
}
```

The bigger `k`, the gooier the join. Early versions used large values and looked like water; the final uses small ones (2.5 px between strands, 6 px into the body) so it reads as stringy rubber.

## 2 · Looking straight down

The camera looks straight down. For each pixel, pass 1 drops a ray from above and **marches** it down, stepping by the distance the SDF reports, until it touches a surface. It records the height and the surface direction (normal). Because rays are vertical, a lot can be skipped: each pixel first works out which tendril segments could possibly reach it, and only tests those. Where nothing but the slab is underneath, its height is solved directly instead of marched, and only the screen region around the button, tendrils and cursor (plus their shadows) is traced at all; the rest of the floor is kept from earlier frames.

## 3 · Light like a photo studio

Pass 2 shades each pixel based on what it hit.

- **Reflections** come from a procedural environment: a black studio with a few shaped lights, plus a hand-built living room (window, sky, tree line, wall, lamp) up and to the left.
- **Fresnel:** surfaces reflect more as they turn away from you, which is why edges and rims glow.
- **Matte vs glossy:** the face uses a broad, soft highlight (rubber); tendrils use sharp reflections (wet goo).
- **Shadows** are traced across the height map toward the light. The button also gets an extra, hand-tuned contact shadow so it sits on the floor.
- **Tone mapping** (ACES) compresses the bright lights so the white floor stays white and the black stays black.

## 4 · Strands that behave like rubber

Each tendril is **14 points joined by sticks**, simulated 240 times a second (Verlet integration):

1. **Move** each point by its velocity, plus gravity and a gentle sideways "slither" wave.
2. **Correct** three times: each pair of neighbours is pushed back to its rest length, and points two apart keep a minimum distance so the strand curves instead of kinking.
3. **Pin** the root to the button and the tip to its wrap site on the cursor.

On top of that:

| Behaviour | How |
|:--|:--|
| **Thinning** | Volume is conserved: radius = √(volume ÷ π·length) |
| **Viscous flow** | Rest length creeps toward the actual length slowly (0.25/s), so slow pulls stretch instead of snapping |
| **Snapping** | Past 1.6× stretch or 160 px, the neck thins to nothing over 100 ms, then the strand splits in two |
| **Recoil** | Each half retracts along its own path (shortened from the free end), so it never crumples |
| **Beads** | Past 1.5× stretch, the radius ripples every ~9 radii, like real slime threads breaking up |
| **No jiggle** | Heavy damping and critically damped springs everywhere: melted rubber, not water |

## 5 · The lunge

New tendrils don't just appear; they **wind up and strike**. A second-order spring drives each tip from the surface to the cursor with a slight backward dip first, a fast rise, and a small overshoot that settles as it latches. The body follows a sideways arc so it reads as a whip, not a straight line.

## 6 · Wrapping the hand

Each finger (and the wrist) has an axis in icon space. A wrap is an **elliptical helix** around that axis: where the sine of the angle is positive the coil passes over the icon, otherwise it tucks underneath, hidden by the slab. Winding is animated from 0 to about 2 turns in a fraction of a second; snapping unwinds it.

## 7 · Feeling caught

The tension of every wrapped strand is summed into one vector. The **drawn** hand is offset along it (up to 10 px) and rotated so its wrist swings toward the pull (up to ~20°). Your real pointer is never moved, so clicks stay exact.

---

## Glossary

| Term | Meaning |
|:--|:--|
| **SDF** | Signed distance field: a shape described by a distance function |
| **Smooth min** | A blend of two shapes with a soft fillet instead of a crease |
| **Ray marching** | Stepping a ray forward by the safe distance until it hits a surface |
| **Normal** | The direction a surface faces; drives all lighting |
| **Fresnel** | Surfaces reflect more at grazing angles |
| **Verlet** | A simple, stable way to simulate points connected by sticks |
| **Damping ζ** | 1.0 means it settles without bouncing (critically damped) |
| **FXAA** | A fast filter that smooths jagged edges |

---

**Next:** [05 · The prompting playbook →](05-prompting-playbook.md)
