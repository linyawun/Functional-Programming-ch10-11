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
favicon: "https://grokkingsimplicity.com/favicon.ico"
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

# 前次回顧：分層設計(1)、分層設計(2)

## 原則 1：讓實作更直觀

- 同層函式細節程度一致
- 只關注當前層級
- 通用函式在低層，變動小
- 上層函式變動大

---

```yaml
layout: center
glow: bottom
```

# 前次回顧：分層設計(1)、分層設計(2)

## 原則 2：以抽象屏障輔助實作

- 上方：忽略實作細節
- 下方：忽略上層用途
- 目的：降低溝通成本、提升可讀性

---

```yaml
layout: center
glow: bottom
```

# 前次回顧：分層設計(1)、分層設計(2)

## 原則 3：讓下層函式保持簡約不變

- 屏障內函式越少越好
- 新功能在高層實現
- 底層函式應保持簡潔易記

---

```yaml
layout: center
glow: bottom
```

# 前次回顧：分層設計(1)、分層設計(2)

## 原則 4：分層只要舒適即可

- 層數適中即可
- 評估標準：是否舒適、是否滿足需求

---

```yaml
layout: center
glow: bottom
```

# 前次回顧：分層設計(1)、分層設計(2)

## 呼叫圖告訴我們的訊息

- 可維護性：上層箭頭少，修改時較安全
- 可測試性：上層箭頭多，測試價值較高
- 可重用性：下方函式少，重用性較高

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

- 能做這些操作的程式語言元素稱為「頭等物件」：
  - 賦值給變數
  - 作為參數傳入函式
  - 作為函式的傳回值
  - 存進資料結構
- JavaScript 基本資料型別（數字、字串、物件、布林、陣列）符合以上 4 種操作 ⮕ 是頭等物件 ✅
- JavaScript 函式**抽象化**後符合以上 4 種操作 ⮕ 是頭等物件 ✅
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
  - 函式行為可被抽象化，可動態決定執行邏輯
  - 靈活性、可重複使用性高

---

```yaml
layout: center
glowOpacity: 0.8
glowSeed: 12
```

# Ch10 頭等函式 (1)

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
  var newItem = objectSet(item, "price", price);
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}

function setQuantityByName(cart, name, quant) {
  var item = cart[name];
  var newItem = objectSet(item, "quantity", quant);
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}

function setShippingByName(cart, name, ship) {
  var item = cart[name];
  var newItem = objectSet(item, "shipping", ship);
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}

function setTaxByName(cart, name, tax) {
  var item = cart[name];
  var newItem = objectSet(item, "tax", tax);
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
  - 差異只在屬性名稱(`'price'`、`'quantity'`、`'shipping'`、`'tax'`)
    <p class='my-0! opacity-75 text-sm'>這些字串其實是應傳入的引數，卻寫在函式名稱裡</p>
- 如何解決此程式碼異味?
  - 將屬性名稱頭等化(first-class)，將屬性作為引數傳入

<div class='note-block'>
函式名稱中的隱性引數：未實際傳入引數，而用函式名稱指出提供給函式的值
</div>

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
  var newItem = objectSetItem(item, "price", price);
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}

cart = setPriceByName(cart, "shoe", 13);
cart = setQuantityByName(cart, "shoe", 3);
cart = setShippingByName(cart, "shoe", 0);
cart = setTaxByName(cart, "shoe", 2.34);
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

```js {*|2|4|8-13|all}{maxHeight:'200px'}
function setFieldByName(cart, name, field, value) {
  // 2. 加入新參數以接收顯性輸入：加入代表屬性的顯性參數 field，將代表屬性值的參數名稱普適化為 value
  var item = cart[name];
  var newItem = objectSetItem(item, field, value); // 3. 利用新參數取代函式實作中的固定值：實作中使用新參數 field
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}

// 4. 更改呼叫程式碼
cart = setFieldByName(cart, "shoe", "price", 13);
cart = setFieldByName(cart, "shoe", "quantity", 3);
cart = setFieldByName(cart, "shoe", "shipping", 0);
cart = setFieldByName(cart, "shoe", "tax", 2.34);
```

<p class='text-sm'>
🧐 讓 API 使用者自己打字串好像不怎麼安全？
</p>
</div>

---

```yaml
glow: top
```

# 討論

- Q：怎麼知道屬性名稱是什麼？
  - A：將屬性名稱作為抽象屏障一部分，寫在 API 規格書裡
- Q：如果之後加入新購物車或商品屬性怎麼辦？
  - A：不論既有或新加的，`setFieldByName` 都可處理，只要提供新屬性名稱即可

👍 本來行銷部門要記多個函式，還要發出新增函式請求 ⮕ 現在只需知道一個函式和幾個屬性名稱字串
  <img src='/images/refactor1-after-refactor.png' width='100%' />

---

# 辨識頭等與非頭等
JavaScript 的頭等物件（first-class objects）與非頭等物件

 <div class='text-4 leading-6'>

|              | 說明                                               | 舉例                                                                                      |
|--------------|----------------------------------------------------|------------------------------------------------------------------------------------------|
| 頭等物件     | <ul><li>可賦值給變數</li><li>可作為函式參數</li><li>可作為函式回傳值</li><li>可存進資料結構</li></ul>  | <ul><li>數字</li><li>字串</li><li>布林值</li><li>陣列</li><li>物件</li><li>函式</li></ul> |
| 非頭等物件   | <ul><li>不能像變數一樣賦值、傳遞、儲存</li><li>存在於所有程式語言，不可避免</li></ul>               | <ul><li>運算子（`+`、`-`）</li><li>控制流程（`for`、`if`、`try/catch`）</li></ul>                         |

</div>


<p class='mt-6'>💡 關鍵：將本來的非頭等物件「頭等化」</p>

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
  - 彈性高
  - 解決程式碼異味

💡 FP 重要技巧：先辨識出一個非頭等物件，再將其頭等化，進而解決某些問題

---

# 用字串當屬性名稱會不會增加錯誤發生率？

如果行銷人員打錯字（打錯屬性名稱）怎麼辦？

- 解法
  - 編譯期檢查（compile-time checks）
  - 執行期檢查（run-time checks）

---

# 用字串當屬性名稱會不會增加錯誤發生率？

如果行銷人員打錯字（打錯屬性名稱）怎麼辦？

### 編譯期檢查（compile-time checks）

- 需要靜態型別系統（static types system）
- JavaScript：沒有內建靜態型別系統，可用 TypeScript 彌補
  - TypeScript：可檢測字串是否符合既有屬性名稱，若字串有誤，型別檢測器在程式碼執行前就指出錯誤
