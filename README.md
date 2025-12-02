````markdown
# AnimateScroll

AnimateScroll is a tiny CSS layer that connects [Animate.css](https://animate.style/) animations to modern CSS scroll driven timelines.

It does not change how your animations look. It only changes when they run, based on how far an element has scrolled into the viewport.

No JavaScript. No extra build step. Just CSS.

---

## Features

- Scroll driven reveal effects using `animation-timeline: view()`
- Works with any Animate.css effect (fade, zoom, backIn, etc.)
- Simple hooks via `.animatescroll` or `.scroll-animate`
- Helper classes for common scroll ranges and offset like behaviour
- Respects `prefers-reduced-motion`
- Graceful fallback if scroll driven animations are not supported

---

## Requirements

- **Animate.css v4** (loaded before AnimateScroll)
- A browser that supports:
  - `animation-timeline: view()`
  - `animation-range`

If these features are not available, AnimateScroll does nothing and your elements simply appear without scroll driven behaviour.

---

## Installation

Add Animate.css and AnimateScroll to your page:

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/animate.css@4/animate.min.css">
<link rel="stylesheet" href="AnimateScroll.css">
````

Copy `AnimateScroll.css` from this repository into your project and adjust the path as needed.

---

## Basic usage

1. Add your usual Animate.css classes for the effect you want
2. Add either `.animatescroll` or `.scroll-animate`
3. Optionally add a helper class for the scroll range

```html
<section class="animate__animated animate__fadeInUpBig animatescroll as-range-entry">
  Scroll driven fade in from bottom
</section>
```

### Example

```html
<section class="animate__animated animate__backInUp animatescroll as-range-entry">
  I animate while I enter the viewport
</section>

<section class="animate__animated animate__backInRight scroll-animate as-range-entry">
  Same behaviour using the alias .scroll-animate
</section>
```

Animate.css still controls:

* which keyframes are used
* easing and general motion
* delay and repeat (via classes like `animate__delay-1s`)

AnimateScroll only decides which part of the scroll progress drives the animation.

---

## Core concepts

### Hooks

Use one of these as a base:

* `.animatescroll`
* `.scroll-animate` (semantic alias)

They must be combined with `animate__animated` from Animate.css.

```html
<div class="animate__animated animate__zoomInUp animatescroll">
  Zoom in while scrolling
</div>
```

### Range helpers

These classes control which part of the element’s visibility drives the animation:

```css
.as-range-entry   { --as-range: entry; }
.as-range-cover   { --as-range: cover; }
.as-range-contain { --as-range: contain; }
```

* `entry`
  The animation runs while the element enters the viewport

* `cover`
  The animation is driven while the element moves through the viewport
  from entering to leaving

* `contain`
  The animation only runs while the element is fully visible

Usage:

```html
<div class="animate__animated animate__fadeInUpBig animatescroll as-range-entry">
  Entry range
</div>

<div class="animate__animated animate__fadeInRightBig animatescroll as-range-cover">
  Cover range
</div>

<div class="animate__animated animate__zoomInUp animatescroll as-range-contain">
  Contain range
</div>
```

---

## Offset style helpers

Instead of changing the viewport or using pixel offsets, AnimateScroll uses `animation-range` to target a slice of the entry phase.

This gives an “earlier” or “later” feeling without dealing with `view()` insets.

```css
/* Early: animation finishes earlier in the entry phase */
.as-offset-early-soft   { --as-range: entry 0% 70%; }
.as-offset-early-strong { --as-range: entry 0% 40%; }

/* Late: animation starts later in the entry phase */
.as-offset-late-soft    { --as-range: entry 30% 100%; }
.as-offset-late-strong  { --as-range: entry 60% 100%; }
```

Examples:

```html
<!-- Early soft: most motion happens as the element just appears -->
<div class="animate__animated animate__backInUp animatescroll as-range-entry as-offset-early-soft">
  Early soft offset
</div>

<!-- Early strong: motion is compressed into the very beginning of entry -->
<div class="animate__animated animate__backInUp animatescroll as-range-entry as-offset-early-strong">
  Early strong offset
</div>

<!-- Late soft: element is partially visible before motion really starts -->
<div class="animate__animated animate__backInUp animatescroll as-range-entry as-offset-late-soft">
  Late soft offset
</div>

<!-- Late strong with Animate.css delay -->
<div class="animate__animated animate__backInUp animatescroll as-range-entry as-offset-late-strong animate__delay-1s">
  Late strong offset plus Animate.css delay
</div>
```

---

## Custom ranges

You are not limited to the predefined helpers.

AnimateScroll exposes a single custom property:

```css
--as-range
```

It is directly mapped into `animation-range`. You can override it inline for fine grained control:

```html
<div
  class="animate__animated animate__backInUp animatescroll"
  style="--as-range: entry 15% 85%;">
  Custom animation range between 15% and 85% of entry
</div>
```

This is useful when you want to tune the feel of an effect in a specific layout without adding another helper class.

---

## Accessibility and fallbacks

AnimateScroll is designed as progressive enhancement.

* If `prefers-reduced-motion: reduce` is set
  The scroll driven behaviour is not applied

* If `animation-timeline: view()` is not supported
  The `@supports` block will not run
  Elements behave like plain Animate.css content, or just appear if you do not use Animate.css

In both cases the content remains visible and usable.

---

## Demo

This repository contains a demo file that shows all available combinations:

* basic usage with `.animatescroll`
* alias `.scroll-animate`
* all range helpers
* all offset style helpers
* a custom inline range example
* combination with Animate.css delay

To run it:

1. Clone the repository or download the files
2. Open `demo.html` in a modern browser
3. Scroll slowly up and down to see how each section behaves

You can also copy the demo markup into a CodePen or similar playground to tweak it for your own layout.

---

## Limitations

* The animation scrubs with scroll
  Scrolling up drives the animation in reverse. For “play once then stay” behaviour you still need some JavaScript to remove the scroll timeline after the first run.

* Browser support
  Scroll driven animations and `animation-timeline: view()` are relatively new. Expect support first in modern Chromium based browsers and Safari. Always test on your target platforms.

* No callbacks
  AnimateScroll is CSS only. It does not provide hooks to run JavaScript after an animation finishes.
