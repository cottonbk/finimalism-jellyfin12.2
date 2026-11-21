# Finimalism — rewritten for Jellyfin 12.2

Faithful port of [tedhinklater/finimalism](https://github.com/tedhinklater/finimalism) to
**Jellyfin Server 12.2.x** with the bundled **jellyfin-web 12.2** client.

Every original rule is preserved unchanged, so the classic look is identical.
On top of that, each main theme file now also styles the **new modern UI**
(the React/MUI shell that Jellyfin 12.2 boots by default) using the theme's own
variables, and bridges the finimalism colours into the official
`--jf-palette-*` custom-property hooks that the 12.2 web client exposes.

## What changed for 12.2

Jellyfin 12.2 ships two UIs:

| Layout | How to get it | DOM | Theme coverage |
|---|---|---|---|
| **Modern** (default) | just log in | React/MUI shell + classic content DOM | styled by the new *modern UI adapter* section appended to each main file |
| **Legacy / TV** | User Settings → Display → Layout → `desktop-legacy` / `mobile-legacy` / `tv` | classic DOM everywhere | styled by the original rules (kept verbatim) |

Content pages (home sections, library grids, item details, playback OSD)
render with the classic DOM in **both** layouts, so the bulk of finimalism
works as before. The 12.2 port adds:

1. **`--jf-palette-*` palette bridge** — maps `--accent` / `--tint` / `--tint2`
   onto the MUI palette (primary/secondary/buttons/backgrounds/app bar/text),
   so the modern shell recolours itself from the theme's variables.
2. **Modern shell styling** — transparent MUI app bar with drop-shadow text,
   tinted glass on scroll, tinted + blurred MUI navigation drawer with the
   finimalism hover (white bg / black text) and accent selected states,
   tinted menus, dialogs, tooltips, snackbars, skeleton loaders, and accent
   on switches, checkboxes, chips, pagination, tabs and progress bars.
3. **Modern-shell selector twins** — the `:has()` detail-page conditions that
   were anchored on `.mainAnimatedPages` get `.skinBody` twins (pages are
   mounted inside `.skinBody` in the modern layout; the twins are harmless
   duplicates in the classic layout).
4. **Space-separated colour twins** — `--accentSpace`, `--tintSpace`,
   `--tint2Space` (the modern UI composes alpha as `rgb(R G B / a)`, which
   needs spaces instead of commas).
5. **Stable MUI hooks** — two rules that targeted 10.11-era emotion hash
   classes (`.css-4yt2of` / `.css-139vfv2`, which change between MUI
   versions) are now covered by `.MuiPaper-root` styling. The old selectors
   are still present but no longer relied upon.

Nothing was removed. The two upstream files ship a stray `}` (a lost
`@media` opener around the `#albumsTab` rules); it was left as-is because
browsers recover from it identically.

## Files

| File | Look | Notes |
|---|---|---|
| `Finimalism11.css` | **Modded UI & animations** (custom sidebar, home screen, fades) | flagship variant |
| `Finimalism12.css` | **Classic Jellyfin layout** with finimalism styling | closest to upstream look |
| `finimalism10.11.css` | Classic layout, purple accent, no tint system | kept working on 12.2 |
| `finimalism10.11-black.css` | Black + red accent overlay | kept working on 12.2 |
| `finimalism-just-black.css` | Pure black overlay (use over another finimalism file) | inherits `--accent` |
| `theme.css` | TV/client tweaks (focus outlines, guide, dialogs) | unchanged, works as-is |
| `no-animation.css` | Disables the item-details animations | unchanged |
| `horizontal-scrolling.css` | Horizontal home rows | unchanged |
| `scrollers-11.css` | Horizontal scrollers for the V11 home | unchanged |
| `libraryCardAriaText.css` | Hides duplicated library card aria text | unchanged |
| `blurryface's-half-and-half.css` | Half-and-half detail layout tweak | unchanged |

## Install

Paste the import line into **Dashboard → Branding → CSS**
(or per-user: **User Settings → Display → CSS**).

### Option A — host the files and import them

Upload this folder to your own GitHub repo (or any HTTPS host) and use:

```css
@import url("https://cdn.jsdelivr.net/gh/cottonbk/finimalism-jellyfin12.2@main/Finimalism11.css");
```

```css
@import url("https://cdn.jsdelivr.net/gh/cottonbk/finimalism-jellyfin12.2@main/Finimalism12.css");
```

### Option B — no hosting

Open the CSS file, copy **all** of its content and paste it directly into the
Branding CSS box. (Long, but zero setup.)

For Jellyfin Media Player use the latest [Jellyfin Desktop 2.0.0+](https://flathub.org/en/apps/org.jellyfin.JellyfinDesktop).

## Custom colours

Same system as upstream — override the variables **after** the import:

```css
@import url("https://cdn.jsdelivr.net/gh/YOUR-USER/finimalism@main/Finimalism12.css");

/* Custom Colours */
:root {
  --accent: 202, 20, 20;          /* classic UI (comma-separated) */
  --accentSpace: 202 20 20;       /* modern UI (space-separated)  */
  --backdropBlur: blur(0px);
  --rounding: 0.8em;
  --tint: 0, 0, 0;
  --tintSpace: 0 0 0;
  --tint2: 176, 34, 7;
  --tint2Space: 176 34 7;
}
```

> Tip: set the `*Space` twin to the same colour as its comma twin — the
> modern shell uses the space form for alpha blending. If you skip it, only
> the classic layout follows your custom colour.

## Helpers

Horizontal scrollers on the V11 home page:

```css
@import url("https://cdn.jsdelivr.net/gh/YOUR-USER/finimalism@main/scrollers-11.css");
```

## Feedback / verification checklist

Tested against jellyfin-web 12.2.0 sources (modern + legacy layouts,
desktop + mobile breakpoints). If something looks off, include:
screen resolution, device type (desktop/mobile/TV), client
(Jellyfin Media Player, Chrome, Firefox…) and which theme file you use.