- Java：可用 enum 型別確保屬性名稱正確性
- Haskell：可用「可辨識聯合（discriminated union）」達成類似目的
  - 可辨識聯合：「代數資料型別（algebraic data type）」中的「和型別（sum type）」，可用代數資料型別概括
- 靜態型別系統會隨語言不同而有變化，需看情況決定

---

# 用字串當屬性名稱會不會增加錯誤發生率？

如果行銷人員打錯字（打錯屬性名稱）怎麼辦？

### 執行期檢查（run-time checks）

- 在程式執行時進行，與編譯無關
- 一樣可告知傳入字串是否正確
- JavaScript 沒靜態型別系統，此方式可能更合適

```js {*}{maxHeight:'220px'}
var validItemFields = ["price", "quantity", "shipping", "tax"]; // 列出正確屬性名稱

function setFieldByName(cart, name, field, value) {
  if (!validItemFields.includes(field)) {
    // 當 field 引數值為頭等物件時，執行期檢測就很容易
    throw "Not a valid item field: " + "'" + field + "'.";
  }
  var item = cart[name];
  var newItem = objectSet(item, field, value);
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}

function objectSet(object, key, value) {
  // objectSet 是在第 6 章定義的
  var copy = Object.assign({}, object);
  copy[key] = value;
  return copy;
}
```

---

# 將屬性名稱頭等化，會不會造成 API 難以修改？

- 將屬性名稱頭等化，看起來像是暴露抽象屏障下的細節

  - 「購物車」與「商品」的屬性定義於抽象屏障以下

- 讓屏障之上的使用者傳入屬性名稱，會不會破壞抽象屏障、讓屬性難以變更？
  <div v-click='1'>

  - 不完全正確，內部實作細節沒有暴露
  - 🧊 API 的屬性「字串」不應更動
  - ✅ 屏障底層的屬性名稱仍可自由修改
  - 即使下層屬性真的更改，使用者仍可用相同「字串」，只要將該字串轉為正確屬性名稱即可
  </div>

---

# 將屬性名稱頭等化，會不會造成 API 難以修改？

- 舉例：若開發小組想將 `'quantity'` 改為 `'number'`，但不想影響屏障以上程式碼
  - 行銷人員可繼續用 `'quantity'` ，開發人員自行將字串轉為 `'number'`
  - 為何可轉換？
    - 已將屬性名稱頭等化
    - 能將屬性名稱存在陣列、物件，對其做邏輯判斷

<div class='ml-6'>

```js {*}{maxHeight:'200px'}
var validItemFields = ["price", "quantity", "shipping", "tax", "number"];
var translations = { quantity: "number" };

function setFieldByName(cart, name, field, value) {
  if (!validItemFields.includes(field)) {
    throw "Not a valid item field: " + field + ".";
  }
  if (translations.hasOwnProperty(field)) {
    field = translations[field]; // 將舊屬性名稱轉換成新名稱
  }
  var item = cart[name];
  var newItem = objectSet(item, field, value);
  var newCart = objectSet(cart, name, newItem);
  return newCart;
}
```

</div>

---

# 為什麼要用物件實作資料？

為何以物件實作「購物車」與「商品」？

- hash map 可用不同型別的鍵存取屬性值，適合表示鍵值對資料
- 「購物車」和「商品」屬於呼叫圖低層級，需被各函式存取
  - 以物件或陣列通用資料結構實作，可讓資料不受限於單一用途
    <img src='/images/low-level-data-type.png' width='700px' />

---

# 為什麼要用物件實作資料？

### 資料（Data）要能到處通用，而非侷限於少數介面

- Data 的優勢：詮釋方式多元
  - 可依不同目的，以多種方法使用或解釋 Data
  - 讓 Data 保持靈活，以便在程式各處重複使用的原則稱為 **「資料導向」（data orientation）**
- 如果用特定 API 綁定 Data，未來想用新方法處理就會受限

👍 商品、購物車等通用實體應以通用資料結構(如：物件、陣列)來實作

<div class='note-block mt-12'>
資料導向（data orientation）：以通用資料結構表示「與事件和實體有關之事實」的設計原則
</div>


---

# 討論

- 可以讓行銷團隊不必自行寫 `for` 迴圈嗎？
  - 需將 `for` 迴圈頭等化
  - 寫一個「以頭等函式為引數的新函式」，稱為**高階函式**

---

```yaml
glow: right
```

# 討論

### 頭等函式與高階函式

- 頭等函式：可將該函式當成另一個函式的引數
- 高階函式：此函式能接收其他函式作為引數
<img src='/images/first-class-and-high-order-function.png' width='450px' />
<p class='my-0! opacity-75 text-sm'>沒有頭等函式，就不可能寫出高階函式</p>

<div class='note-block mt-6'>
高階函式（high-order function）：以其他函式做為傳入引數或傳回值
</div>

---

# `for` 迴圈重構範例

走訪陣列的兩個 for 迴圈範例

- 料理與吃飯
  ```js
  for (var i = 0; i < foods.length; i++) {
    var food = foods[i];
    cook(food);
    eat(food);
  }
  ```
- 洗碗
  ```js
  for (var i = 0; i < dishes.length; i++) {
    var dish = dishes[i];
    wash(dish);
    dry(dish);
    putAway(dish);
  }
  ```
- 兩迴圈目的不同，程式碼相似
  - 將兩者改寫得完全一致，就可刪除其中一個

---

# 將兩個迴圈改寫成一致

<div class='note-block'>
<b>辨識函式名稱中的隱性引數</b>

1. 函式實作相似
2. 實作中的不同處反映在函式名稱上
</div>

<div class='note-block'>
<b>重構 1 的步驟</b>

1. 辨識出函式名稱裡的隱性引數
2. 加入新參數以接收顯性輸入
3. 利用新參數取代函式實作中的固定值
4. 更改呼叫程式碼
</div>

---

# 將兩個迴圈改寫成一致

1. 標注相同處
   - 目標：讓沒底線的也變相同
     <img src='/images/refactor1-forloop-step1.png' width='100%' />

---

# 將兩個迴圈改寫成一致

2. 將迴圈包在函式中

<div class="grid grid-cols-2 gap-x-4">

```js {*|1,7-8}
function cookAndEatFoods() {
  for (var i = 0; i < foods.length; i++) {
    var food = foods[i];
    cook(food);
    eat(food);
  }
}
cookAndEatFoods();
```

