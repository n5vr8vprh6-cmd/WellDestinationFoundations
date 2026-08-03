# Well Destination Foundations — Website

The digital home of the flagship education program inside the **Saint Lucia Well
Destination** initiative. Built as a production-ready static site: semantic HTML
(best possible SEO), a tokenized CSS design system, and light
progressive-enhancement JavaScript. No frameworks, no build step required.

## Open it

Serve the parent folder and open `/well-destination-foundations/`:

```
py -m http.server 4300 --directory ".."      # or use the wellness-night launch config
http://localhost:4300/well-destination-foundations/
```

| File | What it holds |
|------|----------------|
| `index.html` | Full page: copy, structure, SEO metadata, JSON-LD schema |
| `css/site.css` | Design system: tokens, components, responsive rules, motion CSS |
| `js/site.js` | Enhancement only: nav toggle, scroll reveal, email capture stub |
| `js/atmosphere.js` | WebGL hero: procedural Saint Lucia dawn (Pitons, mist, sea, stars) |
| `js/motion.js` | Choreography: split-text, staggers, 3D tilt, journey line, counters |
| `js/hero-video.js` | Loads/plays the hero cinemagraph; skips it when conditions call for stills |
| `js/experience.js` | GSAP + ScrollTrigger + Lenis: the immersive scroll journey |

### Hero cinemagraph (Kling-generated)

`assets/hero-loop-web.{webm,mp4}` is a 10s looping video layered over the
static `hero-pitons.jpg`, generated from that same photo via Kling
(`image_to_video`, model `kling-video-v2_6`, locked-off camera, ambient
motion only — no pan/zoom/dolly, since the page already drives its own
scroll zoom and WebGL mist overlay on top of this footage). Built as a
**forward+reverse boomerang** (`reverse` + `concat` in one ffmpeg pass) so
the loop point is mathematically exact — no jump-cut. Encoded to
`libvpx-vp9` (WebM, ~4.3MB, served first) and `libx264` (MP4 fallback,
~5.6MB) at 1280px wide, no audio track.

`js/hero-video.js` is pure enhancement over the always-present `<img>`:
- Skips entirely for `prefers-reduced-motion`, `navigator.webdriver`
  (automated agents), and Save-Data/2G connections — the poster image is
  the whole experience for those visitors.
- `preload="none"` until conditions are confirmed, then loads and fades in
  on `canplay` (never blocks the image, which is the real LCP element).
- Pauses via `IntersectionObserver` when scrolled off-screen and on
  `visibilitychange` (hidden tab) — resumes automatically when back in view.
- If autoplay is blocked by the browser, the promise rejection is caught
  silently and the static poster simply remains — no broken state, no error.

GSAP's hero-open tween (`js/experience.js`) scales both the `<img>` and the
`<video>` together so the Ken-Burns zoom-out stays in sync regardless of
which layer is currently visible.

**The full cinemagraph set** (all Kling `kling-video-v2_6`, locked-off
ambient loops): the hero (boomerang loop), plus `sulphur-loop` (rising
steam, in the photo strip), `seacliff-loop` (rolling swell, the full-bleed
breaker), and `dawn-loop` (glassy shimmer behind the final CTA). The three
newer ones carry *directional* motion, which reads backwards in a boomerang,
so they use **crossfade loops** instead (ffmpeg `xfade`: body from 1s +
first second dissolved over the tail → a 4s mathematically seamless loop;
note `xfade` requires constant frame rate — re-stamp `fps=24` after each
`trim`). All four load through the same `js/hero-video.js` ladder:
lazy-armed near the viewport, faded in on `canplay`, paused off-screen,
skipped for reduced-motion/agents/Save-Data. Combined weight of the three
new loops: ~2.5MB. Cocoa-market and the golden-wave Pitons were deliberately
excluded (crowd/hand motion and crashing surf are where generative artifacts
and loop seams show first).

Everything works with JavaScript disabled (content, navigation, FAQ via native
`<details>`); JS adds motion and form handling.

### The immersive journey (GSAP mode)

When GSAP + ScrollTrigger + Lenis load from CDN (and motion is allowed),
`site.js` switches the page into **gsap mode** — a three-act scroll film:

- **Act I · Arrival.** The page opens pre-dawn: stars, dark sea, the Pitons
  barely lit. The hero pins for 130% of a viewport and **scrolling raises the
  sun** — a `uSun` uniform scrubs the shader from night to full golden dawn
  (sun climbs, stars dissolve, the gold path ignites on the sea). A scroll cue
  reads "Scroll · the sun rises with you."
- **Act II · The three days.** On desktop the curriculum pins and the three
  day-cards travel horizontally — the learning journey literally moves you
  forward.
