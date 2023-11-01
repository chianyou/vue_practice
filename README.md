#  vue框架簡介

- 基於html、css、javascript。
- 模板、回應式、組件設計。
- 漸進式的框架、適應各種開發環境。
  - 強化既有的傳統網頁應用
  -  開發完整的單頁式網站應用
  -  支援後續的伺服器渲染
# 模板語法
Vue 使用一种基于 HTML 的模板语法，使我们能够声明式地将其组件实例的数据绑定到呈现的 DOM 上
### 1.插入html
雙大括號為純文字，想插入html要使用`v-html`
```vue-html
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```
### 2.Attribute(屬性) 綁定
要使用`v-bind`指令，並解有簡寫語法`:`
```vue-html
<div :id="dynamicId"></div>
```
### 3.布林值Attribute
依據true/flase來決定attribute是否出現在該元素上(這邊使用disabled為例)
在這種情況`v-bind`使用上比較特別
```vue-html
//用v-bind綁定disabled，用isButtonDisable這個屬性來辨別按鈕有沒有被禁用
<button :disabled="isButtonDisabled">Button</button>
```
### 4.綁定多個值
可以用不帶參數的`v-bind`來綁定多個attribute的javascript對象
<div class="composition-api">

```js
const objectOfAttrs = {
  name: 'chianyou',
  school: 'soochow'
}
```

</div>

```vue-html
//直接用v-bind綁定objectOfAttrs
<div v-bind="objectOfAttrs"></div>
```

### 5.Javascript表達式
如果想在Vue的模板內加入Javascript的方式
- 文本(雙大括號)
- 以`v-`開頭的attribute值
```vue-html
//用雙括號插入Javascript的範例

//數字加一
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}
//先將文本分割然後反轉最後重組
{{ message.split('').reverse().join('') }}
//如果id的值是123會變成list-123
<div :id="`list-${id}`"></div>
```

### 6.指令Dirctives
指令就是前面有`v-` 的attribute，vue也有很多內建指令
(例如`v-if` 、`v-on`、`v-slot`)

- **參數Argument**(在指令後會加冒號做分隔)
有些指令後面會需要一個參數
```vue-html
//這裡的href就是一個參數
//v-bind將url綁在href這個屬性上
<a v-bind:href="url"> ... </a>

<!-- 簡寫 -->
<a :href="url"> ... </a>
```
- **動態參數**
參數也可以是Javascript但要包在一個括號內

```vue-html
//attributeNaame為Javascript的動態屬性 url是該屬性的值
<a v-bind:[attributeName]="url"> ... </a>

<!-- 簡寫 -->
<a :[attributeName]="url"> ... </a>
```
- **修飾符Modifiers**
以`.`開頭 表示要用特殊方式綁定
例如`.prevent`會告訴`v-on`(簡稱@)去觸發`event.preventDefault()`
```vue-html
//@submit用於監聽表單提交事件 .prevent阻止默認的提交行為
<form @submit.prevent="onSubmit">...</form>
```
# 響應式 
 **響應是指的是數據改變後Vue 會通知到使用該數據的代碼**
### 1.`ref`、`reactive()`
-  **`ref`**
```vue-html
//導入ref函數
import { ref } from 'vue'
//創建一個count的響應式對象，初始值設為0
const count = ref(0)
```
`ref()` 接收參數，並將其包裹在一個帶有 `.value `屬性的 ref 對象(也就是`count`)中返回
```vue-html
const count = ref(0)

// 使用 console.log() 列印 count 變量
console.log(count) // { value: 0 }

// 使用 .value 屬性來訪問 count 的值
console.log(count.value) // 0

// 增加count的值
count.value++
console.log(count.value) // 1
```
- **`reactive()`**
會使對象本身有響應性
```vue-html
//導入reactive函數
import { reactive } from 'vue'

//使用reactive創建一個state的對象，並包含一個count屬性
初始值為0
const state = reactive({ count: 0 })
```
`reactive()`創建的對象自身就有響應性所以不需使用`.value`去訪問、可以直接用`state.count`

- `reactive()`缺點
  - 有限的值類型(Map、Set 这样的集合类型)
  - 不能替換整個對象
  替換整個對象，原始對像和新對像之間綁定關係會丟失
  - 對結構操作不友好