```js {*|1,8-9}
function cleanDishes() {
  for (var i = 0; i < dishes.length; i++) {
    var dish = dishes[i];
    wash(dish);
    dry(dish);
    putAway(dish);
  }
}
cleanDishes();
```

</div>

---

# 將兩個迴圈改寫成一致

3. 將功能相同、名稱不同的變數改為更普適化的名稱

<div class="grid grid-cols-2 gap-x-4">

```js {*|3}
function cookAndEatFoods() {
  for (var i = 0; i < foods.length; i++) {
    var item = foods[i]; // 將 food 統一命名為 item
    cook(item);
    eat(item);
  }
}
cookAndEatFoods();
```

```js {*|3}
function cleanDishes() {
  for (var i = 0; i < dishes.length; i++) {
    var item = dishes[i]; // 將 dish 統一命名為 item
    wash(item);
    dry(item);
    putAway(item);
  }
}
cleanDishes();
```

</div>

---

# 將兩個迴圈改寫成一致

4. 針對「函式名稱中有隱性引數」重構

- 🔺 程式碼異味
  - `cookAndEatFoods` 對應 `foods` 陣列
  - `cleanDishes` 對應 `dishes` 陣列
- 以重構 1 「將隱性引數轉為顯性」改寫

<div class="grid grid-cols-2 gap-x-4">

```js {*|1-2,9}
function cookAndEatArray(array) {
  // 改用更普適化名稱、接收顯性陣列參數
  for (var i = 0; i < array.length; i++) {
    var item = array[i];
    cook(item);
    eat(item);
  }
}
cookAndEatArray(foods); // 將陣列傳入
```

```js {*|1-2,10}
function cleanArray(array) {
  // 改用更普適化名稱、接收顯性陣列參數
  for (var i = 0; i < array.length; i++) {
    var item = array[i];
    wash(item);
    dry(item);
    putAway(item);
  }
}
cleanArray(dishes); // 將陣列傳入
```

</div>

---

# 將兩個迴圈改寫成一致

5. 將 for 迴圈主體擷取成新函式

- 兩函式唯一不同處：`for` 迴圈主體區塊
  ⮕ 可擷取為新函式

<div class="grid grid-cols-2 gap-x-4">

```js {*|1-2,5,8-13}
function cookAndEatArray(array) {
  // 1. 兩者不同處反映在函式名稱內的隱性引數：cookAndEat
  for (var i = 0; i < array.length; i++) {
    var item = array[i];
    cookAndEat(item); // 3. 呼叫擷取函式
  }
}

function cookAndEat(food) {
  // 2. 定義擷取函式
  cook(food);
  eat(food);
}

cookAndEatArray(foods);
```

```js {*|1-2,5,8-14}
function cleanArray(array) {
  // 1. 兩者不同處反映在函式名稱內的隱性引數：clean
  for (var i = 0; i < array.length; i++) {
    var item = array[i];
    clean(item); // 3. 呼叫擷取函式
  }
}

function clean(dish) {
  // 2. 定義擷取函式
  wash(dish);
  dry(dish);
  putAway(dish);
}

cleanArray(dishes);
```

</div>

---

```yaml
glow: top
```

# 將兩個迴圈改寫成一致

6. 針對「函式名稱中有隱性引數」重構

- 🔺 程式碼異味
  - `cookAndEatArray` 呼叫 `cookAndEat()`
  - `cleanArray` 呼叫 `clean()`
    <img src='/images/refactor1-forloop-step6.png' width='60%' />

---

# 將兩個迴圈改寫成一致

6. 針對「函式名稱中有隱性引數」重構

- 以重構 1 「將隱性引數轉為顯性」改寫

<div class="grid grid-cols-2 gap-x-4">

```js {*|1-2,5,14}
function operateOnArray(array, f) {
  // 1. 改成普適化的名字，加入新參數接受顯性引數
  for (var i = 0; i < array.length; i++) {
    var item = array[i];
    f(item); // 2. 在函式實作中使用新參數
  }
}

function cookAndEat(food) {
  cook(food);
  eat(food);
}

operateOnArray(foods, cookAndEat); // 3. 在呼叫程式碼中傳入引數
```

```js {*|1-2,5,15}
function operateOnArray(array, f) {
  // 1. 改成普適化的名字，加入新參數接受顯性引數
  for (var i = 0; i < array.length; i++) {
    var item = array[i];
    f(item); // 2. 在函式實作中使用新參數
  }
}

function clean(dish) {
  wash(dish);
  dry(dish);
  putAway(dish);
}

operateOnArray(dishes, clean); // 3. 在呼叫程式碼中傳入引數
```

</div>

---

# 將兩個迴圈改寫成一致

7. ✅ 兩個函式看起來一致，所有實作差異都能以引數彌補

- 差異：迴圈中的操作、被操作的陣列

---

# 將兩個迴圈改寫成一致

8. 目前程式碼中，兩個 `operateOnArray` 函式完全相同，可刪除其中一個

<div class="grid grid-cols-2 gap-x-4">

```js {*|1-7}
function operateOnArray(array, f) {
  // operateOnArray 和右方的完全相同
  for (var i = 0; i < array.length; i++) {
    var item = array[i];
    f(item);
  }
}

function cookAndEat(food) {
  cook(food);
  eat(food);
}

operateOnArray(foods, cookAndEat);
```

```js {*|1-6}
function operateOnArray(array, f) {
  for (var i = 0; i < array.length; i++) {
    var item = array[i];
    f(item);
  }
}

function clean(dish) {
  wash(dish);
  dry(dish);
  putAway(dish);
}

operateOnArray(dishes, clean);
```

</div>

---

# 將兩個迴圈改寫成一致

8. 目前程式碼中，兩個 `operateOnArray` 函式完全相同，可刪除其中一個

- `operateOnArray` 這類函式在 JavaScript 通常稱作 `forEach()`
  - 將 `operateOnArray` 改名為 `forEach()`
- `forEach()` 可接受函式引數，屬於高階函式

<div class="grid grid-cols-2 gap-x-4">

```js
function forEach(array, f) {
  // forEach 有一個引數為函式
  for (var i = 0; i < array.length; i++) {
    var item = array[i];
    f(item);
  }
}

function cookAndEat(food) {
  cook(food);
  eat(food);
}

forEach(foods, cookAndEat);
```

```js
function clean(dish) {
  wash(dish);
  dry(dish);
  putAway(dish);
}

forEach(dishes, clean);
```

</div>

---

# 將兩個迴圈改寫成一致