- **Act III · The pathway.** The journey line draws itself under scrub
  control; nodes light as you pass. Ghost place-names ("Saint Lucia,"
  "Foundations") drift behind the editorial in outlined display type; the
  photography panels parallax against their sections.
- Lenis provides inertia scrolling, drives ScrollTrigger, and powers smooth
  anchor navigation; deep links (`/#curriculum`) re-anchor after pin spacers
  grow the page and land with everything above them already revealed.

**Mode ladder:** `gsap` (full experience) → `io` (IntersectionObserver
reveals; automatic if any CDN script fails, or forced via `?flat=1`) →
`instant` (reduced-motion users and automated agents; everything visible,
static). Content never depends on JavaScript at all.

### The motion system

- **Hero atmosphere** — a hand-written GLSL fragment shader renders Saint
  Lucia at dawn in real time: conical Piton silhouettes with sun-rim light,
  drifting FBM mist, a gold sun path and glitter on the sea, broken water
  reflections, stars dissolving into first light, filmic grain. Fully
  procedural (zero image assets, ~6 KB of code), brand-palette exact, with
  pointer parallax on three depth layers. The loop pauses off-screen and on
  hidden tabs; reduced-motion users get a single still frame; no WebGL means
  the CSS gradient fallback simply shows.
- **Split-text headlines** — h1/h2 words rise from masked slots with a
  cascade, timed to font loading.
- **Staggered grids** — pillars, stats, days, bonuses, people, outcomes
  cascade in per-child.
- **3D tilt** — day cards, bonus cards, portraits and the price card tilt in
  perspective with a gold cursor-following glare (fine pointers only).
- **Journey line** — the pathway's gold thread draws itself with scroll;
  nodes light as you pass them.
- **Counters** — the stats count up on first view.
- **Chrome** — reading-progress thread (teal→gold), header hides on
  scroll-down / returns on scroll-up, magnetic primary CTAs with a sheen
  sweep, orbiting dashed ring on the hero mark.
- **Guarantees** — `prefers-reduced-motion` and automated agents
  (`navigator.webdriver`) get everything instantly with no animation, and a
  `settled` failsafe snaps all animated text visible 3.6 s after reveal, so
  content can never be lost to an interrupted transition.

---

## 1 · Website architecture

**Phase 1 (this build): a single, long-form editorial page** at `/foundations`,
with anchor-linked sections. One page keeps the narrative arc intact (the page
*is* the sales conversation), maximizes scroll-based persuasion, and needs no
CMS to launch.

**Phase 2 target information architecture** (see §12 Scalability):

```
welldestination.com
├── /                     Movement home (the Well Destination thesis)
├── /foundations          ← this page (flagship program)
├── /webinar              Free webinar registration (dedicated capture page)
├── /immersion            Application-gated, alumni-facing
├── /collective           Membership
├── /destinations/
│   └── /saint-lucia      Flagship destination hub (guide, collection, updates)
├── /journal              Editorial/SEO engine (category authority content)
└── /apply                Application flow
```

## 2 · User journey

Primary persona: an experienced luxury/host-agency advisor, time-poor,
skeptical of hype, motivated by positioning and peer status.

```
Discover (LinkedIn / consortium newsletter / webinar invite)
   → Land on hero: category reframe ("booked → designed")
   → Credibility strip (SLTA, WTA) answers "is this real?"
   → Opportunity data answers "why now?"
   → Category shift + pillars answer "why Well Destinations?"
   → Saint Lucia section answers "why here?"
   → Transformation + curriculum answer "why this program?"
   → Who-this-is-for + outcomes answer "why me?"
   → Investment (value stack before price) answers "is it worth it?"
   → Pathway shows this is step 2 of 6 — belonging, not a transaction
   → FAQ clears residual objections
   → Two exits, always: Enroll ($697) or free webinar (email capture)
```

Every section ends nearer to one of the two conversion paths; the undecided
reader is never more than one screen from the free, low-commitment option.

## 3 · Wireframe hierarchy

