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

# About Diffusion Models?

<span></span>

<!-- 由於 DPM 訓練穩定、取樣結果的品質與多樣性優異，使其近年來在各種生成任務上倍受重視。 -->
Diffusion Probabilistic Models (DPM) have gained significant attention in recent years for various generative tasks due to their **Training Stability, Superior Quality and Diversity** of sampled results.

<!-- 不同於 GAN 與 VAE 等等單步生成方法，Diffusion model 透過 Markov Chain, Score Matching 等等理論將真實分佈與已知分佈( e.g. Gaussian) 串連。並通過訓練好的 Neural Network 迭代數百至數千的採樣步驟，將取樣自已知分佈的樣本轉換到真實分佈。-->

Unlike single-step generative methods such as GANs and VAEs, Diffusion Models leverage theories like Markov Chain or Score Matching to **Connect the Data Distribution with a known Distribution (e.g., Gaussian)**. By utilizing a trained neural network, the model iteratively performs hundreds to thousands of sampling steps to transform samples drawn from the known distribution into the true distribution.

<img class="w-3/4 m-auto" src="/assets/diffusion-models-unconditional_image_generation.webp"/>

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

## About Diffusion Models?

<span class="text-xl">Disadvantages and Problems</span>

<!--然而，正如前述的特點，DPM 需要藉由多次迭代才能產出結果，這不僅讓他耗時過長，Neural Network 的估計誤差更會在迭代過程中不斷累積，導致最終生成品質下降。這與 Autoregressive Models 中出現的 exposure bias problem 相似。-->

However, as mentioned earlier, DPMs require multiple iterations to generate results, making the process time-consuming. Additionally, estimation errors from the neural network accumulate during these iterations, leading to a decline in the final generation quality. This is similar to the exposure bias problem observed in Autoregressive Models.

<img class="w-3/4 m-auto" src="/assets/exposure_bias.png"/>

<SlideCurrentNo class="absolute bottom-4 right-8" />

<style>
h2 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
.slidev-layout h2 + p {
    margin-top: 0rem;
    margin-bottom: 1rem;
    opacity: 0.5;
}
</style>

---

## About Diffusion Models?

<span class="text-xl">Exposure Bias in DPM</span>

