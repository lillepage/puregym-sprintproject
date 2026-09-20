# PureGym — demo build

A click-through demo of the PureGym concept, built to match the Figma prototype
[FinalPagesProto](https://www.figma.com/design/XmRz60JkwUVptAMBmSKonT/FinalPagesProto).

## Running it

**Double-click `index.html`.** That's it — no server, no build step, no internet
connection needed. Everything (fonts included) loads from this folder.

If you'd rather serve it over HTTP:

```bash
powershell -ExecutionPolicy Bypass -File .claude/serve.ps1
```

then open <http://localhost:8080>.

## What to know before presenting

- **There is no JavaScript.** Not a single `<script>` tag, no inline handlers.
  Every screen is plain HTML and CSS, and all navigation is ordinary `<a href>`
  links. The bar chart, the calendar and the week strip are static markup.
- **It is fixed to one screen size: 402 x 874** (iPhone 16 Pro), which is the exact
  frame size used in Figma. It is deliberately **not** responsive — there are no
  media queries. Resizing the browser window will not reflow it; the phone just
  stays centred. Present it on a laptop browser at any window size.
- The phone bezel is drawn in CSS. To remove it, delete the `border-radius` and
  `box-shadow` from `.device` in `app.css` (and the `.device::after` rule, which
  draws the dynamic island).

## The flow

```
index.html          Home
 |- streak tile        -> streak-0.html   0 weeks, teal
 |                          `- tap circle -> streak-1.html   0 weeks, gradient
 |                                             `- tap circle -> streak-2.html   1 week, pink
 |- "23 people in gym" -> traffic-23.html  not very busy
 |                          `- tap circle -> traffic-54.html   a little busy
 |                                             `- tap circle -> traffic-125.html  as busy as it gets
 `- booking tile       -> booking.html     Class booking / Personal trainer

streak-0/1/2.html   the small gear beside "2/week" -> goal.html
```

Every sub-screen's back arrow returns to Home.

**The goal screen is opened by the little gear next to "2/week"** inside the streak
circle (Figma node `14:1575`), not by the settings cog in the header.

**Intentionally not clickable**, because they aren't hotspots in the Figma
prototype either: the header settings cog, *find center*, *help*, *check-in*, the
header logo, the "HORSENS CENTER" dropdown, the calendar dates, and both halves of
the booking screen. They're styled but inert — that's deliberate, not an oversight.

## Files

| File | What it is |
| --- | --- |
| `app.css` | Every style for every screen. Design tokens are at the top. |
| `assets/` | Icons (used as CSS masks), the two booking photos, self-hosted fonts. |
| `_old/` | The previous version, kept for reference. Nothing links to it. |
| `.claude/serve.ps1` | Optional local web server. |

## Making changes

Colours, the frame size and the corner radius are all CSS variables at the top of
`app.css` — change them in one place and every screen follows.

Because there is no script, the per-screen variations are just classes in the HTML:

```html
<!-- filled-in day pills and dates -->
<div class="week-day is-teal">th</div>
<div class="cal-day is-pink">3</div>

<!-- the highlighted "right now" bar in the traffic chart -->
<div class="bar is-now"></div>
```

The counter circles take a modifier class too: `.is-gradient` / `.is-pink` on the
streak page, `.is-mid` / `.is-busy` on the traffic page.

The 25 bar heights live in `app.css` as `.bar:nth-child(n)` rules, so all three
traffic screens share one copy of the chart shape — only the `is-now` bar differs.

### Two hotspots on the streak circle

The circle advances the streak state, and the gear inside it opens the goal screen.
An `<a>` can't be nested inside another `<a>`, so the circle's hotspot is a
transparent overlay (`.counter-hit`) and the gear sits above it. `.counter-goal` is
raised above the overlay but set to `pointer-events: none`, with `pointer-events:
auto` back on the gear itself — so only the gear takes that click.
