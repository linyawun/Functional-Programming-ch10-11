---
highlighter: shiki
css: unocss
colorSchema: light
transition: fade-out
mdc: true
layout: center
glowSeed: 4
title: 《簡約的軟體開發思維：用 Functional Programming 重構程式 - 以 Javascript 為例》 Ch10-11
info: |
  ## 《簡約的軟體開發思維：用 Functional Programming 重構程式 - 以 Javascript 為例》 讀書會導讀：Ch10-11
  - speaker：Monica
  - 《簡約的軟體開發思維：用 Functional Programming 重構程式 - 以 Javascript 為例》 讀書會：[Functional-Programming-Book-Club](https://github.com/Tech-Book-Community/Functional-Programming)
author: Monica
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
fonts:
  # basically the text
  sans: Robot Noto Sans
  # use with `font-serif` css class from UnoCSS
  serif: Robot Noto Serif
  # for code blocks, inline code, etc.
  mono: Fira Code
# slidev style reference: https://github.com/antfu/talks/tree/main/2024-06-13
---

# 《簡約的軟體開發思維：用 Functional Programming 重構程式 - 以 Javascript 為例》 Ch10~11

## 頭等函式 (1)、(2)

<div class='mt-10 opacity-60'>
<p>speaker：Monica</p>
<p>2025.5.29 @Tech-Book-Community</p>
</div>

<style>
  h1{
    @apply font-bold
  }
  h2{
    @apply text-dark-700;
  }
  .slidev-layout p{
    margin-top: 0px;
    margin-bottom: 0.5rem;
  }
</style>

---

```yaml
layout: intro
glowSeed: 15
glowOpacity: 0.3
```

# Hi, I'm Monica

<div class="opacity-80">

- 一年多經驗的前端工程師 <br>
- 常用技術：React、Next.js <br>
- 興趣：聽音樂、看漫畫、看小說、偶爾看劇 <br>
- 想學的很多，但學得很慢...

</div>

<div my-10 w-min flex="~ gap-1" items-center justify-center>
  <mdi:medium op50 ma text-xl/>
  <div><a href="https://medium.com/@linyawun031" target="_blank" class="border-none! font-300">Monica</a></div>
</div>

---

```yaml
layout: center
glow: bottom
```

# 前次回顧

---

```yaml
layout: center
```

# 本次導讀 Ch10-11

## 頭等函式 (1)、(2)

<p class="opacity-80">筆記工：Mi</p>

---

# 頭等抽象化

### 頭等(first-class)物件、頭等函式、頭等抽象化

- first-class：譯為「頭等」、「一級」
  <p class='my-0! opacity-80 text-sm'>本書使用「頭等」</p>

<br/>

- 不同類型的頭等元素稱呼
  - first-class values (頭等值)
  - first-class entities (頭等實體)
  - first-class objects (頭等物件)
  <p class='my-0! opacity-80 text-sm'>本書使用「頭等物件」</p>

---

# 頭等抽象化

### 頭等(first-class)物件、頭等函式、頭等抽象化

- 可做到以下操作的程式語言元素稱為「頭等物件」
  - 可賦值給變數
  - 可作為參數傳入函式
  - 可作為函式的傳回值
  - 可存入資料結構中
- JavaScript 基本資料型別：符合以上 4 種操作 -> 是頭等物件 ✅
  - 基本資料型別：數字、字串、物件、布林值、陣列
- JavaScript 函式：**抽象化**後符合以上 4 種操作 -> 是頭等物件 ✅
  - 又稱**頭等函式(first-class functions)**

---

# 頭等抽象化

---

# Navigation

Hover on the bottom-left corner to see the navigation's controls panel, [learn more](https://sli.dev/guide/ui#navigation-bar)

## Keyboard Shortcuts

|                                                    |                             |
| -------------------------------------------------- | --------------------------- |
| <kbd>right</kbd> / <kbd>space</kbd>                | next animation or slide     |
| <kbd>left</kbd> / <kbd>shift</kbd><kbd>space</kbd> | previous animation or slide |
| <kbd>up</kbd>                                      | previous slide              |
| <kbd>down</kbd>                                    | next slide                  |

<!-- https://sli.dev/guide/animations.html#click-animation -->

<img
  v-click
  class="absolute -bottom-9 -left-7 w-80 opacity-50"
  src="https://sli.dev/assets/arrow-bottom-left.svg"
  alt=""
/>

<p v-after class="absolute bottom-23 left-45 opacity-30 transform -rotate-10">Here!</p>
