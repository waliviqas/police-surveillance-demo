# Clarity — every camera, one search

A pinned-scroll deck for Clarity: a camera network that coordinates the search
for a missing person. Eight acts across ~35 viewports, in a cool cyan and
deep-water liquid-glass treatment.

Single self-contained `index.html`. No build step and no dependencies — Outfit
and Inter load from Google Fonts.

## Viewing

Open `index.html` directly, or serve it:

```bash
python3 -m http.server 4174
```

Then visit http://localhost:4174. Scroll, or use arrow keys / space to jump act
to act. Best at 1280px wide or more.

## The argument the deck makes

Each camera is a node. Each edge is **reachability, not proximity** — it carries
how long it actually takes to travel between two cameras on foot, by car, by
transit. The alert is a **walker** that spawns at the point last seen and expands
only along edges it could have covered in the elapsed time, handing each camera
context rather than a query, and re-blooming from any confirmed sighting.

The deck's centrepiece is the act that shows why that beats a radius: a circle
around the point last seen grows as the square of elapsed time (roughly 28 mi²
after an hour at walking pace), and most of it is water, freeway or the inside
of a block. The reachable frontier covers a fraction of the same area.

Those figures are computed, not quoted: 3 mph on foot, area = πr². They hold up
if a judge checks them.

## The acts

| Act | Height | What happens |
|---|---|---|
| 0 · Title | 250vh | CLARITY resolves out of blur, a scan crosses it, rings expand |
| 1 · Hero | 300vh | "The search moves the way people do" — the camera-trail tile opens from zero width |
| 2 · Problem | 500vh | Alerts go out not outward · cameras never talk · area grows as t² |
| 3 · How it works | 750vh | Five cards that scale toward you: the graph, the cone, the handoff (with the walker source), re-bloom |
| 4 · The cone | 500vh | A radius and the reachable frontier growing together, with live area readouts |
| 5 · Privacy | 400vh | No faces, searches expire, every search leaves a trail |
| 6 · Mission | 500vh | The mission line lights word by word |
| 7 · Closing | 300vh | Wordmark, tagline, team |

Everything is scroll-driven. Nothing is on a timer, so the deck plays forward and
backward exactly as you scroll.

## The light

Two **curtains** of vertical striations sit against the left and right edges,
centre kept clear so type stays readable. Each is three
`repeating-linear-gradient` layers scrolling sideways at different speeds — the
interference between layers is what makes it read as liquid rather than as
moving stripes. A **glint** sweeps each curtain every 6.5s, offset so the two
sides never fire together.

Behind that, four blurred blobs drift on a fixed layer. That layer is what the
glass panels refract; without it `backdrop-filter` has nothing to show and every
panel renders white-on-white.

## Editing

Everything is in `index.html`:

- `:root` — the cyan spectrum, ink colours, and the glass tokens
- `.curtain` / `.c1`–`.c3` — curtain speed (`--sp`) and density (`--tile`)
- `.glass` — the shared panel treatment
- One `render()` function drives every act from its scroll progress `p` (0→1)

Act length is set inline as `style="height:Nvh"` on each `<section class="act">`.
Making an act longer slows everything inside it down; the animation math is
normalised to the act, so nothing else needs changing.

### Performance notes

The gallery is the expensive act. Offscreen cards are `visibility:hidden` rather
than `opacity:0`, and `will-change` is applied only to the one or two cards on
screen — an offscreen card at opacity 0 still costs a composited layer and gets
re-rastered. Blur radii step down above 1600px, since blur cost scales with
pixel count and a fullscreen 4K window pays several times what a laptop does.

### Verifying changes

Scroll-driven state can be screenshotted headlessly by faking `pageYOffset` and
pinning one act with `position: fixed`. Anything time-based cannot — headless
Chrome with `--virtual-time-budget` does not advance `requestAnimationFrame`,
so timed animation has to be checked in a real browser. That is one reason
everything here is scroll-driven.
