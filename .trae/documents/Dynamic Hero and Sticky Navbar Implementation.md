# Implementation Plan: Dynamic Hero and Sticky Navbar

## 1. Feature Implementation
### Hero Component (`src/components/Hero.svelte`)
- Updated logic to provide a smooth fade-out and parallax effect on scroll instead of shrinking height.
- Implemented `opacity` and `scale` calculations based on `scrollY`.
- Confined star animation to the full canvas height for continuous effect.

### Navbar Component (`src/components/Navbar.astro`)
- Modified CSS classes to ensure the navbar stays fixed at the top center.
- Added `fixed top-0 left-1/2 -translate-x-1/2 w-full z-50` to the `card-base` container.
- Removed default max-width constraints that might conflict with centering or full-width behavior if desired (though `max-w-[var(--page-width)]` is kept for content containment, positioning is now explicit).

### Layout Integration (`src/layouts/MainGridLayout.astro`)
- Updated `mainPanelTop` logic. When Hero is enabled, content starts at `100vh` to sit below the initial full-screen hero.
- Verified that content scrolls over the fixed/fading hero.

## 2. Verification
- Verified that the navbar remains visible and centered at the top.
- Verified that the hero section fades out and scales down smoothly as the user scrolls down.
- Verified that scrolling back to the top restores the full-screen hero.
- Verified mobile responsiveness.

## 3. Usage
- No new configuration needed beyond enabling the hero in `src/config.ts`.
- The effect is automatic based on scroll position.
