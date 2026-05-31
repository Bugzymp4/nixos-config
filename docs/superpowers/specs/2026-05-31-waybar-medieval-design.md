# Waybar Medieval Theme — Illuminated Scroll

**Date:** 2026-05-31  
**File:** `home.nix` → `programs.waybar.style`

---

## Summary

Restyle the waybar configuration to a **Parchment & Ink / Illuminated Scroll** medieval theme that matches the existing nixos.png wallpaper (aged parchment with embossed NixOS logo). The bar becomes a single unified strip rather than three separate floating boxes.

---

## Visual Design Decisions

### Layout
- **Single connected bar** — one `window#waybar` background with border, spanning left to right. Remove the separate per-section backgrounds from `.modules-left`, `.modules-center`, `.modules-right`.
- Position, margins, height, and spacing remain unchanged from current config.

### Color Palette

| Role | Value | Notes |
|---|---|---|
| Bar background | `rgba(212, 188, 132, 0.92)` | Warm parchment beige, slightly transparent |
| Outer border | `#7a5030` | Dark oak brown |
| Inner shadow | `rgba(90, 56, 32, 0.30)` | Double-stroke illusion via `box-shadow: inset` |
| Primary text | `#2e1a08` | Dark sepia ink |
| Secondary / dim text | `rgba(46, 26, 8, 0.38)` | Inactive workspaces, separators |
| Active workspace highlight | `rgba(122, 80, 48, 0.22)` | Warm tan fill |
| Warning state | `#8b5e2a` | Darker amber |
| Critical state | `#6b2a0a` | Deep sienna |
| Charging / active | `#3a2010` | Near-black sepia |

### Typography
- **Font:** `"Noto Serif", Georgia, serif` (Noto Serif already installed via `noto-fonts`)
- **Base size:** 12px
- **Clock size:** 15px, weight 700
- **Clock label size:** 9px, italic, uppercase, letter-spacing 0.18em
- **Letter spacing:** 0.04em base, 0.06em workspaces

### Clock
- Format string: `{:%H:%M}` (unchanged)
- Label above: `⸻ The Hour ⸻`
- Implementation: CSS `#clock::before` pseudo-element with `content: "⸻ The Hour ⸻"`. If GTK does not render `::before` correctly, fall back to a single-line format: `"⸻ {:%H:%M}"`.

### Workspaces
- Display: Roman numerals (I · II · III · IV)
- **Requires** workspace names to be set in the mango WM config as `"I"`, `"II"` etc. OR the mango compositor already emits names that can be overridden.
- Fallback: If MangoWC does not support custom workspace names, the workspace numbers (1, 2, 3…) will render in Noto Serif, which is still classically elegant. Roman numeral naming can be revisited separately.
- Active workspace: background `rgba(122, 80, 48, 0.22)`, weight 700
- Inactive workspaces: `opacity: 0.35`

### Module Separators
- Right-side modules (cpu · memory · pulseaudio · network · battery) separated by ` · ` in dimmed sepia (`rgba(46, 26, 8, 0.30)`)
- Existing nerd font icons are kept unchanged

### Texture
- **Attempt:** SVG `feTurbulence` noise via data URI as `background-image` on `window#waybar`, layered over `background-color`.
- **Fallback:** If GTK CSS does not render the SVG background-image, the texture block is simply removed and the flat parchment color is used — no other changes needed.

---

## Implementation Scope

Only `programs.waybar.style` in `home.nix` is modified. No new packages are added (Noto Serif is already installed). The waybar `settings` block is unchanged except potentially `format` on the `clock` module if the `::before` fallback is needed.

---

## Out of Scope

- Renaming workspaces in the mango config (can be done as a follow-up)
- Changing module layout or adding/removing modules
- Styling other applications (terminal, rofi, etc.)
