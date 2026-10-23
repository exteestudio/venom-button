<sub>**01 · THE BRIEF**</sub>

# Start with the feeling, not the feature list

*Before touching Claude, two questions: do I see the end result in my mind, and do I know what Claude is capable of? The first message answered the first question in as much sensory detail as I could, and asked Claude to answer the second by doing its own research.*

---

## The first message

This is the opening prompt, lightly trimmed. Notice that it describes **behaviour and material**, not code.

> I want to create a button that is a venom button that acts in a certain way. The cursor will slightly hover around the main button, which would be **liquidy black but stretchy, like a spider web moving towards it.** The movement and the physics of the entire sticky ink or gooey substance should be the same as how Venom acts. Venom is a very sticky character made up of magnetic or alien-type slithering substances, kind of like **a mix of a lizard and a spider.**
>
> I want this environment to be **white, 3D-ish if possible**, because I like when things look super realistic. The button is the natural Venom color.
>
> Let's create something super sexy as a first attempt, and then we can refine the movement together. **There should be strands.** Following the cursor, there should be a couple of strands. **There should be distortion happening on the top part** because strands are not going from a static button. The surface they lie on also gets affected, stretching and strandy.
>
> Use design skills to create realistic physics UI/UX and do a deep dive on Twitter, GitHub or Reddit. Find skills that will help create this very accurately.

## Why it worked

| What the brief did | Why it mattered |
|:--|:--|
| **Named a reference everyone knows** (Venom) | Gave Claude a shared mental model for the motion: sticky, magnetic, alive |
| **Used analogies from nature** (lizard, spider, web) | Translated directly into physics: whip-like lunges, web-like strands |
| **Described the environment** (white, realistic, 3D-ish) | Set the lighting and material direction before any pixels existed |
| **Called out a non-obvious detail** (the surface distorts where strands leave) | This one line became the "tent" that makes strands look rooted, not glued on |
| **Set expectations** ("first attempt, then we refine together") | Gave permission to be ambitious and iterate, instead of over-planning |
| **Asked for research** | Claude searched GitHub and the web and came back with real techniques: ferrofluid shaders, rope physics, the physics of how slime threads thin and snap |

## What Claude did with it

1. **Looked for skills.** It searched for design and physics skills, loaded a design skill for ambitious visual effects, and sent two research agents out: one for goo and strand techniques, one for community skills about shaders and physics.
2. **Chose an approach.** A single HTML file in raw WebGL2. The button is drawn as real 3D geometry (a signed distance field) lit like a product photo, and each strand is a small physics simulation.
3. **Built a first version** with tendrils that lunge, latch, stretch, thin out, snap and recoil, plus surface ripples and a droplet that crawled home.
4. **Tested it in a browser,** frame by frame, and fixed what looked wrong before showing it.

![Idle state from the final version](images/stage-1.jpg)

> [!NOTE]
> The first version looked nothing like the final one. That's the point. The brief sets a direction; the next 24 rounds of plain-English feedback shape it.

---

**Next:** [02 · The process →](02-process.md)