<!--過去的研究 \[[1](https://proceedings.mlr.press/v202/ning23a.html)\] 透過統計分析發現 exposure bias 遵循 Gaussian Distribution，並在訓練時加入額外的 gaussian noise 來模擬  exposure bias，以增強模型在採樣過程中受到 bias 干擾時的穩健性。-->

Previous research \[[DDPM-IP](https://proceedings.mlr.press/v202/ning23a.html)\] has found through statistical analysis that exposure bias follows a Gaussian distribution. To enhance the model's robustness against bias interference during the sampling process, additional Gaussian noise is introduced during training to simulate exposure bias.

<div class="grid grid-cols-3">
<img class="m-auto" src="/assets/hist_gaussian_error_900step_pixel_3072.png"/>
<img class="m-auto" src="/assets/hist_gaussian_error_600step_pixel_2576.png"/>
<img class="m-auto" src="/assets/hist_gaussian_error_300step_pixel_2049.png"/>
</div>

<SlideCurrentNo class="absolute bottom-4 right-8" />

<style>
h2 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
.slidev-layout h2 + p {
    margin-top: 0rem;
    margin-bottom: 1rem;
    opacity: 0.5;
}
</style>


---

## About Diffusion Models?

<span class="text-xl">Exposure Bias in DPM</span>

<!--然而，重新訓練模型需要耗費高昂的成本，為了在不重新訓練模型的前提下減輕 exposure bias，本研究提出 Time Step Shifting 的技巧，在採樣的過程動態調整樣本對應的時間步（等同於 gaussian noise 的標準差）。並在沒有重新訓練模型的情況下提昇採樣品質。-->

However, retraining the model can be costly. To mitigate exposure bias without the need for retraining, this study proposes a technique called Time Step Shifting. This method dynamically adjusts the time step (equivalent to the standard deviation of Gaussian noise) during the sampling process, improving the sampling quality without requiring model retraining.

<img src="/assets/figure-1.png"/>

<SlideCurrentNo class="absolute bottom-4 right-8" />

<style>
h2 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
.slidev-layout h2 + p {
    margin-top: 0rem;
    margin-bottom: 1rem;
    opacity: 0.5;
}
</style>

---

# Investigating Exposure Bias in DPMs

<span class="text-xl">Background: Diffusion Probabilistic Models</span>

<img class="w-3/4 m-auto" src="/assets/forward-backward.png"/>
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

## Investigating Exposure Bias in DPMs

<img class="w-4/5 m-auto" src="/assets/genetative_stage.png">

<v-switch>
  <template #0>
  <!--為了探究 Exposure Bias 在 DPM 個步驟的嚴重程度，作者分析了生成樣本與其所對應真實樣本之間的 MSE，在分析過程中發現如果起始採樣步驟 $t_s$ 太接近 $T$ ，就會導致生成的樣本與真實樣本無關。-->
  To investigate the severity of exposure bias at different steps in DPM, the authors analyzed the Mean Squared Error (MSE) between generated samples and their corresponding real samples.
  </template>
  <template #1>
  To investigate the severity of exposure bias at different steps in DPM, the authors analyzed the Mean Squared Error (MSE) between generated samples and their corresponding real samples.

  Additionally, they found that if the initial sampling step <katex-elem expr="t_s"/> is too close to <katex-elem expr="T"/>, the generated samples become unrelated to the real samples.
  </template>
  <template #2></template>
</v-switch>

<my-rect v-after.hide h=100 w=300 x=50 y=245 color=white></my-rect>
<my-rect v-after.hide h=65 w=235 x=350 y=280 color=white></my-rect>
<katex-elem
  v-after.hide
  class="absolute left-85 top-63 pointer-events-none"
  expr="\Large{\cdots}"></katex-elem>


<div v-after>

- **Stage 1** ( $t>t_s$ )
	- $\text{MSE}_t=\cfrac{\Vert \hat x_t-x_t\Vert^2_2}{n},\;\hat x_{T-1:t_s+1}\sim p_\theta(x_{T-1:t_s+1}|x_T)$
- **Stage 2** ( $t\leq t_s$ )
	- $\text{MSE}_t=\cfrac{\Vert \hat x^t_0-x_0\Vert^2_2}{n},\;\hat x^t_0=x_\theta(\hat x_{t+1}),\;\hat x_{t_s-1:1}\sim p_\theta(x_{t_s-1:1}|x_{t_s})$

</div>

<!--
- This observation also reveals that the image generation process of diffusion models basically contains two stages.
	- In the first stage, the model moves the Gaussian distribution towards the image distribution and no modes are presented at this stage, which means we can not know which images will be generated.
	- In the second stage, the prediction shows that clear patterns and modes are presented. We can predict which images will be generated following the backward process.
- This observation led us to divide the error computation into two stages.
-->

<SlideCurrentNo class="absolute bottom-4 right-8" />

<style>
h2 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
.slidev-layout h2 + p {
    margin-top: 0rem;
    margin-bottom: 1rem;
    opacity: 0.5;
}
</style>

---

## Investigating Exposure Bias in DPMs

<img src="/assets/figure-3.png">

<v-switch>
<template #0>

- The generation error in the second phase did not decrease as the time steps progressed;
</template>
<template #1>

- The generation error in the second phase did not decrease as the time steps progressed;
- Instead, it gradually increased.
</template>

</v-switch>

<my-rect v-after h=150 w=125 x=140 y=80 color=#ff555555></my-rect>
<my-rect v-after h=150 w=125 x=360 y=80 color=#ff555555></my-rect>
<my-rect v-after h=150 w=125 x=575 y=80 color=#ff555555></my-rect>
<my-rect v-after h=150 w=125 x=790 y=80 color=#ff555555></my-rect>


<SlideCurrentNo class="absolute bottom-4 right-8" />

<style>
h2 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
.slidev-layout h2 + p {
    margin-top: 0rem;
    margin-bottom: 1rem;
    opacity: 0.5;
}
</style>

---

# Alleviating Exposure Bias via Time Step Shifting

- The Exposure Bias $e_t$ in DPMs follows Normal Distribution. \[[DDPM-IP](https://proceedings.mlr.press/v202/ning23a.html)\]

$$\begin{aligned}
\tilde x_{t}=&x_{t}+e_{t}
=\sqrt{\hat\alpha_{t}}x_0+\sqrt{1-\hat\alpha_{t}}\epsilon+e_t\\
=&\sqrt{\hat\alpha_{t}}x_0+\lambda_t\tilde\epsilon_t
\end{aligned}$$

- Assume that $\tilde x_t$ still follows a certain distribution of $x_{T:0}$

<img src="/assets/figure-1.png"/>

<!--
- 在前期實驗中，作者透過計算樣本分佈之間的相關性驗證假設
	- $\Large{C(\tilde x_{t},t')=e^{-\text{dis}(\tilde x_{t},x_{t'})}}$
	- 由於訓練時 $\tilde x_t = x_t$ ，因此相關性 $C(x_t,t)=1$ 。然而在推理時 $\tilde x_t$ 是由 Neural Network 擬合的 backward process 所採樣得來，由 Figure.3 的實驗已經確認會與真實分佈的 $x_t$ 出現偏差。
-->

<SlideCurrentNo class="absolute bottom-4 right-8" />

<style>
h2 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
.slidev-layout h2 + p {
    margin-top: 0rem;
    margin-bottom: 1rem;
    opacity: 0.5;
}
</style>

---

## Alleviating Exposure Bias via Time Step Shifting

<img src="/assets/time_step_shift.png"/>
<my-rect h=20 w=780 x=130 y=350 color=white></my-rect>

<katex-elem
  class="absolute left-85 top-88 pointer-events-none"
  expr="\large{C(\tilde x_{t-1},t-t_s)=e^{-\text{dis}(\tilde x_{t-1},x_{t-t_s})}}"></katex-elem>

- 由 backward process 採樣的 $\tilde x_{t-1}$ 與偏移 $t_s$ 的真實樣本 $x_{t-t_s}$ 計算的相關性確認帶有 exposure bias 的 $\tilde x_t$ 分佈確實能對應至 $x_T$ 到 $x_0$ 之間的某個 state。

<SlideCurrentNo class="absolute bottom-4 right-8" />

<style>
h2 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
.slidev-layout h2 + p {
    margin-top: 0rem;
    margin-bottom: 1rem;
    opacity: 0.5;
}
</style>

---

## Alleviating Exposure Bias via Time Step Shifting

<img src="/assets/algo.png"/>

<my-rect h=20 w=45 x=350 y=335 color=#2288AA55></my-rect>
<span
 class="absolute left-85 top-88 pointer-events-none color-[#2288AA]">If $t$ is large, $\sqrt{\bar\alpha_t}x_0$ will be very close to 0.</span>

<SlideCurrentNo class="absolute bottom-4 right-8" />

<style>
h2 {
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

<img src="/assets/main_results.png"/>

<SlideCurrentNo class="absolute bottom-4 right-8" />

---

# Experiment
<span class="text-2xl">Discussion on Efficiency and Performance</span>

<div class="grid grid-cols-5">
<img class="col-span-3" src="/assets/figure-5.png"/>
<p class="col-span-3"></p>
</div>

<SlideCurrentNo class="absolute bottom-4 right-8" />

---

# Experiment
<span class="text-2xl">Comparison with Training-Required Method ADM-IP</span>

<img src="/assets/table-2.png"/>


<SlideCurrentNo class="absolute bottom-4 right-8" />

---

# Experiment
<span class="text-2xl">Influence of Window Sizes and Cutoff values</span>

<img class="w-4/5 m-auto" src="/assets/figure-7.png"/>


<SlideCurrentNo class="absolute bottom-4 right-8" />

---

# Experiment
<span class="text-2xl">Timestep Trajectory</span>

<img class="w-4/5 m-auto" src="/assets/timestep_trajectory.png"/>

---
layout: end
---