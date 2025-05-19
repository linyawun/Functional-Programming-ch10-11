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

# 《簡約的軟體開發思維：用 Functional Programming 重構程式 - 以 JavaScript 為例》 Ch10~11

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

```js {*|1,8,15,22}{maxHeight:'160px'}
function setPriceByName(cart, name, price) {
  var item = cart[name];
  var newItem = objectSet(item, 'price', price);
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}

function setQuantityByName(cart, name, quant) {
  var item = cart[name];
  var newItem = objectSet(item, 'quantity', quant);
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}

function setShippingByName(cart, name, ship) {
  var item = cart[name];
  var newItem = objectSet(item, 'shipping', ship);
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}

function setTaxByName(cart, name, tax) {
  var item = cart[name];
  var newItem = objectSet(item, 'tax', tax);
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}

function objectSet(object, key, value) {
  var copy = Object.assign({}, object);
  copy[key] = value;
  return copy;
}
```

- 程式碼異味：重複
  - 唯一差異：指定屬性名的字串(`'price'`、`'quantity'`、`'shipping'`、`'tax'`)
    - 字串出現在函式名稱中 -> 函式名稱的某部分是該函式的引數
- 「函式名稱中的隱性引數」：未實際傳入任何引數，而用函式名稱指出提供給函式的值
- 如何解決此程式碼異味?
  - 將屬性名稱頭等化(first-class)，將屬性作為引數傳入

---

# 辨識程式碼異味：函式名稱中的隱性引數

- 程式碼異味特徵
  - 函式實作非常相似
  - 上述實作的不同處顯示在函式名稱上
    <br/>
    函式名稱中有差異處，視為隱性引數

---

# 重構 1：將隱性引數轉換為顯性參數

- 將隱性引數轉為顯性參數的步驟
<ol class='ml-6'> 
  <li>辨識出函式名稱裡的隱性引數</li>
  <li>加入新參數以接收顯性輸入</li>
  <li>利用新參數取代函式實作中的固定值</li>
  <li>更改呼叫程式碼</li>
</ol>

---

# 重構 1：將隱性引數轉換為顯性參數

重構前後的 `setPriceByName`

- 重構前

<div class='ml-6'>

```js {*|1}
function setPriceByName(cart, name, price) {
  // 1. 辨識函式名稱的隱性引數：Price 是函式名稱中的隱性引數
  var item = cart[name];
  var newItem = objectSetItem(item, 'price', price);
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}

cart = setPriceByName(cart, 'shoe', 13);
cart = setQuantityByName(cart, 'shoe', 3);
cart = setShippingByName(cart, 'shoe', 0);
cart = setTaxByName(cart, 'shoe', 2.34);
```

</div>

---

# 重構 1：將隱性引數轉換為顯性參數

重構前後的 `setPriceByName`

- 重構後
  - 四個既有函式變成一個單一函式
  - 將屬性名稱（`'price'`、`'quantity'`、`'shipping'`、`'tax'`）當成頭等物件（first-class values）
    - 頭等：可將屬性值作為引數傳入函式、可儲存在變數或陣列

<div class='ml-6'>

```js {*|1|3|8-12|all}{maxHeight:'200px'}
function setFieldByName(cart, name, field, value) {
  // 2. 加入新參數以接收顯性輸入：加入代表屬性的顯性參數 field，將代表屬性值的參數名稱普適化為 value
  var item = cart[name];
  var newItem = objectSetItem(item, field, value); // 3. 利用新參數取代函式實作中的固定值：實作中使用新參數 field
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}

// 4. 更改呼叫程式碼
cart = setFieldByName(cart, 'shoe', 'price', 13);
cart = setFieldByName(cart, 'shoe', 'quantity', 3);
cart = setFieldByName(cart, 'shoe', 'shipping', 0);
cart = setFieldByName(cart, 'shoe', 'tax', 2.34);
```

<p class='text-sm'>
🧐 讓 API 使用者自己打字串好像不怎麼安全？
</p>
</div>

---

# 討論

- Q：不必再請開發小組專門撰寫設定某屬性的函式？
  - A：是，現在可存取任意屬性，只要把屬性值作為字串傳入函式即可
- Q：怎麼知道屬性名稱是什麼？
  - A：將屬性名稱作為抽象屏障一部分，寫在 API 規格書裡
- Q：如果之後加入新購物車或商品屬性怎麼辦？
  - A：不論既有或新加的，`setFieldByName` 都可處理，只要提供新屬性名稱即可
- 👍 本來行銷部門要記多個函式，還要發出新增函式請求 -> 現在只需知道一個函式和幾個屬性名稱字串
  - 待補圖

---

# 辨識頭等與非頭等

### JavaScript 的頭等物件（first-class objects）

- **數字、字串、布林值、陣列、物件、函式** 都是「頭等物件」
  - 可以：
    - 指派給變數
    - 當作參數傳給函式
    - 作為函式的回傳值
    - 存進資料結構（如陣列、物件）
- **函式** 也是頭等物件
  - 所有能對數字、字串等做的操作，對函式也都適用

---

# 辨識頭等與非頭等

### JavaScript 的非頭等物件

- **非頭等物件**：無法像變數一樣操作、傳遞或儲存
  - 舉例
    - 運算子（operator）：`+`、`-`
    - 控制流程：`for` 迴圈、`if`、`try/catch`
- 非頭等物件存在於所有程式語言，不可避免
  - 💡 關鍵：將本來的非頭等物件「頭等化」

---

```yaml
glow: right
```

# 辨識頭等與非頭等

再看一次重構

<img src='/images/refactor1-example.png' width='480px' />

- 將函式名稱轉為頭等化字串，再將其作為引數傳入
- 重構成功的原因：利用 JavaScript 可用字串存取物件屬性的功能
- 優點
  - 提高彈性
  - 解決程式碼異味

💡 重複出現的重構模式：先辨識出一個非頭等物件，再將其頭等化，進而解決某些問題 -> FP 重要技巧

---

# 用字串當屬性名稱會不會增加錯誤發生率？

如果行銷人員打錯字（打錯屬性名稱）怎麼辦？

- 解法
  - 編譯期檢查（compile-time checks）
  - 執行期檢查（run-time checks）

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
#
