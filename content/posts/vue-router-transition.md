---
title: "Vue Router 过渡动效"
date: 2024-03-26T16:55:11+08:00
tags: ["Vue", "Vue Router", "动画", "CSS", "前端"]
categories: ["技术"]
---

最近在做 H5 PWA 项目，技术栈是 Vue。

由于是 PWA 项目，所以应用的体验要无限往原生靠近，网页的过渡是很生硬的，没有任何的动效可言，很出戏，网页的感觉太强了，所以就想着给网页加上一些过渡动效。

翻阅了一下 Vue Router 的文档，提供了类似的[案例](https://router.vuejs.org/guide/advanced/transitions.html)。

## 如何实现

按照上述文档的案例，我们可以用 Vue 提供的 [`<transition>`](https://vuejs.org/guide/built-ins/transition.html) 搭配 [`<router-view>`](https://router.vuejs.org/guide/advanced/router-view-slot.html) 来实现过渡动效。

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

再进行路由跳转的时候已经可以看到过渡效果了：

<video controls style="width: min(calc(100vw - 28px), 390px);">
  <source src="https://p.madcodelife.com/blog/2024/03/efff01316cea7426d21639f67ab8cf4a.mov" type="video/mp4">
</video>

但这个效果不是我们想要的，我们需要的是类似 iOS 页面栈的效果，所以还需要改造下动画：

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

试下效果：

<video controls style="width: min(calc(100vw - 28px), 390px);">
  <source src="https://p.madcodelife.com/blog/2024/03/c19c1f8f550097cca5e2a694f3d1c269.mov" type="video/mp4">
</video>

已经可以看到初步的效果了，还有一个细节需要处理，就是没办法区分前进和返回，我们需要自定义动画。

这里需要用到 Vue Router 提供的[`beforeEach`]('https://router.vuejs.org/api/interfaces/Router.html#beforeEach')和[`afterEach`](https://router.vuejs.org/api/interfaces/Router.html#afterEach)两个钩子函数：

```javascript
// 记录当前网页历史的位置
let position = 0

router.beforeEach((to) => {
  // 判断是前进还是返回
  const isBack = position > window.history.state.position
  to.meta.transition = isBack ? "slide-right" : "slide-left"
})

router.afterEach(() => {
  // 跳转后清空
  position = window.history.state?.position || 0
})
```

动态设置当前过渡动画：

```html
<router-view v-slot="{ Component, route }">
  <transition :name="route.meta.transition">
    <component :is="Component" :key="route.path"></component>
  </transition>
</router-view>
```

补充`slide-right`动画样式：

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

最终效果：

<video controls style="width: min(calc(100vw - 28px), 390px);">
  <source src="https://p.madcodelife.com/blog/2024/03/b22079812a48a20bd5f96d8cb099e387.mov" type="video/mp4">
</video>

## 更多

有的路由或者场景可能不需要动画，一样可以通过上述`beforeEach`钩子的指定某些路由动画效果为`none`。

```javascript
router.beforeEach((to) => {
  // 过滤某些页面的动画效果
  if (to.path === "/no-transition") {
    to.meta.transition = "none"
  } else {
    const isBack = position > window.history.state.position
    to.meta.transition = isBack ? "slide-right" : "slide-left"
  }
})
```

## 可能遇到的问题

### iOS 手势返回闪屏

在 iOS Safari 上使用手势返回时，如果动画效果是`none`，有概率会闪现一帧上一个页面的内容，猜测是因为`Transition`组件识别到`css`中不存在的动画时会有一些边缘情况。

通过设置过渡效果`none`的样式即可修复：

```css
.none-leave-from,
.none-leave-active,
.none-leave-to {
  display: none;
}
```

过渡效果可以自定义，这里只是一个简单的实现，可以根据自己的需求来调整。

希望这篇文章能帮助你更好地理解和使用`Vue`和`Vue Router`的过渡动效。如果你在实践中遇到任何问题，或者有任何建议，欢迎在评论区留言。

感谢你的阅读，期待下次再见。
