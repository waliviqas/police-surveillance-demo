# Clarity — every camera, one search

A pinned-scroll deck for Clarity, a camera network that coordinates the search
for a missing person. Modelled on Apple product pages: deep-space palette,
luminous liquid glass, and a pinned centrepiece that scroll drives.

Single self-contained `index.html`. No build step, no dependencies beyond
Outfit and Inter from Google Fonts.

## Viewing

```bash
python3 -m http.server 4174
```

http://localhost:4174 — scroll, or arrow keys / space to jump act to act.
Best at 1280px or wider, and it is built to be shown in a dark room.

## What was taken from Apple

Inspected live rather than guessed at. From apple.com/apple-vision-pro:

- **28.5 viewports** of scroll for a single product page, so length is not the
  enemy — pacing is.
- **`position: sticky` with a `top` offset** clearing the nav is the entire pin
  mechanism. Not scroll-jacking, not a library.
- **Native CSS scroll-driven animation** (`animation-timeline`) is in use, which
  is why their pages feel weightless. We stay on a rAF handler because the graph
  simulation needs real logic per frame, not just interpolation.
- **Scroll-scrubbed video**, not canvas image sequences, drives their hero.

The important structural lesson: Apple pins *the product* and lets scroll drive
it. Clarity's product is not an object, it is the search propagating — so that
is what gets pinned.

## The centrepiece

`#act-search` is 900vh of pinned camera graph. The graph is generated once,
deterministically, then a Dijkstra over travel-time edges gives every node an
arrival time. Scroll position is simply *elapsed time*, so the frame you see is
the honest answer to "what does the network look like at t?" — the same question
the real walker answers.

Four beats: the search opens, the cone expands, a camera hits nine minutes out,
and the walker re-blooms from the sighting while the confirmed trail draws
itself back through the graph. Live readouts for cameras reached, frontier size
and confidence.

The bloom is deliberately **linear, not eased** — a cone expands at a constant
rate, and easing made the whole network light up in the first third.

## The acts

| Act | Height | What happens |
|---|---|---|
| 0 · Title | 220vh | CLARITY resolves out of blur |
| 1 · Hero | 300vh | "The search moves the way people do" |
| 2 · Problem | 460vh | Alerts go out not outward · cameras never talk · area grows as t² |
| 3 · **The search** | 900vh | The pinned graph, scrubbed by scroll |
| 4 · How | 600vh | Four cards that scale toward you, including the walker source |
| 5 · Privacy | 420vh | No faces · searches expire · every search leaves a trail |
| 6 · Closing | 300vh | Wordmark, tagline, team |

Everything is scroll-driven. Nothing is on a timer, so it plays forward and
backward exactly as you scroll — which also means every frame is reachable for
a screenshot.

## Editing

- `:root` — the cyan/violet spectrum and glass tokens
- `renderSearch(p)` — the whole centrepiece, driven by act progress 0→1
- `BEATS` — the four copy beats and where they fire
- `ORIGIN` / `HIT` — fixed node indices so the story is repeatable every run
- Act length is `style="height:Nvh"` on each `<section class="act">`; the maths
  is normalised to the act, so lengthening one just slows it down

### Performance

Offscreen gallery cards are `visibility:hidden` rather than `opacity:0`, and
`will-change` is applied only to cards on screen — an offscreen card at opacity
0 still costs a composited layer and gets re-rastered. Blur radii step down
above 1600px, since blur cost scales with pixel count.

### Verifying changes

Scroll-driven state screenshots headlessly by faking `pageYOffset` and pinning
one act with `position: fixed`. Time-based animation does not — headless Chrome
with `--virtual-time-budget` never advances `requestAnimationFrame`. One more
reason everything here is scroll-driven.