前後改寫比較

<div class="grid grid-cols-2 gap-x-4">

<div>

#### 原始程式

```js
for (var i = 0; i < foods.length; i++) {
  var food = foods[i];
  cook(food);
  eat(food);
}

for (var i = 0; i < dishes.length; i++) {
  var dish = dishes[i];
  wash(dish);
  dry(dish);
  putAway(dish);
}
```

</div>

<div>

#### 使用 `forEach()`

```js
// 這裡以匿名函式呈現
forEach(foods, function (food) {
  cook(food);
  eat(food);
});

forEach(dishes, function (dish) {
  wash(dish);
  dry(dish);
  putAway(dish);
});
```

- 需實作 `for` 迴圈不同功能時，只要呼叫 `forEach()` 即可

</div>
</div>

---

# 建立高階函式的步驟

1. 將目標程式碼包裹在新函式中
2. 用普適化名稱替換過於專一的命名
3. 將隱性引數改為顯性（重構 1）
4. 擷取函式
5. 再次將隱性引數改為顯性（重構 1）

<div v-click='1' class='mt-6'>
重構 2：「以回呼取代主體實作」可用更少步驟達到上述效果
</div>

---

# 重構 2：以回呼取代主體實作

- 問題：為了送出錯誤訊息，需將上千行程式碼包在 `try/catch` 內，`try/catch` 內都是重複的程式碼

  - `try` 和 `catch` 區塊無法分開，不能包成獨立函式重複使用

  ```js
  // 類似程式碼不斷重複
  try {
    saveUserData(user);
  } catch {
    logToSnapErrors(error); // 將錯誤送往 Snap Errors 服務
  }
  ```

<div v-click='1' class='mt-6'>

- 解法：以回呼取代主體實作
</div>

---

# 以回呼取代主體實作

1. 觀察重複區塊

<div class="grid grid-cols-2 gap-x-4">

```js
try {
  saveUserData(user);
} catch {
  logToSnapErrors(error); // catch 區塊都相同
}
```

```js
try {
  fetchProduct(productId);
} catch {
  logToSnapErrors(error); // catch 區塊都相同
}
```

</div>

---

# 以回呼取代主體實作

2. 辨識「前段-主體-後段」結構

<div class="grid grid-cols-2 gap-x-4">

```js
try {
  // 前段
  saveUserData(user); // 主體
} catch {
  // 後段
  logToSnapErrors(error); // 後段
} // 後段
```

```js
try {
  // 前段
  fetchProduct(productId); // 主體
} catch {
  // 後段
  logToSnapErrors(error); // 後段
} // 後段
```

</div>

- 相同處：前、後段
- 不同處：主體<span class='opacity-80 text-sm'>（像一個「可填空」區域）</span>
- 目標
  - 可重複利用相同處（前、後段）
  - 可自由變動不同處（主體）

<div class='note-block'>
JavaScript 裡，作為引數傳入的函式通常叫「回呼（callback）」
</div>

---

# 以回呼取代主體實作

3. 將所有區塊包裝成函式 a

<div class="grid grid-cols-2 gap-x-4">

<div>

原始程式

```js
try {
  saveUserData(user);
} catch {
  logToSnapErrors(error);
}
```

</div>

<div>

包裝後

```js {*|1,7-9}
function withLogging() {
  try {
    saveUserData(user);
  } catch {
    logToSnapErrors(error);
  }
}

withLogging(); // 定義完此函式後，才可呼叫
```

</div>

</div>

---

# 以回呼取代主體實作

4. 擷取主體實作（會變化的區塊），當成引數傳入 `withLogging`

<div class="grid grid-cols-2 gap-x-4">

<div>

目前程式

```js
function withLogging() {
  try {
    saveUserData(user);
  } catch {
    logToSnapErrors(error);
  }
}

withLogging();
```

</div>

<div>

擷取回呼

```js {*|2,4,11}
function withLogging(f) {
  // 1. f 代表要傳入的函式，成為一個參數
  try {
    f(); // 2. 在原本主體位置呼叫回呼函式
  } catch {
    logToSnapErrors(error);
  }
}

withLogging(function () {
  // 3. 傳入主體程式碼
  saveUserData(user); // 單行的匿名函式
});
```

</div>

</div>

---

```yaml
glow: bottom
```

# 以回呼取代主體實作

<div class='note-block'>
<b>重構 2 的步驟</b>

1. 辨識前段、主體與後段區塊
2. 將所有區塊包裝成函式
3. 將主體區塊擷取成回呼

</div>

---

# 內嵌與匿名函式

### 定義函式的方法

1. 全域定義
<div class='ml-6'>

- 在全域範圍定義、有名稱
- 可在程式任何地方呼叫

```js
function saveCurrentUserData() {
  // 定義全域範圍的函式
  saveUserData(user);
}

withLogging(saveCurrentUserData); // 將函式名稱當引數傳入
```

</div>

---

# 內嵌與匿名函式

### 定義函式的方法

2. 區域定義
<div class='ml-6'>

- 在區域範圍定義、有名稱
- 只能在特定區域內呼叫，外部無法存取
- 適用情境：想在特定範圍內多次呼叫，又不希望被範圍外程式呼叫

```js
function someFunction() {
  var saveCurrentUserData = function () {
    // saveCurrentUserData 此函式只能在 someFunction() 的範圍內呼叫
    saveUserData(user);
  };

  withLogging(saveCurrentUserData); // 利用名稱傳入函式
}
```

</div>

---

# 內嵌與匿名函式

### 定義函式的方法

3. 內嵌定義
<div class='ml-6'>

- 直接在需要的位置定義，沒有名稱
  - 稱為**匿名函式(anonymous function)**
- 適用情境：實作很短、僅需呼叫一次

```js
// function()...此函式沒有名稱，會在需要的地方直接定義，這就是內嵌匿名函式
withLogging(function () {
  saveUserData(user);
});
```

</div>

<div class='note-block'>

- 內嵌函式(inline function)：在有需要處直接定義的函式
  - 如：需傳入函式時，把該函式定義在參數列中
- 匿名函式(anonymous function)：沒有名稱的函式，通常用內嵌方式定義

</div>

---

# 為什麼要將 `saveUserData()` 包裹在函式中?

```js {*|8-12}{maxHeight:'150px'}
function withLogging(f) {
  try {
    f();
  } catch {
    logToSnapErrors(error);
  }
}

withLogging(function () {
  saveUserData(user); // 為何要將此函式包在另一函式?
});
```