```
┌ sticky nav — brand · anchors · [Enroll]
├ HERO (dark) — eyebrow / H1 thesis / lead / CTA pair / fact line
│   └ signature: ring mark → gold journey line → coordinates
├ credibility strip — institutional partners
├ OPPORTUNITY (paper) — lead + 3-stat grid + source note
├ CATEGORY SHIFT (ink) — split: argument | 3-era evolution list
│   └ 8-pillar grid (4×2 → 2×4 → 1-col)
├ WHY SAINT LUCIA (paper) — split: art-directed photo | argument
│   └ 4 reasons (2×2 → 1-col)
├ PROGRAM (sand) — before/after transformation cards + format facts
├ CURRICULUM (charcoal) — 3 day cards: label | summary + topic pills
├ BONUSES (sand) — 6 cards, 3×2 → 2×3 → 1-col
├ FACILITATORS (ink) — 3 portrait cards (placeholders)
├ WHO THIS IS FOR (paper) — role list | "a note on fit" aside
├ OUTCOMES (ink) — 3 columns: professional / personal / business
├ INVESTMENT (paper) — value stack | sticky price card ($697)
├ PATHWAY (charcoal) — journey line as spine, 6 nodes, "you are here"
├ FAQ (paper) — 8 native-details accordions
├ FINAL CTA (ink) — enroll + webinar email capture
└ footer — brand, nav, coordinates
```

## 4 · Page copy

Complete copy is in `index.html` (single source of truth). Voice: editorial,
declarative, zero hype. Placeholders are marked `[…]` — partner names,
facilitator bios, refund policy — so nothing unconfirmed reads as fact.
Statistics carry a visible "verify current editions" source note until the
team locks final Global Wellness Institute figures.

## 5 · UX rationale (conversion psychology per section)

- **Hero** — sells the *category shift*, not the course. "Booked → designed"
  is the entire transformation in six words (dream outcome ↑).
- **Credibility strip** — institutional proof directly under the claim
  (perceived likelihood ↑) without testimonials we don't have.
- **Opportunity** — three numbers, one source, no charts: data as
  self-qualification ("this is a market, not a fad") (why now).
- **Evolution list** — positions the buyer *in history*; the highlighted
  "era three — now" row is honest urgency (no timers).
- **Before/after cards** — the muted quiet card vs. the elevated ink card is
  the value equation drawn as layout: identity upgrade, visible.
- **Curriculum pills** — 19 topics compressed into scannable chips: high
  density of value, low reading effort (effort ↓).
- **Bonuses** — framed "Included with enrollment," not "worth $X,000":
  perceived value without infomercial math.
- **"A note on fit"** — mild disqualification builds trust and raises
  in-group status; it also routes beginners to the webinar (both CTAs win).
- **Investment** — value stack *above* price; price card is sticky so the
  offer follows the reader through the justification.
