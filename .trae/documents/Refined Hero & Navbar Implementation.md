# Implementation Plan: Smooth Fade-out Hero & Centered Navbar

## 1. Feature Implementation
### Hero Component (`src/components/Hero.svelte`)
- **Smooth Fade-out**: Instead of shrinking the height, the Hero now stays fixed in the background.
- **Opacity Transition**: `opacity` decreases as `scrollY` increases (fades out completely by 50vh scroll).
- **Scale Effect**: Slight zoom-out effect on scroll for a parallax feel.
- **Parallax Translate**: `translateY` moves the hero up slightly slower than the scroll speed.
- **Star Canvas**: Updated to draw stars across the full `canvas.height` (100vh) instead of a shrinking visible area.

### Navbar (`src/components/Navbar.astro`)
- **Positioning**: Added `fixed top-0 left-1/2 -translate-x-1/2 w-full z-50` classes.
- **Centering**: `left-1/2 -translate-x-1/2` ensures the navbar stays horizontally centered relative to the viewport.
- **Width**: `max-w-[var(--page-width)]` maintains the correct width constraint while being centered.

### Layout Integration (`src/layouts/MainGridLayout.astro`)
- **Main Content Top**: Set `mainPanelTop` to `"100vh"` when `enableHero` is true. This ensures the main content starts *below* the initial full-screen hero.
- **Scroll Interaction**: As the user scrolls down, the "main-grid" (content) moves up over the fixed Hero. The Hero fades out in the background.
- **Scroll Up**: Scrolling back to the top reveals the full opacity Hero again.

## 2. Verification
- **Navbar**: Stays pinned to the top center.
- **Hero**: Starts full screen. Fades out smoothly on scroll down. Reappears on scroll up.
- **Content**: Flows naturally below the hero initially, then covers it.
- **Mobile**: Navbar and Hero adjustments are responsive.

## 3. Usage
- No config changes required if `hero.enable` is already true.
- The effect is now "Full screen -> Scroll Down -> Content covers fading Hero".
