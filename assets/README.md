# OpenTide brand assets

Pantone Reflex Blue `#001489` · Inter SemiBold · Material Tsunami icon.

## Files

| SVG / PNG prefix | What it is |
|------------------|------------|
| `logo-{normal\|inverse}` | Main logo — pill + circle icon + **opentide** |
| `site-pill-{normal\|inverse}` | Website link — globe + **opentide.org** |
| `license-pill-{normal\|inverse}` | Licence badge — gavel + **EUPL 1.2** |
| `badge-{normal\|inverse}` | OTIDE ring seal badge |
| `icon-{normal\|inverse}` | Circle icon mark / favicon |

**normal** = blue fill, white accent · **inverse** = white fill, blue accent

PNGs are named `{prefix}-{size}px.png` (height for pills/logo, square for badge/icon).

## Regenerate

From the Open Design brand workspace:

```bash
python3 export-assets.py --sync-github
```
