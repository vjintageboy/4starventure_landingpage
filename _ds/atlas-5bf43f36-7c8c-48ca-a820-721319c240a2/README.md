# Atlas — how to build with this system

Atlas is the design system of an internal multi-store Shopify aggregator. It
serves dense operational work — an operator reconciling payouts across 50 stores
at 9am — not marketing. Read `guidelines/DESIGN.md` for the visual system and
`guidelines/PRODUCT.md` for the product strategy; this file is the short version
for composing screens.

## Personality

Approachable-professional: clear, trustworthy, unhurried. Warmth comes from
clarity and spacing, **not** decoration. Trust the numbers — a figure on screen
is something someone will act on, so it gets room, alignment, and a label.

Avoid: generic AI-template SaaS, cluttered legacy admin, consumer/playful
aesthetics, and cloning Shopify's own admin.

## Tokens, not values

Every color is a semantic token — `bg`, `surface`, `panel`, `border`,
`border-strong`, `ink`, `ink-secondary`, `ink-muted`, `accent` (+ `-hover`,
`-active`, `-weak`, `-strong`), and the status pairs `success`/`success-bg`,
`warning`/`warning-bg`, `danger`/`danger-bg`, `info`/`info-bg`,
`neutral`/`neutral-bg`. Use `bg-surface`, `text-ink-muted`, `border-border`,
never a hex or a Tailwind palette color like `bg-slate-100`.

The accent is one calm indigo and stays under ~10% of any screen: actions,
active nav, selection, focus. A screen that is mostly accent is wrong.

Light theme only. One font: Söhne, via `font-sans`.

## Layout vocabulary

- Page content sits on `bg`; raised things (tables, cards, panels) are
  `bg-surface` inside `rounded-xl border border-border`.
- Chrome (sidebar, header) is `bg-panel` — cooler and deeper than the content
  surface, so the working area reads as the foreground.
- Grids of metrics use a hairline lattice: `gap-px bg-border` on the container,
  `bg-surface` on each cell.
- Buttons are the `.btn` classes — `.btn-primary`, `.btn-secondary`, `.btn-ghost`,
  sized with `.btn-sm`. Inputs and selects use `.input`.

## Page shape

Every page opens with `PageHeader` — title, one line of orientation, optional
status badges inline after the title, and the page's primary action on the
right. Dashboards then lead with `MetricStrip` (lead metrics large, detail
metrics smaller in the band below), charts next, tables last.

## Status is never color alone

`StatusBadge` always pairs its tint with a written label. Five variants —
`success`, `warning`, `danger`, `info`, `neutral` — and the domain helpers
(`financialBadge`, `fulfillmentBadge`, `trackingBadge`, `riskBadge`,
`payoutBadge`, `roleBadge`, `syncBadge`) map a raw Shopify value to one of them.
Prefer the helper over choosing a variant by hand.

## Empty and loading states are designed, not omitted

Every list gets `EmptyState` with a real sentence and, where an action makes
sense, one button. Distinguish "nothing here yet" from "nothing matches your
filters" — they need different copy and different actions. Charts have their own
empty panels (a store with no session data is not the same as a store with no
orders).

Loading uses `Skeleton` shaped like the content it replaces — same row heights,
same column rhythm — so nothing jumps when data lands.

## Money, dates, and scale

- Amounts across stores can be in different currencies. When a total spans them,
  say so — the charts carry an "approximate total" footnote for exactly this.
- Dates in the UI are formatted in one app-wide timezone; date **ranges** are
  plain `YYYY-MM-DD` calendar strings (`DateRangePicker`), never instants.
- Design for 50+ stores and large datasets first: long store names truncate,
  tables paginate, charts stay readable when one store dwarfs the rest.

## Accessibility

WCAG 2.1 AA. The ink ramp is built to pass on white — keep body copy at `ink` or
`ink-secondary`, reserve `ink-muted` for labels and captions. Icon-only controls
need an `aria-label`. Focus rings are `accent`, and they stay visible.

# Atlas (@shopify-hub/app@0.0.0)

This design system is the published @shopify-hub/app React library, bundled as a single
browser global. All 14 components are the real upstream code.

## Where things are

- `_ds_bundle.js` — the whole-DS bundle at the project root; loads every component to `window.Atlas`. First line is a `/* @ds-bundle: … */` metadata header.
- `styles.css` — the single stylesheet entry: it `@import`s the tokens, fonts, and component styles (`_ds_bundle.css`). Link this one file.
- `components/<group>/<Name>/<Name>.prompt.md` (example JSX + variants), `<Name>.d.ts` (types), `<Name>.html` (variant grid).
- `tokens/*.css` — CSS custom properties, names verbatim from upstream.
- `fonts/` — `@font-face` files + `fonts.css` (when the package ships fonts).
- `guidelines/` — the design system's own usage guidance (2 doc(s), see `guidelines/index.md`). Read these before composing larger layouts.

For a specific component, `read_file("components/<group>/<Name>/<Name>.prompt.md")`.

## Loading

Add these two lines to your page once (React must be on the page first):

```html
<link rel="stylesheet" href="styles.css">
<script src="_ds_bundle.js"></script>
```

Components are then available at `window.Atlas.*`. Mount into a dedicated child node (e.g. `<div id="ds-root">`), not the host page's own React root, so the two trees don't collide:

```jsx
const { AovChart } = window.Atlas;
ReactDOM.createRoot(document.getElementById('ds-root')).render(<AovChart />);
```

Wrap the tree in the provider — most components read theme/i18n from context:

```jsx
<NextIntlClientProvider locale={"en"} messages={atlasMessages}>{children}</NextIntlClientProvider>
```

## Tokens

167 CSS custom properties from @shopify-hub/app. Names are
preserved verbatim from upstream. They are declared inside `_ds_bundle.css` (this DS ships one compiled stylesheet rather than separate token files).

- **color** (40): `--rdp-accent-color`, `--rdp-accent-background-color`, `--rdp-today-color`, …
- **spacing** (8): `--rdp-dropdown-gap`, `--rdp-months-gap`, `--rdp-weekday-padding`, …
- **typography** (11): `--font-sans`, `--font-weight-normal`, `--font-weight-medium`, …
- **radius** (5): `--rdp-day_button-border-radius`, `--rdp-week_number-border-radius`, `--radius-md`, …
- **shadow** (7): `--tw-shadow`, `--tw-ring-shadow`, `--tw-shadow-alpha`, …
- **other** (96): `--rdp-day-height`, `--rdp-day-width`, `--rdp-day_button-border`, …

## Components

### general
- `AovChart`
- `ConversionChart`
- `DateRangePicker`
- `EmptyState`
- `IconAddressAlert`
- `IconSpinner`
- `InlineDateRangeCalendar`
- `MetricStrip` — Two flat figure bands: the primary pulse first, then the revenue breakdown.
- `PageHeader`
- `ProductBarChart`
- `RevenueChart`
- `Skeleton`
- `StatusBadge` — Status is conveyed by label + color dot together  never color alone (DESIGN.md
- `StoreBarChart`