- 直接寫 `withLogging(saveUserData(user));` 會怎麼樣?
  - `saveUserData(user)` 會**先**被呼叫，再進入 `withLogging()` 中
  <p v-click='2'>⛔ <code>saveUserData(user)</code> 的呼叫處在 <code>try</code> 區塊外，不如預期</p>

---

# 為什麼要將 `saveUserData()` 包裹在函式中?

```js {8-12}{maxHeight:'150px'}
function withLogging(f) {
  try {
    f();
  } catch {
    logToSnapErrors(error);
  }
}

withLogging(function () {
  saveUserData(user); // 為何要將此函式包在另一函式?
});
```

- 如何讓 `saveUserData(user)` 在正確地方被呼叫?
  - 將 `saveUserData(user)` 包在另一函式中，再當作引數傳入

<div v-click='1' class='ml-12'>
<p >💡 將 <code>saveUserData(user)</code> 先「存放」在包裝函式內，推遲到時機成熟時再執行</p>
```js
function(){
    saveUserData(user); // 在包裹函式被呼叫前，這一行都不會執行
}
```
</div>

---

# JavaScript 函式是頭等物件，可做很多操作

#### 指定給變數

```js
var f = function () {
  saveUserData(user);
};
```

<div class='mb-6'/>

#### 存放至 collection 資料結構（如：陣列、物件）

```js
array.push(function () {
  saveUserData(user);
});
```

---

# JavaScript 函式是頭等物件，可做很多操作

#### 當成引數傳入另一個函式

```js
withLogging(function () {
  saveUserData(user);
});
```

<b>作為函式引數，可自行決定函式執行時機</b>

<div class="grid grid-cols-3 gap-x-4">

<div>
  <p class='my-0!'>在特定條件下呼叫</p>

```js
function callOnThursday(f) {
  if (today === "Thursday") {
    f();
  }
}
```

</div>
<div>

<p class='my-0!'>等待一段時間再呼叫</p>

```js
function callTomorrow(f) {
  sleep(oneDay);
  f();
}
```

</div>
<div>

<p class='my-0!'>在指定脈絡下呼叫</p>

```js
function withLogging(f) {
  try {
    f();
  } catch (error) {
    logToSnapErrors(error);
  }
}
```

</div>
</div>

---

```yaml
layout: center
```

# 為什麼要將 `saveUserData()` 包裹在函式中?

<br class='hidden'>

為了將 `saveUserData()` 推遲到 `withLogging()` 的 `try/catch` 內執行，需將前者包裹後傳入後者

---

```yaml
layout: center
```

# 章節回顧

1. 什麼是「函式名稱中的隱性引數」？這種程式碼異味會造成什麼問題？

<div v-click='1' class='opacity-75 mt-6 text-sm'>

「函式名稱中的隱性引數」指的是：函式名稱裡藏有實際應該作為參數傳入的資訊
<br/>
（例如 `setPriceByName`、`setQuantityByName`，price/quantity 其實是參數）。

這會造成重複程式碼、彈性差、維護困難，因為每多一種需求就要多寫一個幾乎一樣的函式。

</div>

---

```yaml
layout: center
```

# 章節回顧

2. 請說明「頭等物件」和「頭等函式」的定義，並舉一個 JavaScript 的例子。

<div v-click='1' class='opacity-75 mt-6 text-sm'>

頭等物件：可以賦值給變數、作為參數傳遞、作為回傳值、存進資料結構的元素。

頭等函式：函式本身也是頭等物件，可以像資料一樣操作。

```js
const add = (a, b) => a + b;
const ops = [add, Math.max];
const result = ops[0](2, 3); // 5
```

</div>

---

```yaml
layout: center
```

# 章節回顧

3. 程式重構：請觀察下列兩個函式，將它們重構成一個更通用的高階函式，並簡述這樣做的好處。

```js
function printAllUppercase(strings) {
  for (var i = 0; i < strings.length; i++) {
    console.log(strings[i].toUpperCase());
  }
}

function printAllWithPrefix(strings, prefix) {
  for (var i = 0; i < strings.length; i++) {
    console.log(prefix + strings[i]);
  }
}

printAllUppercase(["foo", "bar"]);
// FOO
// BAR

printAllWithPrefix(["foo", "bar"], ">> ");
// >> foo
// >> bar
```

---

```yaml
layout: center
```

# 章節回顧

3. 程式重構：請觀察下列兩個函式，將它們重構成一個更通用的高階函式，並簡述這樣做的好處。

<div v-click='1' class='opacity-75 mt-6 text-sm'>

```js {*}{maxHeight:'250px'}
function forEach(array, f) {
  for (var i = 0; i < array.length; i++) {
    var item = array[i];
    f(item);
  }
}

forEach(["foo", "bar"], function (str) {
  console.log(str.toUpperCase());
});
// FOO
// BAR

forEach(["foo", "bar"], function (str) {
  console.log(">> " + str);
});
// >> foo
// >> bar
```

把「要怎麼印」這個行為抽象成參數（`fn`），不用每種印法都寫一個新函式，彈性高、重複少，任何印法都能用同一個高階函式處理。

</div>

---

```yaml
layout: center
```

# 章節回顧

4. 程式理解：請問下列程式碼的 `withLogging` 函式為什麼要傳入一個函式（callback），而不是直接傳入 `saveUserData(user)`？如果直接傳入會有什麼問題？

```js
function withLogging(f) {
  try {
    f();
  } catch {
    logToSnapErrors(error);
  }
}

withLogging(function () {
  saveUserData(user);
});
```

<div v-click='1' class='opacity-75 mt-6 text-sm'>

因為要讓 `saveUserData(user)` 的執行時機在 `try/catch` 區塊內。如果直接寫 `withLogging(saveUserData(user))`，`saveUserData(user)` 會在進入 `withLogging` 前就執行，錯誤不會被 `catch`。

傳入 callback（函式）可以推遲執行，確保在 `try/catch` 內才執行。

</div>

---

```yaml
layout: center
```

# 章節回顧

5. 請解釋什麼是「高階函式」（higher-order function），以及為什麼「頭等函式」是實現高階函式的前提？

<div v-click='1' class='opacity-75 mt-6 text-sm'>

高階函式是指「接受其他函式作為參數，或回傳函式」的函式。

只有當函式是頭等物件（能當作參數、回傳值、變數賦值等）時，才能把函式傳來傳去，才能寫出高階函式。
<br/>
如果函式不是頭等物件，就無法把它當作資料操作，高階函式也就不可能存在。

</div>

---

