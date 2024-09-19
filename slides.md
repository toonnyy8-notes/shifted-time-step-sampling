---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: shifted-time-step-sampling
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
---

<h1>Alleviating Exposure Bias in Diffusion Models<br/>through Sampling with Shifted Time Steps</h1>

<br/>

Mingxiao Li$^{*1}$,
Tingyu Qu$^{*1}$,
Ruicong Yao$^2$,
Wei Sun$^1$ and Marie-Francine Moens$^1$

<p class="text-justify pl-70">
<katex-elem expr='^1' /> Department of Computer Science, KU Leuven<br/>
<katex-elem expr='^2' /> Department of Mathematics, KU Leuven
</p>

## ICLR 2024

<div class="abs-br m-6 flex gap-2">
  <a href="https://github.com/toonnyy8-notes/shifted-time-step-sampling" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

<style>
  .slidev-layout.cover h1 {
    font-size: 2.5rem;
    line-height: 3rem;
  }
</style>

---
transition: fade-out
---

# What is Diffusion Models?


<!--
You can have `style` tag in markdown to override the style for the current page.
Learn more: https://sli.dev/features/slide-scope-style
-->

<SlideCurrentNo class="absolute bottom-4 right-8" />

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

<!--
Here is another comment.
-->

---

# Investigating Exposure Bias in Diffusion Probabilistic Models

Hover on the bottom-left corner to see the navigation's controls panel, [learn more](https://sli.dev/guide/ui#navigation-bar)

## Keyboard Shortcuts

|     |     |
| --- | --- |
| <kbd>right</kbd> / <kbd>space</kbd>| next animation or slide |
| <kbd>left</kbd>  / <kbd>shift</kbd><kbd>space</kbd> | previous animation or slide |
| <kbd>up</kbd> | previous slide |
| <kbd>down</kbd> | next slide |

<!-- https://sli.dev/guide/animations.html#click-animation -->
<img
  v-click
  class="absolute -bottom-9 -left-7 w-80 opacity-50"
  src="https://sli.dev/assets/arrow-bottom-left.svg"
  alt=""
/>
<p v-after class="absolute bottom-23 left-45 opacity-30 transform -rotate-10">Here!</p>


<SlideCurrentNo class="absolute bottom-4 right-8" />

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---

# Alleviating Exposure Bias via Time Step Shifting

<SlideCurrentNo class="absolute bottom-4 right-8" />

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---

# Experiment
<span class="text-2xl">Main Results</span>

<SlideCurrentNo class="absolute bottom-4 right-8" />

---

# Experiment
<span class="text-2xl">Discussion on Efficiency and Performance</span>

<SlideCurrentNo class="absolute bottom-4 right-8" />

---

# Experiment
<span class="text-2xl">Comparison with Training-Required Method ADM-IP</span>


<SlideCurrentNo class="absolute bottom-4 right-8" />

---

# Experiment
<span class="text-2xl">Influence of Window Sizes and Cutoff values</span>


<SlideCurrentNo class="absolute bottom-4 right-8" />