- **比較兩者差異**
  - `ref`接受所有型態資料，但物件(object)或陣列(array)內部屬性改變時不會做監聽
  - `reactive()`只接受物件或陣列的資料型態，內部屬性變動時會做深層監聽，取資料時也不需要`.value`

### 2.`<script setup>`
可以用`<script setup>`單文件組件(SFC)來簡化` setup()`函數的代碼

# 計算屬性
使用計算屬性去描述複雜的邏輯

```vue-html
<script setup>
//導入reactive跟computes函數
import { reactive, computed } from 'vue'

//創建一個叫chianyou的響應式對象，包含一個name屬性跟一個butlist屬性
const chianyou = reactive({
  name: 'chianyou',
  buylist: [
    'clothes',
    'food'
  ]
})

// 建立了一個計算buy屬性
const buy = computed(() => {
  return chianyou.buylist.length > 0 ? 'Yes' : 'No'
})
</script>

<template>
  <p>Has shopping:</p>
  <span>{{ buy}}</span>
</template>
```
![](https://hackmd.io/_uploads/Hk0vg58M6.png)






# Class與Style綁定
class跟style都是attribute可以用`v-bind`去做綁定，但若內容比較複雜時容易出錯，所以vue有專門為class、style的`v-bind`綁定做其他處理
### 1.綁定HTML class
給 `:class` (`v-bind:class`的簡寫)傳遞一个對象來動態切換 class：
```vue-html
//這裡是根據isActive的true/flase值去判斷active是否存在
<div :class="{ active: isActive }"></div>
```
`:class` 指令也可以和一般的 `class` attribute 共存
<div class="composition-api">

```js
//創建兩個響應式變量isActive、hasError並設定初始值(true/flase)
const isActive = ref(true)
const hasError = ref(false)
```

套入模板
```vue-html
<div
  class="static"
//如果isActive的值為true，active會應用到該元素上
//如果hasError的值為true，text-danger會應用到該元素上(將文本變成紅色)
  :class="{ active: isActive, 'text-danger': hasError }"
></div>
```
所以渲染結果
```vue-html
//isActive的初始值為true所以一開始就會應用
<div class="static active"></div>
```

### 2.綁定數組
我們可以給 :class 綁定一个數组来渲染多個 CSS class
<div class="composition-api">

```js 
//創建兩個變量activeclass、errorClass，分別包含字串值active、text-danger
const activeClass = ref('active')
const errorClass = ref('text-danger')
```

</div>

```vue-html
//使用:class綁定將這兩個包在一個數組中
<div :class="[activeClass, errorClass]"></div>
```

渲染的结果是：

```vue-html
<div class="active text-danger"></div>
```
### 3.綁定內連樣式
`:style` 支持綁定 JavaScript 的對象值，對應的是 HTML 元素的 `style` 屬性：

<div class="composition-api">

```js 
//創建兩個變量activeColor、fontsize並將它分別設定為red、30
const activeColor = ref('red')
const fontSize = ref(30)
```

</div>

```vue-html
//用:style動態設置元素的樣式
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
也可以直接綁定
<div class="composition-api">

```js
//用reactive創建styleObject裡面有兩個屬性color、fontsize
const styleObject = reactive({
  color: 'red',
  fontSize: '13px'
})
```

</div>


```vue-html
<div :style="styleObject"></div>
```
# 條件渲染
滿足某種條件去渲染某一段內容
### 1.`v-if`、 `v-else`、`v-else-if`
`v-if` - 條件性地渲染一塊内容
`v-else` - else 區塊
`v-else-if` - 必須跟在一個`v-if`或`v-else-if` 後面
```vue-html
<script setup>
import { ref } from 'vue'

const awesome = ref(true)
</script>

<template>
//點擊按鈕去觸發awesome變量true/flase值的切換
  <button @click="awesome = !awesome">Toggle</button>

	<h1 v-if="awesome">Vue is awesome!</h1>
	<h1 v-else>Oh no 😢</h1>
</template>

```

### 2.`<template>`上的 `v-if`
切換一個以上的元素時使用
    
### 3.`v-show`
用法一樣但不支持在 `<template>`元素上使用，也不能和 `v-else` 搭配使用
    
### 4.`v-for`
- 若和`v-if`同時出現 則`v-if`優先
- `v-for` 指令的值需要使用 `item in items` 形式
- 使用 v-for 來歷遍每一个對象的所有數性。順序會基於對該對象調用 Object.keys() 的返回值來決定
![](https://hackmd.io/_uploads/r1B8W58zp.png)

# 事件處理
    
### 1.監聽事件
用`v-on`(簡寫`@`)來監聽DOM事件
方法`v-on:click="handler"`
事件處理器(handler)可以是
- 內連事件處理器
- 方法事件處理器
### 2.內連事件處理器
![](https://hackmd.io/_uploads/S1eVX9LfT.png)
用`@click`去監聽點擊事件
當按鈕被點擊counter變量會加一(或減一)
### 3.方法事件處理器
![](https://hackmd.io/_uploads/B1CqQ5IM6.png)
用`@click`監聽按鈕的點擊事件並觸發greet函數
### 4.在內連處理器中調用方法
```vue-html
<script setup>
//設定一個say函數會跳出訊息
function say(message) {
  alert(message)
}
</script>

<template>
    //監聽按鈕點擊事件輸入message(hello)
	<button @click="say('hello')">Say hello</button>
	<button @click="say('bye')">Say bye</button>
</template>

```

# 表單輸入綁定
`v-model`可以簡化連接值綁定或更改事件監聽器
### 1.文本
```vue-html
<script setup>
import { ref } from 'vue'
//新建三個變量
const picked = ref('One')
const message = ref('')
const selected = ref('')
//用來修改message的值
const changeMessage = (newMessage) => {
  message.value = newMessage
}
</script>

<template>
  <div>
    //下拉選單
    <input v-model="message" placeholder="名字" />
    <span>學校: </span>
    <select v-model="selected">
      <option disabled value="">Please select one</option>
      <option>東吳</option>
      <option>輔大</option>
      <option>文化</option>
    </select>
  </div>
    
  <div>
    //二擇一
    <div>性別: </div>
    <input type="radio" id="one" value="男" v-model="picked" />
    <label for="one">男</label>
    <input type="radio" id="two" value="女" v-model="picked" />
    <label for="two">女</label>
  </div>
  <p>我的名字{{message}} 性別為{{picked}} 就讀{{selected}}大學</p>
</template>



```
![](https://hackmd.io/_uploads/Hk45v9LM6.png)
    
# 偵聽器
有時需要在狀態改變時觸發其他功能
`watch`函數可以讓開發者在資料改變時執行特定的函式 

### 1.`watch()`
```
watch(被監聽變數, (新值, 舊值) => {
  要做的事...
});
```

![](https://hackmd.io/_uploads/H1KttqUG6.png)
```vue-html
<script setup>
//導入ref,watch
import { ref, watch } from 'vue'
    
//創建question儲存輸入的問題
const question = ref('')

//創建answer儲存問題的回答
const answer = ref('Questions usually contain a question mark. ;-)')

//使用watch來監視question
//當question產生變化時會開始判斷問題中是否含有問號
//有問號answer會顯示thinking然後去網站獲取回答
watch(question, async (newQuestion) => {
  if (newQuestion.indexOf('?') > -1) {
    answer.value = 'Thinking...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Error! Could not reach the API. ' + error
    }
  }
})
</script>

