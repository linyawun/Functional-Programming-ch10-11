---
highlighter: shiki
css: unocss
colorSchema: light
transition: fade-out
mdc: true
layout: center
glowSeed: 4
title: 《簡約的軟體開發思維：用 Functional Programming 重構程式 - 以 JavaScript 為例》 Ch10-11
info: |
  ## 《簡約的軟體開發思維：用 Functional Programming 重構程式 - 以 JavaScript 為例》 讀書會導讀：Ch10-11
  - speaker：Monica
  - 《簡約的軟體開發思維：用 Functional Programming 重構程式 - 以 JavaScript 為例》 讀書會：[Functional-Programming-Book-Club](https://github.com/Tech-Book-Community/Functional-Programming)
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

```yaml
glow: right
glowOpacity: 0.1
```

# 頭等抽象化

### 頭等抽象化(first-class abstraction)

- 程式語言中，函式本身作為頭等物件，可像資料一樣被傳遞、賦值、作為參數傳入其他函式
  <img src='/images/first-class-abstraction.png' width='350px' />
- 優點
  - 函式行為可被抽象化，可動態決定程式執行邏輯
  - 靈活性、可重複使用性高

---

# 頭等抽象化

### 頭等抽象化(first-class abstraction)

- 舉例：JavaScript 函式可作為參數傳給其他函式

```js {*}{maxHeight:'260px'}
const applyOperation = (a, b, operation) => operation(a, b);

const addition = (x, y) => x + y;
const multiply = (x, y) => {
  if (typeof x === 'number' && typeof y === 'number') {
    return x * y;
  } else if (typeof x === 'string' && typeof y === 'number') {
    return x.repeat(y);
  } else {
    throw new Error('Invalid types for multiplication');
  }
};

console.log(applyOperation(5, 3, addition)); // 8
console.log(applyOperation(5, 3, multiply)); //15
console.log(applyOperation('Good', 'day', addition)); //"Goodday"
console.log(applyOperation('Hi', 3, multiply)); //"HiHiHi"
```

- `addition` 和 `multiply`：具體實作邏輯的函式
- `applyOperation`：抽象化的高階函式

---

```yaml
layout: center
glowOpacity: 0.8
glowSeed: 12
```

# Ch10 頭等函式（1）

---

# 程式碼異味：函式名稱中的隱性引數<span class='text-lg'> (implicit argument)</span>

此程式碼異味代表：程式中可用頭等物件改進的部分
- 程式碼異味特徵
<ol class='ml-6'> 
  <li>程式中有許多相似的實作</li>
  <li>上述實作差異出現在函式名稱上</li>
</ol>



---

# 程式碼異味：函式名稱中的隱性引數<span class='text-lg'> (implicit argument)</span>

### 重構 1：將隱性引數轉為顯性

- 情境：函式名稱中有隱性引數
- 重構方向：加入新參數，將可能的隱性引數轉為頭等物件，再以顯性方式傳入
- 重構步驟

<ol class='ml-6'> 
  <li>辨識出函式名稱裡的隱性引數</li>
  <li>加入新參數以接收顯性輸入</li>
  <li>利用新參數取代函式實作中的固定值</li>
  <li>更改呼叫程式碼</li>
</ol>

- 優點
  - 有效表達程式碼意圖
  - 有機會消除重複元素

---

# 程式碼異味：函式名稱中的隱性引數<span class='text-lg'> (implicit argument)</span>

### 重構 2：以回呼取代主體實作

- 重構方向：將一段程式的主體區塊(有變化處)轉換為回呼，可將該區塊行為傳入頭等函式內
- 重構步驟

<ol class='ml-6'> 
  <li>辨識一段程式的前段、主體、後段區塊</li>
  <li>將所有區塊包裝成函式 a</li>
  <li>將主體區塊擷取成函式 b，將其當成引數傳入函式 a</li>
</ol>

- 優點
  - 輕鬆用既有程式建立更高階函式

---

# 行銷部門仍須與開發小組協調

- 若 MegaMart 行銷部門有新需求，仍須要求開發小組在 API 加入新函式
- 開發小組現有 API 函式無法滿足的新需求如
  - 設定購物車內商品的價格
  - 設定購物車內商品的數量
  - 設定購物車內商品的運費

<div class='mt-12' v-click='1'>
  <p class='my-0!'>
    🤯 抽象屏障為何無法滿足上述需求?
  </p>
  <p class='my-0! opacity-75 text-sm'>
    功能和程式碼都很類似，抽象屏障卻無法提供幫助
  </p>
</div>

<div class='mt-6' v-click='2'>
  <p class='my-0!'>
    🧐 行銷人員原本能直接操作購物車資料，現在因為抽象屏障隱藏了細節，無法直接修改
  </p>
  <p class='my-0! opacity-75 text-sm'>
    須等開發人員開發才能繼續工作
  </p>
</div>

---

# 程式碼異味：函式名稱中的隱性引數
開發人員按新需求寫函式
```js {*}{maxHeight:'220px'}
function setPriceByName(cart, name, price){
    var item = cart[name];
    var newItem = objectSet(item, 'price', price);
    var newCart = objectSet(cart, name, newItem);
    return newCart;
}

function setQuantityByName(cart, name, quant){
    var item = cart[name];
    var newItem = objectSet(item, 'quantity', quant);
    var newCart = objectSet(cart, name, newItem);
    return newCart;
}

function setShippingByName(cart, name, ship){
    var item = cart[name];
    var newItem = objectSet(item, 'shipping', ship);
    var newCart = objectSet(cart, name, newItem);
    return newCart;
}

function setTaxByName(cart, name, tax){
    var item = cart[name];
    var newItem = objectSet(item, 'tax', tax);
    var newCart = objectSet(cart, name, newItem);
    return newCart;
}

function objectSet(object, key, value){
    var copy = Object.assign({}, object);
    copy[key] = value;
    return copy;
}

```
































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