- **Pathway** — reframes $697 as admission to a 6-step trajectory
  (founding member status = scarcity that's true).
- **FAQ** — answers the real objections (guarantees, live/recorded, fit)
  plainly; "what if it isn't for me" routes to the free option, not a
  pressure close.
- **Two-CTA discipline** — every decision point offers Enroll *or* the free
  webinar. No one leaves the page without a next step matched to their
  readiness (time delay ↓).

## 6 · Visual direction

- **Palette (daylight editorial, matched to the printed edition)** — ivory
  paper `#FBF8F1` and sand `#F3EDE0` dominate; deep-teal ink `#133239` for
  text; warm gold `#D9A03C` (deep `#B07E24` on light) as the accent. Dark
  surfaces are reserved for accents: the price card, the after-card, the
  footer, and photographic scrims. Full-saturation deck teal/gold/coral
  appear **only in the concentric-ring mark**, matching the brochure rule.
- **Photography** — real imagery extracted from *Discover Saint Lucia WELL —
  Editorial Founding Edition* (`assets/`): the foliage-framed Pitons cover
  as the hero, golden-wave Pitons, sulphur springs, cocoa market, sea
  shallows (the Why Saint Lucia strip), a wide sea-cliff breaker, and the
  dawn-horizon sea behind the final CTA.
- **Hero** — the brochure cover, full bleed, with a WebGL mist-and-light
  pass screened over it. Scroll clears the morning: mist thins, the cool
  veil lifts, gold shimmer arrives on the water ("Scroll · the morning
  clears"). Headline sits low, over the dark water, as on the printed cover.
- **Type** — Libre Caslon Display (display, used with restraint) · Libre
  Caslon Text (editorial body) · Hanken Grotesk (labels, data, UI). Same
  3-role system as the Discover Saint Lucia WELL brochure.
- **Signature** — the **journey line**: a gold hairline that descends from
  the ring mark in the hero and returns as the literal spine of the pathway
  section. The page's argument (this is a pathway) made visible.
- **Rhythm** — dark cinematic sections alternate with ivory editorial
  sections; magazine pacing, not landing-page pacing.
- **Photography** — three art-directed placeholder panels with full shot
  directions (Pitons dawn, sulphur springs, sea). No stock clichés: no yoga
  silhouettes, no laptop-by-pool. Replace with commissioned photography.
- **Motion** — one idea: quiet upward reveals on scroll. Reduced-motion users
  and automated agents get instant content.
- **Coordinates** `13°54′N 60°58′W` — the brand's "real place" signature, in
  the hero and footer.

## 7 · Component library

`.btn` (gold / ghost / ghost-dark) · `.eyebrow` · `.section` (+ dark / ink /
paper / sand skins) · `.section-head` · `.stat-grid` · `.split` · `.evolution`
· `.pillars` · `.photo` (+ art-direction caption) · `.transform` cards ·
`.format-row` · `.day` cards · `.bonus-grid` · `.people` portraits ·
`.audience` + `.not-for` · `.outcomes` · `.value-stack` + `.invest-card` ·
`.journey` (nodes + spine) · `.faq` (native details) · `.capture` form ·
header/footer. All tokenized; each maps 1:1 to a future CMS block.

## 8 · Design system

Tokens live in `:root` of `css/site.css`: 16 color tokens, 3 font stacks, a
clamp()-based 8-step type scale, spacing rhythm (`--section-y`, `--gutter`),
and a reading measure (`--measure`). Quality floor built in: visible
`:focus-visible` rings, skip link, `prefers-reduced-motion` respected,
WCAG-checked text/background pairs (gold is darkened to `#B07E24` on light
backgrounds for contrast).

## 9 · Mobile layouts

Breakpoints at 960 / 860 / 780 / 700 / 640 / 560 / 520px collapse each grid at
the width where *it* breaks, not at arbitrary device sizes. Nav becomes a
toggle menu at ≤860px. Verified at 375px: no horizontal overflow; all grids
stack to single column; CTA pair and capture form go full-width.

## 10 · Conversion optimization recommendations

1. Put real cohort dates on the page as soon as they exist — "Next cohort:
   [date]" is honest urgency and the single highest-leverage missing element.
2. Add 2–3 named advisor testimonials after cohort one (role + agency +
   specific result); replace the credibility strip placeholders with logos.
3. Wire the capture form to the ESP and send a 3-email webinar sequence
   (thesis → case study → cohort dates).
4. A/B test hero CTA order (Enroll-first vs. Webinar-first) once traffic
   exceeds ~500 visits/cohort; the right default depends on traffic warmth.
5. Add an application-style enrollment step (2 short questions) — for this
   audience, light friction *raises* perceived selectivity and completion.
6. Instrument scroll-depth and CTA-click events (dataLayer hooks are already
   in `site.js`); watch Investment-section arrival rate as the key metric.
7. After cohort one, add a short (60–90s) facilitator welcome film to the
   hero — cinematic, spoken thesis, no slides.

## 11 · SEO recommendations

- Already in place: semantic single-`h1` outline, meta description, canonical,
  Open Graph/Twitter cards, JSON-LD (`Course` + `Offer`), descriptive anchors,
  font `display=swap`, zero render-blocking JS.
- Target queries: "wellness travel advisor training", "wellness tourism
  certification for travel agents", "sell wellness travel", "Saint Lucia
  wellness travel specialist".
- Build `/journal` as the authority engine: pillar pages ("What is a Well
  Destination?") + advisor-intent posts; internal-link them here.
- Add `FAQPage` JSON-LD once final FAQ copy is confirmed (schema should match
  visible text exactly).
- Produce a real `og-foundations.jpg` (1200×630) before launch; the URL is
  already referenced.
- When the domain is final, add `sitemap.xml` + `robots.txt`, and serve over
  a CDN with far-future cache headers on css/js.

## 12 · Future scalability

- **The framework is destination-agnostic by design.** "Well Destination" is
  the platform brand; Saint Lucia is the *founding flagship*, stated in the
  footer. New partners slot into `/destinations/<name>` without touching the
  Foundations program page.
- Copy discipline: the eight pillars and the pathway never mention Saint
  Lucia structurally; only Day 2 and the destination sections do. Adding a
  second destination means a new destination hub + one new Day-2 module, not
  a rewrite.
- The component library maps 1:1 to CMS blocks (Sanity/Contentful): every
  section is a typed object (`stat`, `day`, `bonus`, `person`, `journeyStep`,
  `faqItem`). Migrate content, keep the design system.
- The Collective becomes the retention layer: gated content behind membership
  reuses the same design system in an authenticated app.
- Naming holds at global scale: *Well Destination Foundations* →
  *[Destination] Immersion* → *Well Destination Collective* — program names
  never need to change as destinations join.

---

### Placeholders to resolve before launch

institutional partner names & logos · facilitator names/bios/portraits ·
cohort dates · refund policy · final GWI statistics · commissioned
photography · ESP endpoint for the capture form · analytics container ·
final domain (update `canonical` + JSON-LD URLs).
