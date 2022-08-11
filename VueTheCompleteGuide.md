## Section 1: Getting Started

### Without Build Tools

```js
<script src="https://unpkg.com/vue@3"></script>
```

### imperative approach

```js
const buttonEl = document.querySelector('button');
const inputEl = document.querySelector('input');
const listEl = document.querySelector('ul');

function addGoal() {
  const enteredValue = inputEl.value;
  const listItemEl = document.createElement('li');
  listItemEl.textContent = enteredValue;
  listEl.appendChild(listItemEl);
  inputEl.value = '';
}

buttonEl.addEventListener('click', addGoal);
```

### Refactor 'index.html'

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>A First App</title>
    <link rel="stylesheet" href="styles.css" />
  </head>
  <body>
    <div id="app">
      <div>
        <label for="goal">Goal</label>
        <input type="text" id="goal" v-model="enteredValue" />
        <button v-on:click="addGoal">Add Goal</button>
      </div>
      <ul>
        <li v-for="goal in goals">{{goal}}</li>
      </ul>
    </div>
    <script src="https://unpkg.com/vue@3"></script>
    <script src="app.js"></script>
  </body>
</html>
```

### Refactor 'app.js'

```js
Vue.createApp({
  data() {
    return {
      goals: [],
      enteredValue: '',
    };
  },
  methods: {
    addGoal() {
      this.goals.push(this.enteredValue);
      this.enteredValue = '';
    },
  },
}).mount('#app');
```

## Section 2: Basics & Core Concepts

### 使用方法：

1. 使用 html 中的 id，来 bind vue app 的 mount

```html
<section id="user-goal"></section>
```

```js
app.mount('#user-goal');
```

2. 在 html 中被 bind 的 child 里使用双括号来引用 vue data 中的变量

```html
<section id="user-goal">
  <h2>My Course Goal</h2>
  <p>{{courseGoal}}</p>
</section>
```

```js
const app = Vue.createApp({
  data() {
    return {
      courseGoal: 'finish this course!',
      vueLink: 'https://vuejs.org/',
    };
  },
});
```

注意：

- {{}}仅能在 html 标签之间使用；如果要在标签内使用，需要使用 v-bind，例如：

```html
<p>Learn more <a v-bind:href="vueLink">about Vue</a></p>
>
```

- {{}} 中间可以写基本的 JS 表达式，比如 1+1，Math.random(),但不能写复杂的表达式，比如 if else

3. 在 html 中被 bind 的 child 里使用双括号来引用 vue methods 中的函数，例如

```html
<p>{{outputGoal()}}</p>
```

```js
  data() {
    return {
      courseGoalA: 'finish this course!',
      courseGoalB: 'master Vue!',
      vueLink: 'https://vuejs.org/',
    };
  },
 methods: {
    outputGoal() {
      const randomNumber = Math.random();
      const result = randomNumber < 0.5 ? 'Learn Vue' : 'Master Vue';
      return result;
    },
  },
```

在 vue 中，data 里的返回都可以通过 this，这个指向了 vue app 的 global object 来引用，因此上面的 js 也可以写成

```js
 methods: {
    outputGoal() {
      const randomNumber = Math.random();
      const result = randomNumber < 0.5 ? this.courseGoalA : this.courseGoalB;
      return result;
    },
  },
```

- 对于 有可能出现在变量里的 html 元素，比如

```js
 courseGoalB: '<h2>master Vue!</h2>',
```

可以使用 v-html 来转译

```html
<p v-html="outputGoal()" />
```

但是这种方法应尽量避免，因为可能会带来 xss 隐患

### Declarative Approach：

- We define the final picture or declare the goal, we don't define the steps of getting that picture on the screen or care about the steps of getting there

### Event

- 使用 `v-on` 可以用来接绑定的事件，可以直接在标签属性中使用 data 中的变量，`v-on:`可以用@来简写

```js
<section id="events">
  <h2>Events in Action</h2>
  <button v-on:click="counter++">Add</button>
  <button v-on:click="counter--">Remove</button>
  <p>Result: {{ counter }}</p>
</section>;