<template>
  <p>
    Ask a yes/no question:
    <input v-model="question" />
  </p>
  <p>{{ answer }}</p>
</template>
```
### 2.及時回調偵聽器

一般來說`watch`是在數據改變時才進行回調，但可以透過`immediate: true`在創建偵聽器時立即執行回調。
    
### 3.`watchEffect()`偵聽
只要在{}中直接使用參數值，就會自動監聽。
```vue-html
watchEffect(() => {
  console.log(num.value);
});
```
    
**原本 :**
    
```jsonl
//創建todoId值設為1
const todoId = ref(1)
const data = ref(null)
//監聽todoId
watch(todoId, async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
 //取得值後轉成json格式儲存在data中
  data.value = await response.json()
}, { immediate: true })
```
在這段程式碼中todoId被使用了兩次，一次是用在創建一次是用在watch監聽。
    
**用`watchEffect()`改寫後**
```vue-html
watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```
不再需要`immediate: true`會自己立即執行，也不再需要`todoId`作為源值。

總而言之`watchEffect()`只會回調被使用到的屬性。
    
### 4.停止偵聽
要手動停止一个偵聽器，用 `watch`或 `watchEffect` 返回的函数

```vue-html
const unwatch = watchEffect(() => {})

// ...當該真聽器不再需要時
unwatch()

