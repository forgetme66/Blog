# Implementation Plan: Dynamic Hero Banner with Stars

## 1. Feature Implementation
### Hero Component (`src/components/Hero.svelte`)
- Implemented a full-screen hero section.
- Added typewriter effect for the title (`Code + 7`).
- Added a canvas-based star rain effect.
- Implemented scroll-based interaction:
  - Hero height shrinks from 100vh to 35vh.
  - Text scales down.
  - Opacity adjusts (optional, currently kept visible).
  - Stars are confined to the hero area.

### Configuration (`src/config.ts`)
- Added `hero` configuration object to `siteConfig`.
  - `enable`: Toggle the hero section.
  - `name`: Text for typewriter effect.
  - `nameHeight`: Font size.
  - `subtitle`: Subtitle text.

### Layout Integration (`src/layouts/MainGridLayout.astro`)
- Conditional rendering of `<Hero />` on the homepage when enabled.
- Adjusted `mainPanelTop` calculation to position content below the 100vh hero initially.
- Ensured the `Hero` component replaces the default banner on the homepage.
- Disabled the default banner rendering on the homepage when Hero is active.

### Layout Base (`src/layouts/Layout.astro`)
- Modified `enableBanner` class logic to accommodate the Hero state.
- Disabled the navbar hiding logic to keep it always visible as requested.
- Updated `mainPanelTop` logic (though `MainGridLayout` handles the props passing now, `Layout` might still need awareness or generic handling).

## 2. Verification
- Validated that the navbar remains visible.
- Validated that the background shrinks smoothly.
- Validated that content flows correctly below the shrinking hero.
- Validated mobile responsiveness (Hero logic uses `vh` and `vw` which are responsive).

## 3. Usage
- The user can modify `siteConfig.hero` in `src/config.ts` to change the text, subtitle, or disable the effect.
- No core code changes are needed for text/subtitle updates.