const app = Vue.createApp({
  data() {
    return {
      counter: 0,
    };
  },
});
```

- 但是我们并不想使用这种方法，html 中应该仅关于输出内容

  - 因此我们可以使用 methods
  - methods 中使用 this，来直接获取 data 中的变量
  - v-on 跟着的方法可以加()，也可以不加()

  ```js
  const app = Vue.createApp({
    data() {
      return {
        counter: 0,
      };
    },
    methods: {
      add() {
        this.counter++;
      },
      subtract() {
        this.counter--;
      },
    },
  });

  <section id="events">
    <h2>Events in Action</h2>
    <button v-on:click="add">Add</button>
    <button v-on:click="subtract">Remove</button>
    <p>Result: {{ counter }}</p>
  </section>;
  ```

  - methods 也可以传递参数，html 中只需要在方法的括号里传入参数

  ```js
  methods: {
    add(num) {
      this.counter = this.counter + num;
    },
    subtract(num) {
      this.counter = this.counter - num;
    },
  },

  <button v-on:click="add(5)">Add 5</button>
  <button v-on:click="subtract(5)">Remove 5</button>
  ```

  - 默认的事件，方法均会传递 event，在 vue 中，我们可以在 html 里用$event 来传递事件，假如该事件方法同时需要传递参数

  ```js
   <p>Result: {{ counter }}</p>
      <input type="text" v-on:input="setLastName($event, 'Joe')" />
      <input type="text" v-on:input="setName" />
   <p>Your Name:{{name}}</p>

  methods: {
    setLastName(event, firstName) {
      this.name = firstName + ' ' + event.target.value;
    },
    setName(event) {
      this.name = event.target.value;
    },
  },
  ```

  - 一般浏览器都会跟随一些默认事件，比如当 submit 一个 form 的时候，submit 结束浏览器会默认刷新该页面，想避免这种情况，我们需要在对应的事件中，添加`event.preventDefault();`；或者我们可以启用 vue 的 event modifier，例如`v-on:submit.prevent`
    - 其它常用的 modifier 还有
      - `v-on:click.right`:右键单击
      - `v-on:click.middle`:中键单击
      - `v-on:click.keyup`:某键抬起

  ```js
  confirmInput() {
      this.confirmedName = this.name;
    },
    setName(event, firstName) {
      this.name = firstName + ' ' + event.target.value;
  },

  <input
        type="text"
        v-on:input="setName($event, 'Joe')"
        v-on:keyup.enter="confirmInput"
  />
  ```

  - 对于想仅渲染一次，保留初始值的可以使用 v-once

  ```js
  <p v-once>Starting Counter:{{ counter }}</p>
  ```

- v-model: 如果我们在 bind 一个 input 的情况下，又同时监听了这个 input，例如

```js
<input type="text" v-bind:value="name" v-on:input="setName" />
```

可以使用一个 v-model 直接实现双向绑定

```js
<input type="text" v-model="name" />
```

- 尽量不要使用 method 来渲染某些 data，除非你想每次在页面变化时都执行该方法一次；因为 vue 会在 render 页面的同时，再次执行该 method，例如下面，在每次 counter 更新的时候，output()都会被执行一遍

```js
<button v-on:click="add(5)">Add 5</button>
<button v-on:click="subtract(5)">Remove 5</button>
<p>Result: {{ counter }}</p>
<p>Your Name:{{output()}}</p>

output() {
  return this.name + " " + "Joe";
},
```

想解决这个问题，方法之一是将 {{}}内直接写入 name，但是可能会把部分逻辑代码写在 html 里，另外一种方法则是实用 computed property

- computed property: 只有在方法内的 dependency 有变化时，才会再次执行该方法（初始化页面时依然会被执行一次），一般只用在展示数据，或者更新其它数据在关联数据变化时，注意使用时不加（）

```js
<p>Your Name:{{fullName}}</p>

computed: {
    fullName() {
      return this.name + ' ' + 'Joe';
    },
},
```

- watch property: 根据名字，与 data 内的某项数据绑定，当该 data 变化时，对应的方法一定会执行，不会在 template/html 中出现

```js
watch:{
    name(value){
      this.fullName = value + ' ' + 'Joe'
    }
  },
```

通常，watch 内的方法参数可以为 value，即对应 data 的最新值，也可以是(newValue, oldValue)，因为 watcher 仅仅跟一项数据绑定，因此在作用于数据显示下的功能并没有 computed 好用，它的用法更倾向于在绑定数据发生某些变化时，执行相应的逻辑代码，例如发送请求

- methods：用于绑定的事件，或输出绑定的数据（放在{{}}间）；在输出绑定数据时（放在{{}}间），每当页面更新，该方法都会被执行一次

- Dynamic Styling

  - v-bind 可以结合 inline style 做到 Dynamic Styling

  ```js
  <div
      class="demo"
      :style="{borderColor:boxASelected?'red':'#ccc'}"
      @click="boxSelected('A')"
  ></div>

  methods: {
     boxSelected(box) {
      if (box === 'A') {
        this.boxASelected = !this.boxASelected;
        this.boxBSelected = false;
        this.boxCSelected = false;
      } else if (box === 'B') {
        this.boxASelected = false;
        this.boxBSelected = !this.boxBSelected;
        this.boxCSelected = false;
      } else if (box === 'C') {
        this.boxASelected = false;
        this.boxBSelected = false;
        this.boxCSelected = !this.boxCSelected;
      }
    },
  },
  ```

  - inline style 对于样式并不是一个首选项，我们同样可以使用 v-bind 结合 class 来做到动态 styling

  ```html
  <div
    :class="boxASelected?'demo active':'demo'"
    @click="boxSelected('A')"
  ></div>
  ```

  我们也可以将上面的改成 vue 的新写法，{className:ture/false},如以上，可写为

  ```html
  <div
    :class="{demo:true, active:boxASelected}"
    @click="boxSelected('A')"
  ></div>
  ```

  对于这样存在多个的 class，我们也可以写成

  ```html
  <div :class="['demo', {active:boxASelected}]" @click="boxSelected('A')"></div>
  ```

  在使用 array 的时候，我们也可以直接把 data 的值拿过来

  ```js
  <p :class="[input, toggle]">Style me!</p>

  data() {
    return {
      input: '',
      toggle: 'visible',
      inline: '',
    };
  },
  ```

  使用 Vue 推荐的写法，我们可以把 demo 拿出去，因此 可写为

  ```html
  <div
    class="demo"
    :class="{active:boxASelected}"
    @click="boxSelected('A')"
  ></div>
  ```

  - 我们还可以通过 computed 把逻辑全部放到 script 里

  ```js
  <div class="demo" :class="boxAClasses" @click="boxSelected('A')"></div>

  computed: {
    boxAClasses() {
      return { active: this.boxASelected };
    },
  },
  ```

  ## Section 3: Rendering Conditional Content & Lists