```
# 模板引用
如果要直接訪問DOM會使用到屬性`ref`
### 1.`ref`
```vue-html
<script setup>
//導入ref,onMounted函數
import { ref, onMounted } from 'vue'

//創建一個imput響應式變量
const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="input" />
</template>
```
### 2.`v-for`
```vue-html
<script setup>
import { ref, onMounted } from 'vue'
//v-for對應到的ref是一個數組
const list = ref([
  /* ... */
])

const itemRefs = ref([])

onMounted(() => console.log(itemRefs.value))
</script>

<template>
  <ul>
    <li v-for="item in list" ref="itemRefs">
      {{ item }}
    </li>
  </ul>
</template>
//ref 數組并不保證與源數組相同的順序
```
# 組件基礎
將UI劃分成獨立可重複使用的部分，並且每個部份可以獨自運作
### 1.定義組件
定義在單獨`.vue`文件中稱作**單文件組件**(SFC)
```vue-html
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">You clicked me {{ count }} times.</button>
</template>
```  
### 2.使用組件
如果要使用一個組件，要先將他導入父組件
**範例 :**
先創建一個可以計數的ButtonCounter.vue文件。
```vue-html
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">
    You clicked me {{ count }} times.
  </button>
</template>
```
把ButtonCounter放進ButtonCounter.vue文件中，並且組件可以重複用很多次。
```vue-html
<script setup>
import ButtonCounter from './ButtonCounter.vue'
</script>

<template>
	<h1>Here are many child components!</h1>
	<ButtonCounter />
	<ButtonCounter />
	<ButtonCounter />
</template>
```
畫面:
![](https://hackmd.io/_uploads/BJgwLWCzp.png)
    
### 3.傳遞props
要用到`defineProps`
以Blog的文章title為範例
```vue-html
<!-- BlogPost.vue -->
<script setup>
//使用defineProps函數定義一個叫title的props
defineProps(['title'])
</script>

<template>
  <h4>{{ title }}</h4>
</template>

```

```vue-html
<script setup>
import { ref } from 'vue'
//導入BlogPost的子組件
import BlogPost from './BlogPost.vue'
  
const posts = ref([
  { id: 1, title: 'My journey with Vue' },
  { id: 2, title: 'Blogging with Vue' },
  { id: 3, title: 'Why Vue is so fun' }
])
</script>
//將posts裡的數組文章列表用BlogPost顯示
<template>
	<BlogPost
  	v-for="post in posts"
	  :key="post.id"
  	:title="post.title"
	></BlogPost>
</template>
```
![](https://hackmd.io/_uploads/By86O-Rzp.png)

### 4.監聽事件

將blog文章文字放大，會用到`postFontSize`。
```vue-html
<script setup>
defineProps(['title'])
//可以觸發一個自訂議事件enlarge-text
defineEmits(['enlarge-text'])
</script>

<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button @click="$emit('enlarge-text')">Enlarge text</button>
  </div>
</template>
```
```vue-html
<script setup>
import { ref } from 'vue'
import BlogPost from './BlogPost.vue'
  
const posts = ref([
  { id: 1, title: 'My journey with Vue' },
  { id: 2, title: 'Blogging with Vue' },
  { id: 3, title: 'Why Vue is so fun' }
])
//設定原本字體大小為1
const postFontSize = ref(1)
</script>

<template>
	<div :style="{ fontSize: postFontSize + 'em' }">
    <BlogPost
      v-for="post in posts"
      :key="post.id"
      :title="post.title"
      @enlarge-text="postFontSize += 0.1"
    ></BlogPost>
  </div>
</template>

```
呈現畫面:
![](https://hackmd.io/_uploads/HyCSiWCGT.png)



    
# 組件註冊

Vue 組件在使用前需要先被“註冊”，這樣 Vue 才能在渲染模板時找到其對應的實現
### 1.全局註冊
用`app.component()`讓組件在當前的vue中全局都可以使用
```vue-html
import { createApp } from 'vue'

const app = createApp({})

