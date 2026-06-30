# OpenTide brand assets

Official OpenTide logo assets — **Pantone Reflex Blue** (PMS Reflex Blue C · `#001489`).

**Typography:** Inter SemiBold (600) — text outlined as paths in SVG (no font fallback)  
**Icon:** Material Symbols Tsunami (official path)

## Layout

```
assets/
  svg/   — standalone vector sources (portable, hardcoded fills)
  png/   — raster exports at common sizes
```

Each component ships in **normal** and **inverse** states:

| State | Primary | Secondary |
|-------|---------|-----------|
| **normal** | Pantone `#001489` | White `#ffffff` |
| **inverse** | White `#ffffff` | Pantone `#001489` |

## Components

| Folder | File prefix | Description | PNG sizes |
|--------|-------------|-------------|-----------|
| `lockup/` | `opentide-lockup-pill-circle-highlight-{state}-pantone-reflex-blue` | Primary logo — pill + circle icon highlight | 32–256px height |
| `site-pill/` | `opentide-site-pill-opentide-org-{state}-pantone-reflex-blue` | Site link — wireframe globe + opentide.org | 20–64px height |
| `license-pill/` | `opentide-license-pill-eupl-1-2-{state}-pantone-reflex-blue` | Licence badge — gavel + EUPL 1.2 | 20–64px height |
| `seal/` | `opentide-seal-badge-otide-ring-{state}-pantone-reflex-blue` | OTIDE ring seal badge (square) | 64–512px |
| `circle-mark/` | `opentide-circle-mark-tsunami-{state}-pantone-reflex-blue` | Icon-only circle mark / favicon | 16–256px |

`{state}` is `normal` or `inverse`.

## Usage

- **Light backgrounds** → `normal` lockup, `normal` or `inverse` pills depending on contrast needs
- **Dark backgrounds** → `inverse` lockup
- **Favicon / avatar** → `circle-mark/` at 16–64px
- **Org profile / docs header** → `lockup/` at 64–128px height
- **EUPL attribution** → `license-pill/` alongside content

## Filename pattern

```
opentide-{component}-{state}-pantone-reflex-blue.svg
opentide-{component}-{state}-pantone-reflex-blue-{N}px.png
```

## Regenerate

Source-of-truth design files and export scripts live in the Open Design brand workspace. Re-run `python3 export-assets.py` there, then sync into this repository.