```yaml
layout: center
glowOpacity: 0.8
glowSeed: 12
```

# Ch11 頭等函式 (2)

---

# 函式名稱中的隱性引數與兩種重構

複習第 10 章

### 程式碼異味：函式名稱中的隱性引數

- 此程式碼異味代表：程式中可用頭等物件改進的部分
- 程式碼異味特徵
<ol class='ml-6'> 
  <li>程式中有許多相似的實作</li>
  <li>上述實作差異出現在函式名稱上</li>
</ol>

---

# 函式名稱中的隱性引數與兩種重構

複習第 10 章

### 重構 1「將隱性引數轉換為顯性」

1. 辨識出函式名稱裡的隱性引數
2. 加入新參數以接收顯性輸入
3. 利用新參數取代函式實作中的固定值
4. 更改呼叫程式碼
<div class='mb-6'/>

### 重構 2「以回呼取代主體實作」

1. 辨識一段程式的前段、主體與後段區塊
2. 將所有區塊包裝成函式 a
3. 將主體區塊擷取成回呼 b，並將其當成引數傳入函式 a

---

# 重構陣列的寫入時複製

重構 2 也能去除「寫入時複製」中重複的實作

1. 辨識前段、主體與後段區塊

- 「寫入時複製」三步驟對應的前段、主體與後段
  - 產生複本 ⮕ 前段
  - 修改複本 ⮕ 主體
  - 傳回複本 ⮕ 後段
- 以第六章寫入時複製函式為例

<div class='ml-6'>

```js {*}{maxHeight:'180px'}
function arraySet(array, idx, value) {
  var copy = array.slice(); // 前段
  copy[idx] = value; // 主體
  return copy; // 後段
}

function push(array, elem) {
  var copy = array.slice(); // 前段
  copy.push(elem); // 主體
  return copy; // 後段
}

function drop_last(array) {
  var array_copy = array.slice(); // 前段
  array_copy.pop(); // 主體
  return array_copy; // 後段
}

function drop_first(array) {
  var array_copy = array.slice(); // 前段
  array_copy.shift(); // 主體
  return array_copy; // 後段
}
```

</div>

---

# 重構陣列的寫入時複製

2. 將所有區塊包裝成函式

- 將前段、主體與後段包成新函式
  - 新函式最後只留陣列拷貝(前段)與傳回(後段) ⮕ 命名為 `withArrayCopy`

<div class="grid grid-cols-2 gap-x-4">

<div>

原始程式

```js
function arraySet(array, idx, value) {
  var copy = array.slice();
  copy[idx] = value;
  return copy;
}
```

</div>

<div>

建立包裝函式

```js {*|5-10|1-3}
function arraySet(array, idx, value) {
  return withArrayCopy(array); // 2. 函式內容被抽到新函式，改寫為傳回新函式結果
}

function withArrayCopy(array) {
  // 1. 三區塊包裝到新函式
  var copy = array.slice(); // 前段
  copy[idx] = value; // 主體，idx 和 value 變數尚未定義
  return copy; // 後段
}
```

</div>

</div>

---

# 重構陣列的寫入時複製

3. 將主體區塊擷取成回呼

- 將 `withArrayCopy` 主體擷取成回呼，傳入 `arraySet`
  - 此例的回呼是「修改」陣列
- `withArrayCopy` 新增 `modify` 參數，負責接收並執行回呼
  - `withArrayCopy` 執行 `modify` 回呼時，就會執行傳入的函式 `function(copy){copy[idx] = value;}`

<div class="grid grid-cols-2 gap-x-4">

<div>

目前程式

```js
function arraySet(array, idx, value) {
  return withArrayCopy(array);
}

function withArrayCopy(array) {
  var copy = array.slice();
  copy[idx] = value;
  return copy;
}
```

</div>

<div>

擷取回呼

```js {*|10-14|2-7}{maxHeight:'200px'}
function arraySet(array, idx, value) {
  return withArrayCopy(array, function (copy) {
    // 2. 將 copy[idx] = value 改寫成匿名函式，作為 withArrayCopy 的第二個引數
    copy[idx] = value;
  });
}

function withArrayCopy(array, modify) {
  // 1. 在 modify 接受傳入一個回呼函式
  var copy = array.slice();
  modify();
  return copy;
}
```

</div>

</div>

---

# 重構陣列的寫入時複製

前後改寫比較

<div class="grid grid-cols-2 gap-x-4">

<div>

#### 重構前

```js
function arraySet(array, idx, value) {
  var copy = array.slice();
  copy[idx] = value;
  return copy;
}
```

</div>

<div>

#### 重構後

```js
function arraySet(array, idx, value) {
  return withArrayCopy(array, function (copy) {
    copy[idx] = value;
  });
}

function withArrayCopy(array, modify) {
  var copy = array.slice();
  modify();
  return copy;
}
```

</div>
</div>

---

# 重構陣列的寫入時複製

### 改寫後的影響

🔺 程式碼變長

👍「寫入時複製」變成可重用的函式

👍 建立新「寫入時複製」陣列操作函式更有效率

<div class='ml-6'>
```js
// 建立新排序函式只需將操作傳入 withArrayCopy
var sortedArray = withArrayCopy(array, function(copy){
    SuperSorter.sort(copy);
})
```

</div>
👍 優化效能

<div class='ml-6'>

<p class='text-sm mb-0!'>原本執行一系列「寫入時複製」要建立多個複本，<code>withArrayCopy</code> 只會產生一個複本</p>

<div class="grid grid-cols-2 gap-x-4">

```js
// 這段程式會產生四個陣列複本
var a1 = drop_first(array);
var a2 = push(a1, 10);
var a3 = push(a2, 11);
var a4 = arraySet(a3, 0, 42);
```

```js
// withArrayCopy 只產生一個複本，對唯一複本做四次修改
var a4 = withArrayCopy(array, function (copy) {
  copy.shift();
  copy.push(10);
  copy.push(11);
  copy[0] = 42;
});
```

</div>

</div>

<style>
  .slidev-layout p{
    margin-top: 0px;
    margin-bottom: 0.5rem;
  }
</style>

---

```yaml
glow: top
glowOpacity: 0.1
```

# 讓函式傳回函式

- 錯誤記錄系統：將可能出錯的程式放入 `try`，讓 `catch` 捕捉錯誤並傳給 Snap Errors 記錄服務
  - `try/catch` 就像超級英雄服裝，穿上該服裝即獲得超能力（捕捉錯誤傳送給 Snap Errors 的能力）
    <img src='/images/log-error-cloak.png' width='90%' class='mt-6' />

