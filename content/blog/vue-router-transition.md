---
title: "Page Transitions with Vue Router"
date: 2024-03-26T16:55:11+08:00
tags: ["code"]
categories: ["code"]
comments: true
---

I've been working on a mobile web PWA built with Vue recently.

Since it's a PWA, the experience should feel as close to native as possible. Page changes on the web are abrupt, with no animation at all, which breaks the illusion and feels too much like a website. So I wanted to add some page transitions.

The Vue Router docs have a similar [example](https://router.vuejs.org/guide/advanced/transitions.html).

## Implementation

Following that example, we can combine Vue's [`<transition>`](https://vuejs.org/guide/built-ins/transition.html) with [`<router-view>`](https://router.vuejs.org/guide/advanced/router-view-slot.html) to animate route changes.

```html
<router-view v-slot="{ Component }">
  <transition name="fade" mode="out-in">
    <component :is="Component" :key="route.path"></component>
  </transition>
</router-view>
```

```css
/* fade */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
r .fade-leave-to {
  opacity: 0;
}
```

Navigating between routes now shows a transition:

<video controls style="width: min(calc(100vw - 28px), 390px);">
  <source src="https://assets.madcodelife.com/blog/2024/03/efff01316cea7426d21639f67ab8cf4a.mov" type="video/mp4">
</video>

But that's not quite what we want. We want something like the iOS navigation stack, so the animation needs some work:

```html
<router-view v-slot="{ Component }">
  <transition name="slide-left">
    <component :is="Component" :key="route.path"></component>
  </transition>
</router-view>
```

```css
/* slide */
.slide-left-enter-active,
.slide-left-leave-active {
  transition: transform 300ms ease, opacity 400ms step-end;
  backface-visibility: hidden;
  width: 100vw;
  height: 100vh;
}

.slide-left-enter-active {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1;
}

.slide-left-enter-active {
  background-color: #fff;
}

.slide-left-enter-from {
  transform: translateX(80vw);
}
```

Let's try it:

<video controls style="width: min(calc(100vw - 28px), 390px);">
  <source src="https://assets.madcodelife.com/blog/2024/03/c19c1f8f550097cca5e2a694f3d1c269.mov" type="video/mp4">
</video>

It's starting to look right. One detail remains: it can't tell forward navigation from going back, so we need a custom animation for each.

For this we use the [`beforeEach`](https://router.vuejs.org/api/interfaces/Router.html#beforeEach) and [`afterEach`](https://router.vuejs.org/api/interfaces/Router.html#afterEach) hooks provided by Vue Router:

```javascript
// Track the current position in the browser history
let position = 0

router.beforeEach((to) => {
  // Forward or back?
  const isBack = position > window.history.state.position
  to.meta.transition = isBack ? "slide-right" : "slide-left"
})

router.afterEach(() => {
  // Update after navigation
  position = window.history.state?.position || 0
})
```

Set the transition dynamically:

```html
<router-view v-slot="{ Component, route }">
  <transition :name="route.meta.transition">
    <component :is="Component" :key="route.path"></component>
  </transition>
</router-view>
```

Add the `slide-right` styles:

```css
/* slide */
.slide-left-enter-active,
.slide-left-leave-active,
.slide-right-enter-active,
.slide-right-leave-active {
  transition: transform 300ms ease, opacity 400ms step-end;
  backface-visibility: hidden;
  width: 100vw;
  height: 100vh;
}

.slide-left-enter-active,
.slide-right-enter-active,
.slide-right-leave-active {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1;
}

.slide-right-leave-active {
  z-index: 1;
}

.slide-left-enter-active {
  background-color: #fff;
}

.slide-left-enter-from {
  transform: translateX(80vw);
}

.slide-right-enter-from {
  transform: translateX(-10vw);
}

.slide-right-leave-from {
  transform: translateX(40vw);
}

.slide-right-leave-active {
  transform: translateX(100vw);
}
```

The final result:

<video controls style="width: min(calc(100vw - 28px), 390px);">
  <source src="https://assets.madcodelife.com/blog/2024/03/b22079812a48a20bd5f96d8cb099e387.mov" type="video/mp4">
</video>

## More

Some routes or scenarios may not need an animation. You can use the same `beforeEach` hook to set the transition to `none` for those routes.

```javascript
router.beforeEach((to) => {
  // Skip the animation for certain pages
  if (to.path === "/no-transition") {
    to.meta.transition = "none"
  } else {
    const isBack = position > window.history.state.position
    to.meta.transition = isBack ? "slide-right" : "slide-left"
  }
})
```

## Possible issues

### Flicker on iOS swipe-back

When swiping back in iOS Safari with the transition set to `none`, a frame of the previous page sometimes flashes. My guess is that the `Transition` component hits an edge case when the named transition doesn't exist in the CSS.

Defining styles for the `none` transition fixes it:

```css
.none-leave-from,
.none-leave-active,
.none-leave-to {
  display: none;
}
```

Transitions are fully customizable; this is just a simple implementation, so adjust it to fit your needs.

I hope this helps you understand and use transitions in `Vue` and `Vue Router`. If you run into any problems or have suggestions, feel free to leave a comment.

Thanks for reading, see you next time.