app.component(
  // 註冊名字
  'MyComponent',
  // 組件內容
  {
    /* ... */
  }
)
```
- 全局註冊的缺點
  - 全局註冊，但並沒有被使用的組件無法在生產打包時被自動移除 (也叫“tree-shaking”) 
  - 大型項目中使項目的依賴關係變得不那麼明確

### 2.局部註冊
    
優點是使組件之間的依賴關係更加明確，并且對 tree-shaking 更加友好

導入的組件直接在模板中使用無須註冊
```vue-html
<script setup>
import ComponentA from './ComponentA.vue'
</script>

<template>
  <ComponentA />
</template>
```

# Props
代表屬性 可以在組件上註冊 已將數據從父組件傳遞到其子組件之一
### 1.Props聲明
聲明它所接受的 props，這樣 Vue 才能知道外部傳入的哪些是 props 
- 使用` <script setup> `的单文件组件中可以使用 `defineProps()`來聲明
```vue-html
<script setup>
//使用defineProps來聲明、訪問foo屬性
const props = defineProps(['foo'])
    
//用console.log來列印出props.foo的值
console.log(props.foo)
</script>
```
- 沒有使用 `<script setup>`的組件中，prop 可以使用 props 來聲明
```vue-html
export default {
  props: ['foo'],
  setup(props) {
    // setup() 接收 props 作為第一個參數
    console.log(props.foo)
  }
}

```
### 2.靜態/動態 prop
靜態值形式的props:
```vue-html
<BlogPost title="My journey with Vue" />
```
使用 `v-bind `或縮寫 `:` 來進行動態綁定的 props
```vue-html
<BlogPost :title="post.title" />

<BlogPost :title="post.title + ' by ' + post.author.name" />
```
### 3.Boolean類型轉換
聲明為Boolean類型的props在轉換上規則比較不一樣。
**範例:**
```vue-html
defineProps({
  disabled: Boolean
})
```
可以這樣使用
```vue-html
<!-- 等於 :disabled="true" -->
<MyComponent disabled />

<!-- 等於 :disabled="false" -->
<MyComponent />

```
# 組件事件

### 1.觸發與監聽事件
在組件模板中可以直接使用 `$emit `方法觸發自訂事件
```vue-html
<!-- MyComponent -->
<button @click="$emit('someEvent')">click me</button>
```
父組件可以通過 `v-on` (縮寫為 @) 來監聽事件
```vue-html
<MyComponent @some-event="callback" />
```
**事件參數**
有時候在觸發時會需要設定一個初始值，可以給`$emit`設定一個參數。
```vue-html
<button @click="$emit('increaseBy', 1)">
  Increase by 1
</button>
```
### 2.聲明觸發的事件
用`defineEmits()`來聲明觸發的事件
```vue-html
<script setup>
//用defineEmits聲明兩個自定義事件
defineEmits(['inFocus', 'submit'])
</script>
```
在 <template> 中使用的 `$emit` 方法不能在組件的 `<script setup>` 部分中使用，但 `defineEmits()` 會反回一個相同作用的函數
```vue-html
<script setup>
//聲明兩個自定義事件inFocus、submit並儲存在emit中
const emit = defineEmits(['inFocus', 'submit'])

//用emit觸發submit
function buttonClick() {
  emit('submit')
}
</script>
```
# 組件`v-model`
`v-model`可以雙向綁定。
### 1.`v-model`的參數
通常`v-model`在組件上都是使用 `modelValue` 作為 prop，並以 `update:modelValue` 作為對應的事件，可以透過給v-model指定參數來更改名字。
```vue-html
<MyComponent v-model:title="bookTitle" />
```
子組件聲明一個 `title`，並觸發 `update:title` 事件更新父組件值。
```vue-html
 <!-- MyComponent.vue -->
<script setup>
//從父組件接收一個title屬性
defineProps(['title'])
defineEmits(['update:title'])
</script>

<template>
  <input
    type="text"
    :value="title"
    //將更新的title值傳遞到父組件
    @input="$emit('update:title', $event.target.value)"
  />
</template>  