---

```yaml
glow: bottom
glowOpacity: 0.1
```

# 讓函式傳回函式

- 問題：需手動將幾千行程式碼一一穿上這衣服，工程浩大
  <img src='/images/log-error-many-cloaks.png' width='80%' />

---

```yaml
glow: bottom
glowOpacity: 0.1
```

# 讓函式傳回函式

- 解法：寫一個能代替上述作業的新函式（高階函式）
  <img src='/images/log-error-cloak-high-order-function.png' width='80%' />

---

# 撰寫為程式穿上英雄服裝的高階函式

1. 觀察目前程式碼

<div class='ml-6'>

```js {*|2,8|4,10}
try {
  saveUserData(user); // 只有這行不同，其他都重複
} catch (error) {
  logToSnapErrors(error); // 🦸 此程式有超能力
}

try {
  fetchProduct(productId); // 只有這行不同，其他都重複
} catch (error) {
  logToSnapErrors(error); // 🦸 此程式有超能力
}
```

</div>

---

# 撰寫為程式穿上英雄服裝的高階函式

2. 以 `withLogging` 消除重複

<div class='ml-6'>

```js
function withLogging(f) {
  try {
    f();
  } catch (error) {
    logToSnapErrors(error);
  }
}
```

</div>

---

# 撰寫為程式穿上英雄服裝的高階函式

3. 將原本程式碼套用 `withLogging`

<div class='ml-6'>

```js {*|2,6}
withLogging(function () {
  saveUserData(user); // 🦸 此程式有超能力
});

withLogging(function () {
  fetchProduct(productId); // 🦸 此程式有超能力
});
```

- 問題
  - 套用 `withLogging` 後仍有重複存在
  - 需手動將 `withLogging` 加到程式內所有需要的地方
  - 可能忘記登錄錯誤（忘記將某函式傳入 `withLogging()`）

</div>

---

# 撰寫為程式穿上英雄服裝的高階函式

3. 將原本程式碼套用 `withLogging`

<div class='ml-6'>

```js
withLogging(function () {
  saveUserData(user); // 🦸 此程式有超能力
});

withLogging(function () {
  fetchProduct(productId); // 🦸 此程式有超能力
});
```

- 解法：建立 `wrapLogging` 新函式
  - 以無超能力（不會記錄錯誤）的函式為引數，傳回該函式的超能力（會記錄錯誤）版本
  - 只需在一開始將所有一般函式轉為超能力函式即可

</div>

---

# 撰寫為程式穿上英雄服裝的高階函式

4. 重新檢視原始程式

<div class='ml-6'>

- 這兩段程式既執行一般功能、又有超能力（紀錄錯誤）

```js
try {
  saveUserData(user);
} catch (error) {
  logToSnapErrors(error);
}

try {
  fetchProduct(productId);
} catch (error) {
  logToSnapErrors(error);
}
```

</div>

---

# 撰寫為程式穿上英雄服裝的高階函式

5. 將 `try` 區塊的一般程式改名，反映「不主動記錄錯誤」的事實

<div class='ml-6'>

```js {*|2,8}
try {
  saveUserDataNoLogging(user);
} catch (error) {
  logToSnapErrors(error);
}

try {
  fetchProductNoLogging(productId);
} catch (error) {
  logToSnapErrors(error);
}
```

</div>

---

# 撰寫為程式穿上英雄服裝的高階函式

6. 建立「會記錄錯誤」的函式

<div class='ml-6'>

```js
function saveUserDataWithLogging(user) {
  try {
    saveUserDataNoLogging(user);
  } catch (error) {
    logToSnapErrors(error);
  }
}

function fetchProductWithLogging(productId) {
  try {
    fetchProductNoLogging(productId);
  } catch (error) {
    logToSnapErrors(error);
  }
}
```

✅ 呼叫這兩個函式就能確保錯誤被記錄 <span class='opacity-80 text-sm'>先準備好「超能力」函式，就不用在上千處呼叫時都包 <code>try/catch</code> </span>

🔺 主體實作中太多重複

</div>

---

# 撰寫為程式穿上英雄服裝的高階函式

7. 將實作改寫為匿名函式，將參數名稱普適化

<div class='ml-6'>

```js
function(arg) { // 前段
  try {  // 前段
    saveUserDataNoLogging(arg); // 主體
  } catch (error) { // 後段
    logToSnapErrors(error); // 後段
  } // 後段
} // 後段

function(arg) {  // 前段
  try {  // 前段
    fetchProductNoLogging(arg); // 主體
  } catch (error) { // 後段
    logToSnapErrors(error);  // 後段
  }  // 後段
}  // 後段
```

</div>

---

# 撰寫為程式穿上英雄服裝的高階函式

8. 實作重構 2 以回呼取代主體實作

<div class='ml-6'>

<p class='my-0!'>將 <code>try/catch</code> 包到新函式，將該區塊函式作為匿名函式回傳</p>

<div class="grid grid-cols-2 gap-x-4">

```js
// 將以下匿名函式作為 wrapLogging 的傳回值
function(arg) {
  try {
    saveUserDataNoLogging(arg);
  } catch (error) {
    logToSnapErrors(error);
  }
}
```

<div>

```js
// wrapLogging 可接收一個函式引數
function wrapLogging(f) {
  // wrapLogging 的傳回值是函式
  return function (arg) {
    try {
      // 能執行不同程式的 try/catch 被包裝在匿名函式中
      f(arg);
    } catch (error) {
      logToSnapErrors(error);
    }
  };
}

// 將 wrapLogging 的傳回函式指定給變數，藉此賦予該函式名稱
var saveUserDataWithLogging = wrapLogging(saveUserDataNoLogging);
```

<p class='my-0! opacity-80 text-sm'> <code>wrapLogging</code>：接受一個函式 <code>f</code>，將 <code>f</code> 包裝到 <code>try/catch</code> 中，再以函式形式傳回 </p>
</div>

</div>

</div>

---

# 撰寫為程式穿上英雄服裝的高階函式

9. 可用 `wrapLogging` 將任何一般函式(無記錄功能)轉為超能力函式(會記錄錯誤)

<div class='ml-6'>

```js
var saveUserDataWithLogging = wrapLogging(saveUserDataNoLogging);
var fetchProductWithLogging = wrapLogging(fetchProductNoLogging);
```

- `wrapLogging` 優點
  - 輕易將登錄錯誤的行為附加到函式
  - 消除重複程式(不必反覆打類似的 `try/catch`)

</div>

---

# 撰寫為程式穿上英雄服裝的高階函式

前後改寫比較

<div class="grid grid-cols-2 gap-x-4">

<div>

#### 手動加上「超能力」

```js
// 😵‍💫 若有一千行程式需記錄錯誤，就要寫一千次 try/catch
try {
  saveUserData(user);
} catch (error) {
  logToSnapErrors(error);
}
```

</div>

<div>

#### 自動加上「超能力」

```js
saveUserDataWithLogging(user);

// saveUserDataWithLogging 來自於能給予任意函式超能力的 wrapLogging
function wrapLogging(f) {
  return function (arg) {
    try {
      f(arg);
    } catch (error) {
      logToSnapErrors(error);
    }
  };
}
```

</div>
</div>

---

```yaml
glow: right
glowOpacity: 0.1
```

# 撰寫為程式穿上英雄服裝的高階函式

`wrapLogging` 將 `saveUserDataNoLogging` 轉為 `saveUserDataWithLogging` 示意圖

<img src='/images/wrapLogging-high-order-function.png' width='90%' />

---

# 讓函式傳回函式可以…

<br />
✅ 建立自動化「函式生成工廠」

✅ 將特定程序標準化

---

# 補充

#### 如何讓 `wrapLogging` 傳回的新函式接收更多引數? 如何讓新函式傳回值?

- 如何讓新函式傳回值?
  - 將 `try` 區塊內的 `f(arg)` 改為 `return f(arg)`
- 如何讓 `wrapLogging` 傳回的新函式接收更多引數?
  - 可用 ES6 JavaScript 的 rest arguments 與 spread operator

<div class='ml-12'>

```js {*}{maxHeight:'220px'}
function wrapLogging(f) {
  return function (...args) {
    // 使用 rest parameters 接收任意數量參數
    try {
      return f(...args); // 使用 spread operator 展開參數
    } catch (error) {
      logToSnapErrors(error);
    }
  };
}

// 以 apply 實作，可保持 this 的上下文
function wrapLogging(f) {
  return function (...args) {
    try {
      return f.apply(this, args); // 使用 apply 傳遞參數
    } catch (error) {
      logToSnapErrors(error);
    }
  };
}
```

</div>

---

# 補充

#### 能傳回新函式的高階函式可做到很多事，有可能用高階函式撰寫整個程式嗎?

只用高階函式撰寫程式，有可能做到 👌

---

# 補充

#### 只用高階函式撰寫程式，有好處嗎? 高階函式的取捨

- 高階函式優點
  - 消除重複
  - 處理常見模式(如迴圈、錯誤捕捉)
- 高階函式缺點
  - 過度使用會降低可讀性
- 使用高階函式解決問題時，應評估
  - 高階函式是否比較好?
  - 高階函式是否提升可讀性?
  - 高階函式減少多少重複？
  - 別人是否看懂高階函式在做什麼?


---

```yaml
layout: center
```

# 章節回顧

1. 讓 `try` 和 `catch` 區塊都能執行不同程式碼，實作 `tryCatch`

```js
// 將以下
try {
  sendEmail();
} catch (error) {
  logToSnapErrors(error);
}

// 改成以下
tryCatch(sendEmail, logToSnapErrors);
```

<div v-click='1' class='opacity-75 mt-6 text-sm'>

```js
function tryCatch(f, errorHandler) {
  try {
    return f();
  } catch (error) {
    return errorHandler(error);
  }
}
```

</div>

---

```yaml
layout: center
```

# 章節回顧

2. 以重構 2 將 `if` 敘述包裝成函式，實作 `when(test, then)`

```js
// 將以下
if (array.length === 0) {
  // test 語句
  console.log("Array is empty"); // then 語句
}

// 改成以下
when(
  array.length === 0, // test 語句
  function () {
    console.log("Array is empty"); // then 語句
  },
);
```

<div v-click='1' class='opacity-75 mt-6 text-sm'>

```js
function when(test, then) {
  if (test) return then();
}
```

</div>

---

```yaml
layout: center
```

# 章節回顧

3. 請觀察以下程式碼，使用「以回呼取代主體實作」的重構方式，將重複的程式碼改寫成高階函式：

```js
function addAndLog(a, b) {
  const result = a + b;
  console.log("計算結果:", result);
  return result;
}

function multiplyAndLog(a, b) {
  const result = a * b;
  console.log("計算結果:", result);
  return result;
}
```

---

```yaml
layout: center
```

# 章節回顧

3. 請觀察以下程式碼，使用「以回呼取代主體實作」的重構方式，將重複的程式碼改寫成高階函式：

<div class='opacity-75 text-sm'>

```js
function withLogging(operation) {
  return function (a, b) {
    const result = operation(a, b);
    console.log("計算結果:", result);
    return result;
  };
}

const addAndLog = withLogging((a, b) => a + b);
const multiplyAndLog = withLogging((a, b) => a * b);

console.log(addAndLog(2, 3)); // 計算結果: 5
console.log(multiplyAndLog(2, 3)); // 計算結果: 6
```

</div>

---

```yaml
layout: center
```

# 章節回顧

4. 撰寫 `makeAdder()` 高階函式，傳回可將兩數相加的新函式

```js
// 使用方式
var increment = makeAdder(1);
increment(10); // 11

var plus10 = makeAdder(10);
plus10(12); // 22
```

<div v-click='1' class='opacity-75 mt-6 text-sm'>

```js
function makeAdder(n) {
  return function (x) {
    return n + x;
  };
}
```

</div>

---

```yaml
layout: center
```

# 章節回顧

5. 請解釋什麼是「函式生成工廠」(function factory)，並用一個簡單的例子說明。

<div v-click='1' class='opacity-75 mt-6 text-sm'>
函式生成工廠是指能產生新函式的函式。例如：

```js
function makeGreeter(greeting) {
  return function (name) {
    return greeting + ", " + name + "!";
  };
}

const sayHello = makeGreeter("Hello");
const sayHi = makeGreeter("Hi");

console.log(sayHello("John")); // "Hello, John!"
console.log(sayHi("John")); // "Hi, John!"
```

`makeGreeter` 就是一個函式生成工廠，它接收一個問候語，回傳一個新的問候函式。

</div>

---

```yaml
layout: center
```

# 下回預告：Ch12-13

## Ch12 利用函式走訪

## Ch13 串連函數式工具

- 日期：6/12
- 導讀人：Mi
- 筆記工：Sam

---

```yaml
layout: center
glowSeed: 10
```

# Thanks for Listening!