```
![](https://hackmd.io/_uploads/r1o9BzAGa.png)

    
# `Attributes`
### 1.`Attributes`繼承
傳遞到一個組件，卻沒被聲明為 props 或 emits 的 attribute 或 v-on 事件監聽器。例子: class、style 和 id。

假設有一個<MyButton>組件
```vue-html
<!-- <MyButton> 的模板 -->
<button>click me</button>
```
一個父組件傳入class
```vue-html
<MyButton class="large" />
```
最後的結果
```vue-html
<button class="large">click me</button>
```
<MyButton> 沒有把 class聲明為一個 prop，所以 class 被視作傳透 attribute，自動傳到 <MyButton> 的根元素上。
### 2.禁用 `Attributes` 繼承
不希望自動繼承attributes可以適用`inheritAttrs: false`。
```vue-html
<script setup>
defineOptions({
  inheritAttrs: false
})
// ...setup 
</script>
```
# 插槽
### 1.插槽內容與出口
想要為子组件傳遞一些模板片段，讓子组件在它們的組件中渲染这些片段。
`<slot>`元素是一个插槽出口 (slot outlet)，標示父元素提供的插槽内容 (slot content) 在哪里被渲染。
    
**以fancybutton為例**
```fancybutton.vue
<!-- Fancybutton.vue -->
<template>
  <button class="fancy-btn">
  	<slot/> <!-- slot outlet -->
	</button>
</template>

<style>
.fancy-btn {
  color: #fff;
  background: linear-gradient(315deg, #42d392 25%, #647eff);
  border: none;
  padding: 5px 10px;
  margin: 5px;
  border-radius: 8px;
  cursor: pointer;
}
</style>
```
![](https://hackmd.io/_uploads/BkzkNjLzp.png)

### 2.默認內容
在外部沒有提供任何内容的情况下，可以為插槽指定默認内容。
以`<SubmitButton>`組件為例
在父組件沒有提供任何插槽内容時在 <button> 内渲染“Submit”，只需要將“Submit”寫在 <slot> 標籤之間
```vue-html
<button type="submit">
  <slot>
    Submit <!-- 默认内容 -->
  </slot>
</button>
```
所以如果沒有給`<SubmitButton>`插槽內容時
```vue-html
<SubmitButton />
```
submit將會被自動採用成默认内容
```vue-html
<button type="submit">Submit</button>
```
如果提供插槽內容則會取代默認內容
    




# 依賴注入
正常情況需要從父組件傳遞到仔組件 但若組件樹非常巨大將會很麻煩
而`provide` 和 `inject`可以解決這個問題
- `provide`
```vue-html
<script setup>
import { provide } from 'vue'

provide(/* 注入名 */ 'message', /* 值 */ 'hello!')
</script>
```
如果不是使用`<script setup>` ，`provide()`要跟`setup()`同步調用
```vue-html
import { provide } from 'vue'

export default {
  setup() {
    provide(/* 注入名 */ 'message', /* 值 */ 'hello!')
  }
}
```
- `Inject `
```vue-html
<script setup>
import { inject } from 'vue'

const message = inject('message')
</script>
```
同樣的如果沒有使用`<script setup>`，`inject()`要跟`setup()`同步調用。
```vue-html
import { inject } from 'vue'

export default {
  setup() {
    const message = inject('message')
    return { message }
  }
}
```
# 異部組件
### 1.基本用法
在大型項目中，我們可能需要拆分應用為更小的部分，只有在需要才從伺服器下載需要的組件
而`defineAsyncComponent`可以完成這個需求
```vue-html
import { defineAsyncComponent } from 'vue'

//用defineAsyncComponent創建一個異部組件AsyncComp
const AsyncComp = defineAsyncComponent(() => {
    //返回Promise
  return new Promise((resolve, reject) => {
    // ...從服務器獲取組件
    resolve(/* 獲取到的組件 */)
  })
})
// ... 向使用其他組件一樣去使用 `AsyncComp`
```
### 2.加載與錯誤狀態
`defineAsyncComponent()` 也支持在進階選項中處理這些狀態。
```vue-html
const AsyncComp = defineAsyncComponent({
  // loader函數用於加載異部組件
  loader: () => import('./Foo.vue'),

  // 加載異部組件時顯示的组件
  loadingComponent: LoadingComponent,
  // 展示加載組件前的延遲時間，默認為 200ms
  delay: 200,

  // 加仔失敗後展示的組件
  errorComponent: ErrorComponent,
  // 如果提供了一个 timeout 時間限制，並超時了
  // 也會顯示這裡配置的報錯組件，默認值是：Infinity
  timeout: 3000
})
```











