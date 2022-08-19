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
      - `v-on:click.stop`:停止事件冒泡

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

### v-if

使用 v-if 来直接动态渲染某个标签，其中 v-if 后面可以跟随数据，也可以跟随表达式

```html
<p v-if="goals.length===0">
  No goals have been added yet - please start adding some!
</p>
```

v-else 必须紧跟着 v-if

```html
<p v-if="goals.length===0">
  No goals have been added yet - please start adding some!
</p>
<ul v-else>
  <li>Goal</li>
</ul>
```

v-else-if 可以紧跟着判断

```html
<p v-if="goals.length===0">
  No goals have been added yet - please start adding some!
</p>
<ul v-else-if="goals.length>0">
  <li>Goal</li>
</ul>
```

### v-show

可以单独出现，用法也与 v-if 相似，后面跟条件

```html
<p v-show="goals.length===0">
  No goals have been added yet - please start adding some!
</p>
```

不同点在于，v-show 通过 css 的 display:true/none 来隐藏显示的内容，v-if 则是真的不挂载渲染节点；通常情况下使用 v-if 要优先于 v-show，因为我们不希望隐藏大部分不需要渲染的节点；当一块元素需要频繁切换显示状态的时候，我们可以使用 v-show

### v-for

v-for 后面可以跟表达式来直接循环渲染，注意表达式中取出的单个数据变量，仅能在 v-for 的标签间使用

```html
<li v-for="goal in goals">{{goal}}</li>
<li v-for="(goal, index) in goals">{{ goal }} - {{index}}</li>
```

v-for 不仅可以 loop array，还可以 loop object，并且取到里面的 key

```html
<li v-for="(value,key) in {name: 'Max',age:31} ">{{key}}:{{ value }}</li>
```

v-for 还可以 loop num，

```html
<li v-for="num in 10 ">{{num}}</li>
```

v-for 中还可以添加事件

```js
<li v-for="(goal, index) in goals" @click="removeGoal(index)">
  {{ goal }} - {{index}}
</li>

methods: {
    addGoal() {
      this.goals.push(this.enteredValue);
    },
    removeGoal(index) {
      this.goals.splice(index, 1);
    },
},
```

当使用 for 进行渲染的时候，如果没有 key 绑定每一个 element，那么 vue 可能不会变动 node，而只是变更 node 内的部分内容，因此需要添加 key 给每一个 element，key 是一个 vue 添加的标签

```html
<li v-for="(goal, index) in goals" :key="goal" @click="removeGoal(index)">
  <p>{{ goal }} - {{index}}</p>
  <input type="text" @click.stop />
</li>
```

## Section 4: Course Project: The Monster Slayer Game

- 生成 min ～ max 之间的 random number：
  ```js
  Math.floor(Math.random() * (max - min)) + min;
  ```
- 从 array 中移除一个元素
  ```js
  this.goals.splice(index, 1);
  ```
- methods 中可以使用 this 来调用其他 methods
  ```js
   methods: {
    attack() {
      this.monsterHealth -= getRandomValue(5,12);
      this.beAttacked();
    },
    beAttacked() {
      this.playerHealth -= getRandomValue(8,15);
    },
  },
  ```
- inline style 的写法
  ```html
  <div
    class="healthbar__value"
    :style="{width:playerHealth + '%'}"
    :value="playerHealth"
  ></div>
  ```
- 使用 computed property 来提取 inline 的逻辑

  ```js
  <div
    class="healthbar__value"
    :style="getPlayerHealthBar"
    :value="playerHealth"
  ></div>

  computed: {
    getPlayerHealthBar() {
      return { width: this.playerHealth + '%' };
    },
  },
  ```

- 使用 computed property 来拿到判断返回值

  ```js
  <button :disabled="getSpecialAttackDisable" @click="specialAttack">
    SPECIAL ATTACK
  </button>

  computed: {
    getSpecialAttackDisable() {
      return this.currentRound % 3 !== 0;
    },
  },
  ```

- 使用 unshift，将内容插入到 array 头部
  ```js
  addLogMessage(who, action, value) {
      this.logMessages.unshift({
        actionBy: who,
        actionType: action,
        actionValue: value,
      });
  },
  ```

## Section 5: How Vue Works

### Reactivity

Vue 中使用了类似 JS Proxy 的机制，使得数据与 html 的渲染或者其他数据进行了绑定，这样在该数据变化的同时，被绑定的部分也会自动更新

```js
const data = {
  message: 'Hello!',
  longMessage: 'Hello! World!',
};

const handler = {
  set(target, key, value) {
    if (key === 'message') {
      target.longMessage = value + ' World!';
    }
    target.message = value;
  },
};

const proxy = new Proxy(data, handler);
proxy.message = 'Hello!!!!';
console.log(proxy.longMessage);
```

### template

让 vue 来控制渲染的 html 的部分，我们可以叫他 template；我们可以在 template 中定义要控制渲染的内容，再通过 mount 的方式，来挂载改节点

```js
<section id="app2"></section>;

const app2 = Vue.createApp({
  template: `
    <p>{{ favoriteMeal }}</p>
  `,
  data() {
    return {
      favoriteMeal: 'Pizza',
    };
  },
});

app2.mount('#app2');
```

### ref

在某些场景下，我们并不想做双向绑定，而是想更灵活的控制某个 element，这时候我们可以使用 ref 来锁定这个 element，通过$refs 找到它，并加以处理

```js
<input type="text" ref="userText" >
methods: {
    setText() {
      this.message = this.$refs.userText.value;
    },
  },
```

### How Vue Updates the DOM

- Vue Instance 包含了我们写的 Vue code：data，methods，computed...
- Browser DOM 则是我们通过 Vue controlled template 最终渲染的地方
- Virtual DOM 存在于前面两者之间，仅存在于内存中，当每次 Vue Instance 更新，virtual dom 会进行比较，只有更新的/不同的部分会渲染到真实 DOM 中

### Vue lifecycle

beforeCreate() -> created() -> [compile template] -> beforeMount() -> mounted()
[Mounted Vue Instance, Data Changed] -> beforeUpdated() -> updated()
[instance unmounted] -> beforeUnmount() -> unmounted() 一般我们都不会 unmount

```js
  beforeCreate() {
    console.log('beforeCreate()');
  },
  created() {
    console.log('created()');
  },
  beforeMount() {
    console.log('beforeMount()');
  },
  mounted() {
    console.log('mounted()');
  },
  beforeUpdate() {
    console.log('beforeUpdate()');
  },
  updated() {
    console.log('updated()');
  },
  beforeUnmount() {
    console.log('beforeUnmount()');
  },
  unmounted() {
    console.log('unmounted()');
  },

```

## Section 6 : Introducing Components

我们可以把单独一部分 html 抽出来，在里面封装好数据和逻辑，做成一个独立的组件，每一个组件就像一个 mini app

```js
app.component('friend-contact', {
  template: `
  <li  key={{friend.id}}>
    <h2>{{friend.name}}</h2>
      <button @click="toggleDetails">Show Details</button>
      <ul v-show="showDetails">
        <li><strong>Phone:</strong>{{friend.phone}}</li>
        <li><strong>Email:</strong>{{friend.email}}</li>
      </ul>
  </li>
  `,
  data() {
    return {
      showDetails: true,
      friend: {
        id: 'manuel',
        name: 'Manuel Lorenz',
        phone: '01234 5678 991',
        email: 'manuel@localhost.com',
      },
    };
  },
  methods: {
    toggleDetails() {
      this.showDetails = !this.showDetails;
    },
  },
});
```

注意，component 的命名必须区别于 html 默认标签，因此使用两个单词相连中间加“-”的形式是有必要的

## Section 7 : Vue CLI

vue create vue-test-app

### vue file

- template
- script
- style

默认情况下，创建 App.vue，用来存放 app 的配置信息

'./'开始为相对路径，指向与该文件同级的位置

## Section 8 : Component Communication

### 父传子

- props
  - 在子组件中定义，一旦定义后就跟 data 一样，可以在全局使用
  - 传递过来的 props，在子组件中是无法更改的
  - props 的命名遵循驼峰法
  ```vue
  <template>
    <li>
      <h2>{{ name }}</h2>
      <button @click="toggleDetails()">
        {{ detailsAreVisible ? 'Hide' : 'Show' }} Details
      </button>
      <ul v-if="detailsAreVisible">
        <li><strong>Phone:</strong> {{ phoneNumber }}</li>
        <li><strong>Email:</strong> {{ emailAddress }}</li>
      </ul>
    </li>
  </template>
  <script>
  export default {
    props: ['name', 'phoneNumber', 'emailAddress'],
    data() {
      return {
        detailsAreVisible: false,
        friend: {
          id: 'manuel',
          name: 'Manuel Lorenzo',
          phone: '01234 5678 991',
          email: 'manuel@localhost.com',
        },
      };
    },
    methods: {
      toggleDetails() {
        this.detailsAreVisible = !this.detailsAreVisible;
      },
    },
  };
  </script>
  ```
  - props 的定义中可以添加相关的属性，以及 validator，如果父组件传值，在 console 中 vue 会给出 warning
    ```js
    props: {
      name: { type: String, required: true },
      phoneNumber: { type: String, required: true },
      emailAddress: { type: String, required: true },
      isFavorite: {
        type: String,
        required: false,
        default: '0',
        validator: function (value) {
          return value == '0' || value == '1';
        },
      },
    },
    ```
  - 通过父组件的标签属性来传递，标签属性的命名在父组件中由驼峰转换成了
    "-"与小写
  ```vue
  <template>
    <header><h1>My Friends</h1></header>
    <ul>
      <friend-contact
        name="manuel"
        phone-number="01234 5678 991"
        email-address="manuel@localhost.com"
      ></friend-contact>
      <friend-contact
        name="julie"
        phone-number="09876 543 221"
        email-address="julie@localhost.com'"
      ></friend-contact>
    </ul>
  </template>
  ```
  - 在父组件中，传递的 props value 只能是字符串，因此，当出现非字符串类型时，需要 v-bind
  ```html
  <ul>
    <friend-contact
      name="manuel"
      phone-number="01234 5678 991"
      email-address="manuel@localhost.com"
      :is-favorite="true"
    ></friend-contact>
    <friend-contact
      name="julie"
      phone-number="09876 543 221"
      email-address="julie@localhost.com'"
      :is-favorite="false"
    ></friend-contact>
  </ul>
  ```
  - 父组件也可以通过 v-for 来进行将自身的 data 值传递给 props，需要注意的是所有的数据均需要 bind
  ```html
  <ul>
    <friend-contact
      v-for="friend in friends"
      :key="friend.id"
      :name="friend.name"
      :phone-number="friend.phone"
      :email-address="friend.email"
      :is-favorite="friend.fav"
    ></friend-contact>
  </ul>
  ```

### 子传父

- 子组件可以通过 emit custom events 的方法，令父组件监听该事件，当捕捉到事件触发时，执行对应的方法；父组件里监听的绑定事件名，要与子组件里 emit 的一致
- $emit 的第二个及往后的参数，将作为参数依次传递给父组件的方法
  子组件：

  ```
  <button @click="toggleFavorite()">
      {{ isFavorite ? 'UnFav' : 'Fav' }}
  </button>

  methods: {
    toggleFavorite() {
      this.$emit('toggle-favorite', this.id);
    },
  },
  ```

  父组件：

  ```
  <ul>
    <friend-contact
      v-for="friend in friends"
      :key="friend.id"
      :id="friend.id"
      :name="friend.name"
      :phone-number="friend.phone"
      :email-address="friend.email"
      :is-favorite="friend.fav"
      @toggle-favorite="toggleFavorite"
    ></friend-contact>
  </ul>

  methods: {
    toggleFavorite(id) {
      const friend = this.friends.find((f) => f.id === id);
      friend.fav = !friend.fav;
    },
  }
  ```

  - 子组件中，我们也可以直接在标签里 emit

  ```html
  <button @click="$emit('delete-contact', id)">Delete</button>
  ```

  - 如果 emit 事件时 passthrough，即父传子，子传孙子，则在子中的写法即为, 第二个参数为$event

  ```html
  <knowledge-grid
    :topics="topics"
    @select-topic="$emit('select-topic', $event)"
  ></knowledge-grid>
  ```

  - 从 array 中取到对应 id 的 object

  ```js
  const friend = this.friends.find((f) => f.id === id);
  ```

  - 从 array 中移除对应 id 的 object

  ```js
  this.friends = this.friends.filter((f) => f.id !== id);
  ```

### emits

- 为了更方便的管理，我们可以在 emits 这个 property 里注册需要 emit 的事件

```js
emits: ['toggle-favorite'],
```

- 一种更为详细的写法是，我们可以在 emits 里详细定义事件的类型，和对应该事件会执行的部分逻辑，注意对象类型是 function 的时候，参数为$emit 的第二个参数，即为向父组件发送的数据

```js
  emits: {
    'toggle-favorite': function (id) {
      if (id) {
        return true;
      } else {
        return false;
      }
    },
  },
```

### provide/inject

- 解决祖孙组件之间的通信问题，除了传统的逐层传递，还可以将祖父组件想要传递的信息放入 provide，再在子孙的 inject 属性中提取，即可实现数据共享
- 两者必须是一条分支下来的关系，比如祖父-孙子
- provides 与 inject 可以解决祖孙节点的传值，而且是中间经过的节点不会再看到需要传递的数据或方法，但是默认情况下如果在祖孙之间并不存在太多层，则不建议使用该方法，因为会大大降低代码可读性。我们仅仅在祖父节点中定义了 provide，但是却不知道他会在哪里被使用，同样道理，我们在子孙节点中使用了 inject，我们却不知道是哪里来的 provide
  祖父组件：

```js
provide() {
    return { topics: this.topics };
},
```

孙子组件：

```js
export default {
  inject: ['topics'],
  emits: ['select-topic'],
};
```

- 方法也可以依照此来传递，需要注意的是，传递方法的时候，provide 仅仅填的是注册的引用名和指向的具体方法，在子孙组件中，如果该方法需要参数，需要记得添加
  祖父组件

```vue
<template>
  <div>
    <active-element
      :topic-title="activeTopic && activeTopic.title"
      :text="activeTopic && activeTopic.fullText"
    ></active-element>
    <knowledge-base :topics="topics"></knowledge-base>
  </div>
</template>
<script>
export default {
  data() {
    return {
      topics: [
        {
          id: 'basics',
          title: 'The Basics',
          description: 'Core Vue basics you have to know',
          fullText:
            'Vue is a great framework and it has a couple of key concepts: Data binding, events, components and reactivity - that should tell you something!',
        },
        {
          id: 'components',
          title: 'Components',
          description:
            'Components are a core concept for building Vue UIs and apps',
          fullText:
            'With components, you can split logic (and markup) into separate building blocks and then combine those building blocks (and re-use them) to build powerful user interfaces.',
        },
      ],
      activeTopic: null,
    };
  },
  provide() {
    return { topics: this.topics, selectTopic: this.activateTopic };
  },
  methods: {
    activateTopic(topicId) {
      this.activeTopic = this.topics.find((topic) => topic.id === topicId);
    },
  },
};
</script>
```

子孙组件

```vue
<template>
  <li>
    <h3>{{ topicName }}</h3>
    <p>{{ description }}</p>
    <button @click="selectTopic(id)">Learn More</button>
  </li>
</template>

<script>
export default {
  inject: ['selectTopic'],
  props: ['id', 'topicName', 'description'],
  emits: ['select-topic'],
};
</script>
```

## Section 9: Diving Deeper Into Components

### Global VS Local Components

- 在 main.js 中通过 app.component 注册的，是 global component，可以在整个项目中作为自定义 html 标签使用
- global component 会在 app 加载的时候同时加载（load）

### components

- 可以将组件使用到的 component，通过 import，加 components property 注册的方式，引入它，此时它仅会在引用它的 component 中使用，是一个 local component
- 注意，注册 component 时 key 填的是作为使用的标签，后面跟的是具体引用

  ```js
  import TheHeader from './components/TheHeader.vue';
  export default {
    components: {
      'the-header': TheHeader,
    },
    data() {
      return {
        activeUser: {
          name: 'Maximilian Schwarzmüller',
          description: 'Site owner and admin',
          role: 'admin',
        },
      };
    },
  };
  ```

- 注册 component 填入的 key，也可以是 pascal case 例如

  ```js
  components: {
    TheHeader: TheHeader,
  },
  ```

  此时在 template 中的引用，既可以是 TheHeader 标签，也可以是 the-header，后者为 Vue 自动生成，需要注意的是，如果使用后者，则无法添加自闭合

  ```js
  <the-header></the-header>
  <TheHeader />
  ```

  同时,在 js 语法中，如果 key 与 value 相同，则 key 可以省略

  ```js
  <the-header></the-header>
  <TheHeader />

  components: {
    TheHeader,
  },
  ```

### scoped styling

如果是在 vue 文件<style></style>之间加的 style，默认会作用到整个 app，如果想限定该段 style 仅作用于当前组件，需要加 scoped，例如

```vue
<style scoped>
header {
  width: 100%;
  height: 5rem;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #14005e;
}

header h1 {
  color: white;
  margin: 0;
}
</style>
```

### slot

类似于 react 中的 layout，如果我们想定义一个 wrapper 类型的 component，这个 component 用于包裹其它的 component，这时候就可以用插槽 slot，<slot></slot>之间即为装配的 children component
父亲 component：
BaseCard.vue

```vue
<template>
  <section>
    <slot></slot>
  </section>
</template>

<script>
export default {};
</script>

<style scoped>
section {
  margin: 2rem auto;
  max-width: 30rem;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
}
</style>
```

孩子 component：
UserInfo.vue

```vue
<template>
  <base-card>
    <div>
      <h3>{{ fullName }}</h3>
      <base-badge :type="role" :caption="role.toUpperCase()"></base-badge>
    </div>
    <p>{{ infoText }}</p>
  </base-card>
</template>

<script>
export default {
  props: ['fullName', 'infoText', 'role'],
};
</script>
```

当我们构建起的 wrapper component 想令使用它的 components 不再仅仅拘泥于放置在一个位置，即 wrapper component 更复杂，更有结构化时，我们可以使用 v-slot 标签，即在父 component 中通过定义 slot name， 然后再在子 component 中使用 v-slot 指定对应的部分，来实现对应父 component 中不同区域的放置
父 component：BaseCard.vue

```vue
<template>
  <section>
    <header>
      <slot name="header"></slot>
    </header>
    <slot></slot>
  </section>
</template>
```

子 component：UserInfo.vue

```vue
<template>
  <base-card>
    <template v-slot:header>
      <h3>{{ fullName }}</h3>
      <base-badge :type="role" :caption="role.toUpperCase()"></base-badge>
    </template>
    <p>{{ infoText }}</p>
  </base-card>
</template>
```

当 template 作为 slot 传递给父组件的时候，传递过去的 template 并不会带着在该文件中的 scoped style，因此，必须事先把对应的 style 移动到父组件中才能令 style 生效
子组件

```vue
<template>
  <base-card>
    <template v-slot:header>
      <h3>{{ fullName }}</h3>
      <base-badge :type="role" :caption="role.toUpperCase()"></base-badge>
    </template>
    <p>{{ infoText }}</p>
  </base-card>
</template>

<script>
export default {
  props: ['fullName', 'infoText', 'role'],
};
</script>

<style scoped>
/* 因为以下style对应的template片段会被传递给父组件，而该style又是scoped，因此不会生效，必须移动到对应的父组件才可以 */
section header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
```

如果在父组件的 slot 部分添加代码，则如果子组件在传递过程中，并没有对应的 slot 部分内容，则默认显示父组件中 slot 及其中的代码
父组件

```vue
<template>
  <section>
    <header>
      <slot name="header">
        <h1>The Default</h1>
      </slot>
    </header>
    <slot></slot>
  </section>
</template>
```

子组件

```vue
<template>
  <base-card>
    <p>{{ infoText }}</p>
  </base-card>
</template>
```

在上面这个例子中，即使子组件没有 header 部分，而父组件又定义了 header slot，则 html 一定会产生对应的 slot 节点，想避免这个问题，我们可以使用 v-if 在父组件中做优化，注意这种情况发生在父组件 slot 没有默认代码，而子组件又没有传递代码 template 过来
父组件

```vue
<template>
  <section>
    <header v-if="$slots.header">
      <slot name="header"></slot>
    </header>
    <slot></slot>
  </section>
</template>
```

shorthand: 子组件中的“v-slot：”一律可以用 “#”来代替

```vue
<template>
  <base-card>
    <template #header>
      <h3>{{ fullName }}</h3>
      <base-badge :type="role" :caption="role.toUpperCase()"></base-badge>
    </template>
    <p>{{ infoText }}</p>
  </base-card>
</template>
```

### scoped slot

假如子组件 slot 部分想使用父组件中的 data，可以在父组件的 slot 定义里 bind 数据
父组件

```vue
<template>
  <ul>
    <li v-for="goal in goals" :key="goal">
      <slot :item="goal" another-props="..."></slot>
    </li>
  </ul>
</template>

<script>
export default {
  data() {
    return {
      goals: ['Finish', 'Vue'],
    };
  },
};
</script>
```

子组件

```vue
<template>
  <div>
    <the-header></the-header>
    <badge-list></badge-list>
    <user-info
      :full-name="activeUser.name"
      :info-text="activeUser.description"
      :role="activeUser.role"
    ></user-info>
    <course-goals>
      <template #default="slotProps">
        <h2>{{ slotProps.item }}</h2>
        <p>{{ slotProps.anotherProps }}</p>
      </template>
    </course-goals>
  </div>
</template>
```

以上注意，子组件中关于 props 名称的自动转换；
如果子组件中仅使用了一个 slot，可以将值传递的部分放置在父组件标签名内做到简写
子组件

```vue
<course-goals #default="slotProps">
  <h2>{{ slotProps.item }}</h2>
  <p>{{ slotProps.anotherProps }}</p>
</course-goals>
```

### Dynamic Components

想要动态的切换两个组件显示，我们通常可以通过定义显示标签+v-if 来控制切换

```vue
<template>
  <div>
    <the-header></the-header>
    <button @click="setSelectedComponent('active-goals')">Active Goals</button>
    <button @click="setSelectedComponent('manage-goals')">Manage Goals</button>
    <active-goals v-if="selectedComponent === 'active-goals'"></active-goals>
    <manage-goals v-if="selectedComponent === 'manage-goals'"></manage-goals>
  </div>
</template>

<script>
import TheHeader from './components/TheHeader.vue';
import ActiveGoals from './components/ActiveGoals.vue';
import ManageGoals from './components/ManageGoals.vue';

export default {
  components: {
    TheHeader,
    ActiveGoals,
    ManageGoals,
  },
  data() {
    return {
      selectedComponent: 'active-goals',
      activeUser: {
        name: 'Maximilian Schwarzmüller',
        description: 'Site owner and admin',
        role: 'admin',
      },
    };
  },
  methods: {
    setSelectedComponent(cmp) {
      this.selectedComponent = cmp;
    },
  },
};
</script>
```

但是这样多行的 v-if 对于代码的可扩展性并不好，对于这种情况，vue 开发了<component>标签，其中 is 的属性代表了对应显示的 component 名字，类似于 switch，因此上面的代码可以整理为

```vue
<template>
  <div>
    <the-header></the-header>
    <button @click="setSelectedComponent('active-goals')">Active Goals</button>
    <button @click="setSelectedComponent('manage-goals')">Manage Goals</button>
    <keep-alive>
      <component :is="selectedComponent"></component>
    </keep-alive>
  </div>
</template>
```

#### keep-alive

当我们通过 component 标签来切换组件的时候，被换的组件包括它的 state 都会消失，倘若我们在其中保存有任何的状态都会丢失，为防止这种情况，我们会使用 keep-alive 标签，这样切换过的 component 会保存在 cache 里而不会被销毁

```vue
<keep-alive>
  <component :is="selectedComponent"> </component>
</keep-alive>
```

vue 中可以使用 ref 来指定一个元素和它的名字，然后在 data 通过 refs 来找到这个元素

```vue
<template>
  <h2>Manage Goals</h2>
  <input type="text" ref="goal" />
  <button @click="setGoal">Set Goal</button>
  <error-alert v-if="!isValid">
    <h2>Input Invalid</h2>
    <button @click="setIsValid">OK</button>
  </error-alert>
</template>

<script>
import ErrorAlert from './ErrorAlert.vue';
export default {
  components: {
    ErrorAlert,
  },
  data() {
    return {
      isValid: true,
    };
  },
  methods: {
    setGoal() {
      const enteredGoal = this.$refs.goal.value;
      if (enteredGoal === '') {
        this.isValid = false;
      }
    },
    setIsValid() {
      this.isValid = !this.isValid;
    },
  },
};
</script>
```

#### teleport

上面这个例子中，因为 error-alert 是个 dialog，语义上它的节点位置应该位于 body 中，而不是 manage-goals 这个组件的父节点下，因此，我们可以使用 <teleport to="body"> 来将该 component 挂载在我们指定的节点下，to 后面即是我们指定挂载的位置

```vue
<template>
  <h2>Manage Goals</h2>
  <input type="text" ref="goal" />
  <button @click="setGoal">Set Goal</button>
  <teleport to="body">
    <error-alert v-if="!isValid">
      <h2>Input Invalid</h2>
      <button @click="setIsValid">OK</button>
    </error-alert>
  </teleport>
</template>
```

Vue2 里每一个 template 下面必须有一个子节点，子节点里才能挂在各种兄弟节点，但是 Vue3 里取消了这个限制，也就是<template></template>中间可以不用加 root div 了

### Vue Style Guide

https://v2.vuejs.org/v2/style-guide/?redirect=true

- base Component 应该命名为“BaseXxxx”，“AppXxxx”，“VXxxx”，例如“BaseButton.vue”
- 对于每个页面只可使用一次的单例组件，命名应该以“The”开头，比如“TheHeader.vue”

### File Structure

- ui 下面放 base 组件
- layout 下面放 theHeader 类
- 各类 feature 组件可以放到以 feature 命名的文件夹下

## Section 10: Course Project: The Learning Resources App

### props

从父组件传递 props 的时候，对于使用 data 里的变量，标签的属性前一定要 bind，不然传递不了

```vue
<template>
  <ul>
    <res-card v-for="res in storedRes" :key="res.id" :res="res"></res-card>
  </ul>
</template>
```

- 对于 Base 的组件，一般是在 main.js 里做全局导入
- 引用自定义组件时，传入的 event，会自动落入组件的 root node，比如下面就会落入 base-button 的 root，即加入<button>里

```vue
<base-button @click="changePanel">{{
      activePanel === 'learning-res' ? 'Adding Res' : 'View Res'
    }}
</base-button>
```

- 对于 dynamic 组件的 props，我们既可以通过 props 来传递

```vue
<component :is="activePanel" :storedRes="storedRes"></component>
```

也可以通过 provide/inject 来完成
祖先组件

```vue
<base-card>
    <base-button @click="changePanel">{{
      activePanel === 'learning-res' ? 'Adding Res' : 'View Res'
    }}</base-button>
  </base-card>
<component :is="activePanel"></component>

<script>
export default {
  components: {
    LearningRes,
    TheHeader,
    AddingRes,
  },
  data() {
    return {
      activePanel: 'learning-res',
      storedRes: [],
    };
  },
  provide() {
    return {
      storedRes: this.storedRes,
    };
  },
};
</script>
```

子孙组件

```vue
<script>
export default {
  inject: ['storedRes'],
};
</script>
```

- form 的 submit button 方法可以写在 form 标签内
- 除了用 v-model 可以做数据同步，还可以用 ref
- 对于动态组件中的 emit 事件发送与接受，也可以使用 provide/inject
  父组件

```vue
<template>
  <the-header title="Remember ME"> </the-header>
  <base-card>
    <base-button @click="changePanel">{{
      activePanel === 'learning-res' ? 'Adding Res' : 'View Res'
    }}</base-button>
  </base-card>
  <component :is="activePanel"></component>
</template>
<script>
export default {
  data() {
    return {
      activePanel: 'learning-res',
      storedRes: [],
    };
  },
  provide() {
    return {
      storedRes: this.storedRes,
      addResource: this.addRes,
    };
  },
  methods: {
    addRes(res) {
      this.storedRes.unshift(res);
    },
  },
};
</script>
```

子组件

```vue
<template>
  <base-card>
    <form @submit.prevent="submitResource">
      <header>
        <h3>Add a Resource</h3>
      </header>
      <div class="form-control">
        <label for="title">Title</label>
        <input type="text" id="title" ref="titleInput" />
      </div>
      <div class="form-control">
        <label for="description">Description</label>
        <textarea name="description" id="description" ref="desInput" row="3" />
      </div>
      <div class="form-control">
        <label for="link">Link</label>
        <input type="url" name="link" id="link" ref="linkInput" />
      </div>
      <div>
        <base-button type="submit">Add</base-button>
      </div>
    </form>
  </base-card>
</template>

<script>
export default {
  data() {
    return {
      isSubmitted: false,
      isError: false,
    };
  },
  inject: ['addResource'],
  methods: {
    submitResource() {
      if (
        this.$refs.titleInput.value.trim() === '' ||
        this.$refs.desInput.value.trim() === '' ||
        this.$refs.linkInput.value.trim() === ''
      ) {
        this.setIsError();
        return;
      }
      this.addResource({
        id: Date.now().toString(),
        title: this.$refs.titleInput.value,
        description: this.$refs.desInput.value,
        link: this.$refs.linkInput.value,
      });
      this.setIsSubmitted();
      this.$refs.titleInput.value = '';
      this.$refs.desInput.value = '';
      this.$refs.linkInput.value = '';
    },
  },
};
</script>
```

- 向 array 插入一个数据，并将该数据置顶

```js
this.storedRes.unshift(res);
```

- 除掉输入中的开头空格

```js
this.$refs.titleInput.value.trim();
```

- 在 dialog 实现点击背景关闭功能，可以通过在 dialog 的 root 节点添加 emit 事件完成

```vue
<template>
  <teleport to="body">
    <div @click="$emit('close')"></div>
    <dialog open>
      <header>
        <slot name="header">
          <h2>{{ title }}</h2>
        </slot>
      </header>
      <section>
        <slot></slot>
      </section>
      <menu>
        <slot name="actions">
          <base-button @click="$emit('close')">Close</base-button>
        </slot>
      </menu>
    </dialog>
  </teleport>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      required: false,
    },
  },
  emits: ['close'],
};
</script>
```

- 在 provide/inject 过程中，如果父组件 provide 的数据中，在方法中进行了重新赋值，则新的数据并不会自动更新到 provide 的全局数据中，因此对操作的 provide 相关数据，必须直接操作

```js
export default {

  data() {
    return {
      storedRes: [
        {
          id: 'official-guide',
          title: 'Official Guide',
          description: 'The official Vue.js documentation.',
          link: 'https://vuejs.org',
        },
      ],
    };
  },
  provide() {
    return {
      storedRes: this.storedRes,
    };
  },

  methods: {
    addRes(res) {
      this.storedRes.unshift(res);
      this.activePanel = 'learning-res';
    },
    removeRes(id) {
      //注释中的方法不会生效，因为provide中的地址并不会更新
      // this.storedRes = this.storedRes.filter((r) => r.id !== id);
      const resIndex = this.storedRes.findIndex((res) => res.id === id);
      this.storedRes.splice(resIndex, 1);
    },
  },
};
</script>
```

- 根据 id 查找数据，并将之删除

```js
const resIndex = this.storedRes.findIndex((res) => res.id === id);
this.storedRes.splice(resIndex, 1);
```

## Section 11: Forms

### v-model modifier

- v-model 可以将类型自动转换，如果在 input 中，规定了其类型是 number，用 this.$refs.input.value 得到的值其实是一个 string 类型，因为默认用户输入的 value 都是 string，而如果尝试用 v-model，则它会进行进一步转化，将这个 value 根据类型转换为了 number

```vue
<input id="age" name="age" type="number" v-model="userAge" />
```

也可以通过在 model 后面加 modifier 的方式，强制将 value 的类型做转换，如

```vue
<input id="age" name="age" type="text" v-model.number="userAge" />
```

除了 number，还有两个 modifier，lazy：决定了 v-model 更新的频率，trim：自动去掉字符串开头结尾的空格

- v-model，还可以绑定 select dropdown，其中 data 中对应的初始值即为 select 的默认选项

```vue
<select id="referrer" name="referrer" v-model="referrer">
  <option value="google">Google</option>
  <option value="wom">Word of mouth</option>
  <option value="newspaper">Newspaper</option>
</select>
```

- v-model, 可以绑定 checkbox 和 radio select，注意对于这两种，每一个选项都需要附带一个唯一的 value，以方便 v-model 做出识别

```vue
<div class="form-control">
  <h2>What are you interested in?</h2>
  <div>
    <input
      id="interest-news"
      name="interest"
      type="checkbox"
      value="News"
      v-model="interest"
    />
    <label for="interest-news">News</label>
  </div>
  <div>
    <input
      id="interest-nothing"
      name="interest"
      type="checkbox"
      value="Nothing"
      v-model="interest"
    />
    <label for="interest-nothing">Nothing</label>
  </div>
</div>
<div class="form-control">
  <h2>How do you learn?</h2>
  <div>
    <input
      id="how-video"
      name="how"
      type="radio"
      value="Video Courses"
      v-model="how"
    />
    <label for="how-video">Video Courses</label>
  </div>
  <div>
    <input
      id="how-other"
      name="how"
      type="radio"
      value="Other"
      v-model="how"
    />
    <label for="how-other">Other</label>
  </div>
</div>

  data() {
    return {
      interests: [],
      how: '',
    };
  },
```

- v-model 绑定 checkbox 时，如果同一个 name 下，有好多选项（checkbox），则 v-model 绑定的是个 array；如果只有一个 name，则绑定的是一个数据

```vue
<div class="form-control">
      <input
        type="checkbox"
        id="confirm-terms"
        name="confirm-terms"
        v-model="confirm"
      />
      <label for="confirm-terms">Agree to terms of use?</label>
</div>
```

### input validation

首先需要在 input 中，对 blur 事件进行绑定，即用户输入完毕的 input 检测对应方法，然后对在 blur 对应的方法中进行相应的数据变化，来影响对应的变化，比如输出提示信息，改变标题 css style 等

```vue
<div class="form-control" :class="{ invalid: userNameValidity === 'invalid' }">
  <label for="user-name">Your Name</label>
  <input
    id="user-name"
    name="user-name"
    type="text"
    v-model.trim="userName"
    @blur="validateInput"
  />
  <p v-if="userNameValidity === 'invalid'">
    Please enter a valid user name
  </p>
</div>

<script>
export default {
  data() {
    return {
      userName: '',
      userNameValidity: 'pending',
    };
  },
  methods: {
    validateInput() {
      if (this.userName === '') {
        this.userNameValidity = 'invalid';
      } else {
        this.userNameValidity = 'valid';
      }
    },
  },
};
</script>
```

- form 中的 button 默认 type 是 submit，如果不想每一个 button 都点击触发提交事件，则需要更改其类型

```vue
<li><button type="button">Poor</button></li>
```

### v-model on custom component

- emit 在子组件中可以放在方法里直接传递

```js
methods: {
    activate(option) {
      this.activeOption = option;
      this.$emit('select-rating', option);
    },
  },
```

等效于在 template 中，但是提高了可读性

```vue
<li><button type="button" @click="$emit('select-rating', 'Poor')">Poor</button></li>
```

在子组件和父组件之间的数值传递，除了使用 emit 外，我们同样可以使用 v-model 一次达成，首先我们需要在子组件中定义 props 跟 emits，然后再在父组件中做 v-model 绑定即可
子组件

```vue
<template>
  <li :class="{ active: modelValue === 'Average' }">
    <button type="button" @click="activate('Average')">Average</button>
  </li>
</template>
<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  methods: {
    activate(option) {
      this.$emit('update:modelValue', option);
    },
  },
};
</script>
```

父组件

```vue
<rating-control v-model="userRating"></rating-control>
```

## Section 20: Reusing Functionality: Mixins & Custom Composition Functions

使用 options api 的 mixin，可以让你在组件件 share data，methods，property, lifecycle hooks

除了 component，script 中的其它部分都可以抽象为一个 mixin，然后再次在其它 component 中引用，这样所有在 mixin 中的东西都可以变为共享
mixin

```js
export default {
  data() {
    return {
      alertIsVisible: false,
    };
  },
  methods: {
    showAlert() {
      this.alertIsVisible = true;
    },
    hideAlert() {
      this.alertIsVisible = false;
    },
  },
};
```

component

```vue
<template>
  <user-alert v-if="alertIsVisible" title="Add a User?" @close="hideAlert">
    <p>Do you want to continue with adding a user?</p>
  </user-alert>
  <section>
    <h2>Add a User</h2>
    <button @click="showAlert">Add User</button>
  </section>
</template>

<script>
import UserAlert from './UserAlert.vue';
import alertMixin from './../mixins/alert.js';

export default {
  components: {
    UserAlert,
  },
  mixins: [alertMixin],
};
</script>
```

### mixin merging

component 和 mixin 中都可以定义 data，这时候，组件内实际的 data 是两者 merge 的，如果 component 和 mixin 中均定义了相同 data，则 component 中的 data 会覆盖掉 mixin 中的

如果 component 和 mixin 中均定义了相同的 hooks，比如 mounted，则 mixin 中的先执行，component 中的后执行

### Global mixin

可以通过在 main.js 中注册 mixin，则该 mixin 会被自动加载进所有组件
logger.js

```js
export default {
  data() {
    return {
      logIn: 'Mixins are awesome!',
    };
  },
  mounted() {
    console.log('mixin mounted.');
  },
};
```

main.js

```js
import { createApp } from 'vue';
import App from './App.vue';
import loggerMixing from './mixins/logger.js';

const app = createApp(App);

app.mixin(loggerMixing);

app.mount('#app');
```

mixin 会产生一个问题，因为部分 logic 在不同的 mixin 中做了封装，在我们的 component 调用中，如果引入多个 mixin 就会变得难以维护，比如不知道某个方法来自哪个 mixin，或者我们 overwrite 了某个方法

## Section 12: Sending Http Requests

- firebase 的 request 最后一定要加.json，比如

```js
fetch('https://vue-http-demo-default-rtdb.firebasedatabase.app/surveys.json');
```

- 'Content-Type': 'application/json' 意味着该请求发送的内容格式为 json
- 使用 fetch 的方法，发送 post 请求

```js
fetch(
  'https://vue-http-demo-17142-default-rtdb.asia-southeast1.firebasedatabase.app/surveys.json',
  {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      name: this.enteredName,
      rating: this.chosenRating,
    }),
  }
);
```

- 使用 axios 的方法，发送 post 请求，他会自动 set content-type，以及自动将 body 转换为 Json

```js
import axios from 'axios';
axios.post(
  'https://vue-http-demo-17142-default-rtdb.asia-southeast1.firebasedatabase.app/surveys.json',
  {
    name: this.enteredName,
    rating: this.chosenRating,
  }
);
```

- fetch 发送 get 请求

```js
fetch('https://vue-http-demo.app/surveys.json')
  .then((response) => {
    if (response.ok) {
      return response.json();
    }
  })
  .then((data) => {
    this.results = response.data;
    console.log(data);
  });
```

- axios 发送 get 请求

```js
axios
  .get('https://vue-http-demo.app/surveys.json')
  .then((response) => {
    this.results = response.data;
    console.log(this.results);
  })
  .catch((error) => {
    console.log(error);
  });
```

- 一般会把加载数据放在 mount 里

```js
mounted() {
    this.loadResults();
  },
```

## Section 13: Routing: Building "Multi-Page" Single Page Applications

npm install --save vue-router

### config vue-route

- step 1: import { createRouter, createWebHistory } from 'vue-router'
- step 2: 使用 createRouter 方法，传递 path 和 component，以及 history
  - history：设置如何管理 routing history，一般采用默认的 createWebHistory()，及正常管理记录用户的 url 历史
  - routes：告诉 vue 我们要添加哪些 url，以及分别对应哪些 component
- step 3: 使用 app.use 装载 router
- step 4: 在 App 中使用 <router-view>来挂载，所有的在 router 中注册的 component 现在都变成了 global component
  main.js

```js
import { createApp } from 'vue';
import { createRouter, createWebHistory } from 'vue-router';
import App from './App.vue';

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/teams',
      component: () => import('./components/teams/TeamsList.vue'),
    },
    {
      path: '/users',
      component: () => import('./components/users/UsersList.vue'),
    },
  ],
});

const app = createApp(App);
app.use(router);
app.mount('#app');
```

App.vue

```vue
<template>
  <the-navigation @set-page="setActivePage"></the-navigation>
  <main>
    <router-view></router-view>
  </main>
</template>
```

### router-link

router-link 类似于<a>，也会被渲染成<a>，但是他不会重置页面的 state，仅需要填写指向的 path，就可完成

```vue
<li>
  <router-link to="/teams">Teams</router-link>
</li>
```

router-link 中，所在对应 url 时候，会自动多出 router-link-active 和 router-link-exact-active class,因此通过定义这两个 class，我们可以为当前所在的 router-link 添加样式；他们的区别在于，exact-active 要求 navigation item 与当前路径完全匹配，而-active 则是指要求 navigation item 只包含部分当前路径就好

```css
a.router-link-active {
  color: #f1a80a;
  border-color: #f1a80a;
  background-color: #1a037e;
}
```

如果对默认的 router-link-active 和 router-link-exact-active 两个 class 名不满意，可以在 main 的配置里修改

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/teams',
      component: () => import('./components/teams/TeamsList.vue'),
    },
    {
      path: '/users',
      component: () => import('./components/users/UsersList.vue'),
    },
  ],
  linkActiveClass: 'is-active',
});
```

### Navigation

在功能代码中，可以使用$router.push，将页面导向其它地址，并记录在history中，其它还有$router.back(后退), $router.forward（前进）

```js
methods: {
  confirm() {
    this.$router.push('/teams');
  },
},
```

### passing data

this.$route.path ：获取当前路径
this.$route.params：获取当前路径参数，即：后面的内容

可以将 params 拿到的参数放在 created，来更新数据

```vue
<template>
  <section>
    <h2>{{ teamName }}</h2>
    <ul>
      <user-item
        v-for="member in members"
        :key="member.id"
        :name="member.fullName"
        :role="member.role"
      ></user-item>
    </ul>
  </section>
</template>

<script>
import UserItem from '../users/UserItem.vue';

export default {
  inject: ['users', 'teams'],
  components: {
    UserItem,
  },
  data() {
    return {
      teamName: '',
      members: [],
    };
  },
  created() {
    const teamId = this.$route.params.teamId;
    const selectedTeam = this.teams.find((team) => team.id === teamId);
    const members = selectedTeam.members;
    this.members = this.users.filter((user) => members.includes(user.id));
    this.teamName = selectedTeam.name;
  },
};
</script>
```

<router-link>可以代替<a>实现动态 url
例如

```vue
<template>
  <li>
    <h3>{{ name }}</h3>
    <div class="team-members">{{ memberCount }} Members</div>
    <router-link :to="'/teams/' + id">View Members</router-link>
  </li>
</template>

<script>
export default {
  props: ['id', 'name', 'memberCount'],
};
</script>
```

为了让上面更好看，可以使用 computed

```vue
<template>
  <li>
    <h3>{{ name }}</h3>
    <div class="team-members">{{ memberCount }} Members</div>
    <router-link :to="teamMembersLink">View Members</router-link>
  </li>
</template>

<script>
export default {
  props: ['id', 'name', 'memberCount'],
  computed: {
    teamMembersLink() {
      return '/teams/' + this.id;
    },
  },
};
</script>
```

在这里，可以通过返回 object 的方式来替换返回 string

```vue
<script>
export default {
  props: ['id', 'name', 'memberCount'],
  computed: {
    teamMembersLink() {
      return { path: '/teams/' + this.id };
    },
  },
};
</script>
```

即使我们通过 router-link 来更新 url，页面的 component 可能也不会变，但是 component 里的 this.$route 会改变，因此我们可以将 route 加入 watch，来动态通知页面更新

```vue
<template>
  <section>
    <h2>{{ teamName }}</h2>
    <ul>
      <user-item
        v-for="member in members"
        :key="member.id"
        :name="member.fullName"
        :role="member.role"
      ></user-item>
    </ul>
  </section>
</template>

<script>
import UserItem from '../users/UserItem.vue';

export default {
  inject: ['users', 'teams'],
  components: {
    UserItem,
  },
  data() {
    return {
      teamName: '',
      members: [],
    };
  },
  methods: {
    loadTeamMembers(route) {
      const teamId = route.params.teamId;
      const selectedTeam = this.teams.find((team) => team.id === teamId);
      if (!selectedTeam) return;
      const members = selectedTeam.members;
      this.members = this.users.filter((user) => members.includes(user.id));
      this.teamName = selectedTeam.name;
    },
  },
  created() {
    this.loadTeamMembers(this.$route);
  },
  watch: {
    $route(newRoute) {
      this.loadTeamMembers(newRoute);
    },
  },
};
</script>
```

如果想杜绝对 route 的依赖，因为通过$route 的方式来更新组件，会大大降低组件的可复用性，我们可以将 id 以 props 的形式传进来
main.js

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/teams/:teamId',
      component: () => import('./components/teams/TeamMembers.vue'),
      props: true,
    },
  ],
});
```

TeamMembers.vue

```vue
<template>
  <section>
    <h2>{{ teamName }}</h2>
    <ul>
      <user-item
        v-for="member in members"
        :key="member.id"
        :name="member.fullName"
        :role="member.role"
      ></user-item>
    </ul>
  </section>
</template>

<script>
import UserItem from '../users/UserItem.vue';

export default {
  inject: ['users', 'teams'],
  props: ['teamId'],
  components: {
    UserItem,
  },
  data() {
    return {
      teamName: '',
      members: [],
    };
  },
  methods: {
    loadTeamMembers(teamId) {
      const selectedTeam = this.teams.find((team) => team.id === teamId);
      if (!selectedTeam) return;
      console.log(selectedTeam);
      const members = selectedTeam.members;
      this.members = this.users.filter((user) => members.includes(user.id));
      this.teamName = selectedTeam.name;
    },
  },
  created() {
    this.loadTeamMembers(this.teamId);
  },
  watch: {
    teamId(newId) {
      this.loadTeamMembers(newId);
    },
  },
};
</script>
```

### redirect

route 的配置中可以加入 redirect，以重定向一个 url

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams' },
    {
      path: '/teams',
      component: () => import('./components/teams/TeamsList.vue'),
    },
    {
      path: '/users',
      component: () => import('./components/users/UsersList.vue'),
    },
  ],
});
```

或者通过 alias 也能达到此效果

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/teams',
      component: () => import('./components/teams/TeamsList.vue'),
      alias: '/',
    },
  ],
});
```

不同的地方是，redirect 会改变 path，而 alias 不会

想匹配哪些没有对应的路径，可以在 routes 的最后一项里插入

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams' },
    {
      path: '/teams',
      component: () => import('./components/teams/TeamsList.vue'),
    },
    {
      path: '/users',
      component: () => import('./components/users/UsersList.vue'),
    },
    {
      path: '/teams/:teamId',
      component: () => import('./components/teams/TeamMembers.vue'),
      props: true,
    },
    { path: '/:notFound(.*)', redirect: '/teams' },
  ],
});
```

/:notFound(.\*) 意为所有/之后的任何 path，都会被引导到这里，所以这个一般也会放在最后一项，不然会 overwrite 其它所有的 routes

### nested routes

为了使 routes 的格式更清晰，我们可以将部分的 route 从 root 里取出，再作为 children route 放入，比如

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams' },
    {
      path: '/teams',
      component: () => import('./components/teams/TeamsList.vue'),
      children: [
        {
          path: ':teamId',
          component: () => import('./components/teams/TeamMembers.vue'),
          props: true,
        },
      ],
    },
    {
      path: '/users',
      component: () => import('./components/users/UsersList.vue'),
    },
    { path: '/:notFound(.*)', redirect: '/teams' },
  ],
});
```

需要注意的是，这样做以后，需要在对应的组件下再添加 router-view，因为一般情况下 router-view 服务于 rootroutes
TeamsList.vue

```vue
<template>
  <router-view></router-view>
  <ul>
    <teams-item
      v-for="team in teams"
      :key="team.id"
      :id="team.id"
      :name="team.name"
      :member-count="team.members.length"
    ></teams-item>
  </ul>
</template>
```

当 child route 被 active 的时候，对应的 parent route 也将变为 active 状态

### named routes

我们可以为每一个 route 命名，这样在组件内 to 的时候，就可以通过 name，来调取该组件，这样在更改 route 的 path 以后，我们就不需要更新每个使用该 route 的 path 了
main.js

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams' },
    {
      name: 'teams',
      path: '/teams',
      component: () => import('./components/teams/TeamsList.vue'),
      children: [
        {
          name: 'team-members',
          path: ':teamId',
          component: () => import('./components/teams/TeamMembers.vue'),
          props: true,
        },
      ],
    },
    {
      path: '/users',
      component: () => import('./components/users/UsersList.vue'),
    },
    { path: '/:notFound(.*)', redirect: '/teams' },
  ],
});
```

子组件

```vue
<template>
  <li>
    <h3>{{ name }}</h3>
    <div class="team-members">{{ memberCount }} Members</div>
    <router-link :to="teamMembersLink">View Members</router-link>
  </li>
</template>

<script>
export default {
  props: ['id', 'name', 'memberCount'],
  computed: {
    teamMembersLink() {
      // return '/teams/' + this.id;
      // return { path: '/teams/' + this.id };
      return { name: 'team-members', params: { teamId: this.id } };
    },
  },
};
</script>
```

同样 push 也可以使用 name

```js
this.$router.push({ name: 'team-members', params: { teamId: this.id } });
```

### query params

我们不需要在配置 route 的时候配置 query params，只需要在组件使用它的时候，以下面的形式传入就好

```js
return { path: '/teams/' + this.id + '?sort=asc' };
```

或者

```js
return {
  name: 'team-members',
  params: { teamId: this.id },
  query: { sort: 'asc' },
};
```

想解析 query params，我们也只需要使用 this.$route.query.

### multiple routes and named router-view

假如我们想根据 path 的不同，来决定页面上细节部位的变化，比如 footer，在 team 和 user 的不同 path 下，内容也不同，我们可以使用 named router-view 来以类似 slot 的方式完成这个需求
首先，我们在配置 route 时引入不同的 component，并给以 name
main.js

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams' },
    {
      name: 'teams',
      path: '/teams',
      components: {
        default: () => import('./components/teams/TeamsList.vue'),
        footer: () => import('./components/teams/TeamFooter.vue'),
      },
      children: [
        {
          name: 'team-members',
          path: ':teamId',
          component: () => import('./components/teams/TeamMembers.vue'),
          props: true,
        },
      ],
    },
  ],
});
```

然后我们在 component 里的 router-view 加上命名

```vue
<template>
  <the-navigation></the-navigation>
  <main>
    <router-view></router-view>
  </main>
  <footer>
    <router-view name="footer"></router-view>
  </footer>
</template>
```

### scroll behavior

scrollBehavior 方法应该返回一个在页面跳转后，页面所在的位置，而其中的 savedPosition 则记录了在跳转前所在的屏幕位置

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams' },
    ...
  ],
  scrollBehavior(to, from, savedPosition) {
    if (savedPosition) {
      return savedPosition;
    }
    return { left: 0, top: 0 };
  },
});
```

因为用不到 to，from，因此还可以写成

```js
 scrollBehavior(_, _2, savedPosition) {
    if (savedPosition) {
      return savedPosition;
    }
    return { left: 0, top: 0 };
  },
```

### navigation guard

beforeEach

- 该方法在每次页面跳转前执行，其中有三个参数，to，from，next，to 和 from 可以拿到$route 的等效信息，使用 next()表示允许进入下个页面，next(false)则表示不允许，next 内还可以传递要转入的页面地址，比如

```js
import App from './App.vue';

const router = createRouter({
  history: createWebHistory(),
  routes: [
  ...
});

router.beforeEach(function (to, from, next) {
  next();
});

const app = createApp(App);
```

```js
next('/teams');
next({ name: 'team-members', param: { teamId: 't2' } });
```

### beforeEnter

- 该方法还可以插入在 children route 里，表示在导入这个页面前，会运行的 function

```js
 routes: [
    { path: '/', redirect: '/teams' },
    {
      path: '/users',
      component: () => import('./components/users/UsersList.vue'),
      beforeEnter(to, from, next) {},
    },
  ],
```

### beforeRouteEnter

- 相对应的，子组件里也可以插入类似的方法，用法相同

```vue
<script>
import UserItem from './UserItem.vue';

export default {
  components: {
    UserItem,
  },
  inject: ['users'],
  methods: {
    confirm() {
      this.$router.push('/teams');
    },
  },
  beforeRouteEnter(to, from, next) {
    next();
  },
};
</script>
```

以上三处的 navigation guard 的顺序为: global beforeEach -> childe route beforeEnter -> component beforeRouterEnter

- beforeRouteUpdate
  在组件中使用，切仅在路径有变更，而该组件又被复用时使用，例如下面，id 基于路径，因此可以替代 watch

```vue
<script>
import UserItem from '../users/UserItem.vue';

export default {
  inject: ['users', 'teams'],
  props: ['teamId'],
  components: {
    UserItem,
  },
  data() {
    return {
      teamName: '',
      members: [],
    };
  },
  methods: {
    loadTeamMembers(teamId) {
      const selectedTeam = this.teams.find((team) => team.id === teamId);
      if (!selectedTeam) return;
      console.log(selectedTeam);
      const members = selectedTeam.members;
      this.members = this.users.filter((user) => members.includes(user.id));
      this.teamName = selectedTeam.name;
    },
  },
  created() {
    this.loadTeamMembers(this.teamId);
  },
  beforeRouteUpdate(to, from, next) {
    this.loadTeamMembers(to.params.teamId);
    next();
  },
  // watch: {
  //   teamId(newId) {
  //     this.loadTeamMembers(newId);
  //   },
  // },
};
</script>
```

- afterEach
  在 main 里使用，在每次导航完成后执行，无法改变导航，一般用于在页面导航完成后，执行某些功能，例如发送统计信息

```js
router.beforeEach(function (to, from, next) {
  next();
});

router.afterEach(function (to, from) {
  // sending analytics data
});

const app = createApp(App);
```

- beforeRouteLeave
  在 component 内使用，在即将离开该地址（页面）之前，执行的方法，类似于 component unmounted，可以取消地址的导航，优先度高于 beforeEach 和 beforeRouteEnter
  一般用于帮助用户确保输入的 form 信息等有保存

```vue
<script>
export default {
  components: {
    UserItem,
  },
  methods: {
    ...
  },
  beforeRouteEnter(to, from, next) {
    next();
  },
  beforeRouteLeave(to, from, next) {
    if (this.changesSaved) {
      next();
    } else {
      const userWantsToLeave = confirm('Are you sure you want to leave?');
      next(userWantsToLeave);
    }
  },
};
</script>
```

- Route Metadata
  在配置 route 时使用，一旦放入 meta，等于可以在 to，from，$route 中通过.meta 拿到该值，例如
  main.js

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams' },
    {
      name: 'teams',
      path: '/teams',
      meta:{needsAuth:true},
      components: {
        default: () => import('./components/teams/TeamsList.vue'),
        footer: () => import('./components/teams/TeamFooter.vue'),
      },
      children: [
        {
          name: 'team-members',
          path: ':teamId',
          component: () => import('./components/teams/TeamMembers.vue'),
          props: true,
        },
      ],
    },
    ...
  ],
});

router.beforeEach(function (to, from, next) {
  if(to.meta.needsAuth){
    console.log('need auth');
  }
  next();
});
```

### organize files

可以将 main 中的 route 部分全部取出，然后再导入 main 中，做到更好的文件整理
原 main.js

```js
import { createApp } from 'vue';
import { createRouter, createWebHistory } from 'vue-router';
import App from './App.vue';

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams' },
    {
      name: 'teams',
      path: '/teams',
      meta: { needsAuth: true },
      components: {
        default: () => import('./pages/TeamsList.vue'),
        footer: () => import('./components/teams/TeamFooter.vue'),
      },
      children: [
        {
          name: 'team-members',
          path: ':teamId',
          component: () => import('./components/teams/TeamMembers.vue'),
          props: true,
        },
      ],
    },
    {
      path: '/users',
      component: () => import('./pages/UsersList.vue'),
    },
    { path: '/:notFound(.*)', redirect: '/teams' },
  ],
  scrollBehavior(_, _2, savedPosition) {
    if (savedPosition) {
      return savedPosition;
    }
    return { left: 0, top: 0 };
  },
});

router.beforeEach(function (to, from, next) {
  if (to.meta.needsAuth) {
    console.log('need auth');
  }
  next();
});

const app = createApp(App);

app.use(router);

app.mount('#app');
```

新 main.js

```js
import { createApp } from 'vue';
import App from './App.vue';
import router from './router.js';

const app = createApp(App);

app.use(router);

app.mount('#app');
```

router.js

```js
import { createRouter, createWebHistory } from 'vue-router';

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams' },
    {
      name: 'teams',
      path: '/teams',
      meta: { needsAuth: true },
      components: {
        default: () => import('./pages/TeamsList.vue'),
        footer: () => import('./components/teams/TeamFooter.vue'),
      },
      children: [
        {
          name: 'team-members',
          path: ':teamId',
          component: () => import('./components/teams/TeamMembers.vue'),
          props: true,
        },
      ],
    },
    {
      path: '/users',
      component: () => import('./pages/UsersList.vue'),
    },
    { path: '/:notFound(.*)', redirect: '/teams' },
  ],
  scrollBehavior(_, _2, savedPosition) {
    if (savedPosition) {
      return savedPosition;
    }
    return { left: 0, top: 0 };
  },
});

router.beforeEach(function (to, from, next) {
  if (to.meta.needsAuth) {
    console.log('need auth');
  }
  next();
});

export default router;
```

## Section 15: Vuex

local state: 仅在某一个 component 内可以使用的 data
global state： 多个或者所有 component 都可以使用的 data

理论上我们可以依然使用 provide 来实现 global state，但是他有如下问题

- provide 提供的 state，需要在改 component 里（比如 App.vue）定义对应修改方法，而这个方法本身可能只是供其他 component 使用的，会造成 provide 所在的 component 变成 fat component
- data 可能在预期之外发生改变
- 因为忘记更新 state，可能会造成 data 错误

npm install --save vuex

### config vuex

- step 1: import vuex
- step 2: 使用 createStore 方法，配置 state
  - state 的配置写法像极了 component 里的 data
- step 3: 使用 app.use 装载 store
- step 4: 在 子 component 中通过$store.state 来使用
  main.js

```js
import { createApp } from 'vue';
import { createStore } from 'vuex';
import App from './App.vue';

const store = createStore({
  state() {
    return {
      counter: 0,
    };
  },
});

const app = createApp(App);

app.use(store);
app.mount('#app');
```

App.vue

```js
<h3>{{ $store.state.counter }}</h3>

methods: {
    addOne() {
      this.$store.state.counter++;
    },
  }
```

### mutation

为了对 state 的管理更加统一，对 state 更改的方法可以在 createStore 时使用 mutation 注册，这样在其他 component 里就可以直接通过 commit 调用该方法
main.js

```vue
const store = createStore({ state() { return { counter: 0, }; }, mutations: {
increment(state) { state.counter++; }, }, });
```

子组件

```vue
<script>
export default {
  methods: {
    addOne() {
      this.$store.commit('increment');
    },
  },
};
</script>
```

mutation method 可以添加 payload，以供组件调用时传入参数
main

```js
mutations: {
  increase(state, payload) {
    state.counter += payload.value;
  },
},
```

子组件

```js
addOne() {
  this.$store.commit('increase', { value: 10 })
  this.$store.commit({
    type: 'increase',
    value: 10,
  });
},
```

mutations 中的方法必须是同步的，而不能是异步的，因为 state 可能会在其它地方调用，如果异步可能会造成阻塞

### getters

为了保证 component 对 state 中的数据做更统一的访问，我们还可以定义 getters，类似于 computed
main

```js
const store = createStore({
  state() {
    return {
      counter: 0,
    };
  },
  getters: {
    finalCounter(state) {
      return state.counter * 2;
    },
  },
});
```

子组件

```vue
<template>
  <h3>{{ counter }}</h3>
</template>

<script>
export default {
  computed: {
    counter() {
      return this.$store.getters.finalCounter;
    },
  },
};
</script>
```

getters 中的方法，可以互相调用

```js
getters: {
  finalCounter(state) {
    return state.counter * 2;
  },
  normalizedCounter(state, getters) {
    const finalCounter = getters.finalCounter;
    if (finalCounter > 100) return 100;
    return finalCounter;
  },
},
```

对没有使用又必要的参数，可以用\_替换，比如上面的可以写为

```js
getters: {
  finalCounter(state) {
    return state.counter * 2;
  },
  normalizedCounter(_, getters) {
    const finalCounter = getters.finalCounter;
    if (finalCounter > 100) return 100;
    return finalCounter;
  },
},
```

### Actions

为了使 state 的数据可以异步更新，可以使用 action
main

```js
const store = createStore({
  state() {
    return {
      counter: 0,
    };
  },
  mutations: {
    increment(state) {
      state.counter++;
    },
    increase(state, payload) {
      state.counter += payload.value;
    },
  },
  actions: {
    increment(context) {
      setTimeout(function () {
        context.commit('increment');
      }, 2000);
    },

    increase(context, payload) {
      setTimeout(function () {
        context.commit('increase', payload);
      }, 2000);
    },
  },
});
```

子组件

```vue
<script>
export default {
  methods: {
    addOne() {
      this.$store.dispatch('increment');
      this.$store.dispatch({
        type: 'increase',
        value: 10,
      });
    },
  },
};
</script>
```

actions 中的 context 非常强大，可以使用 context.getter, context.dispatch，所以我们是可以在 actions 的方法中相互 dispatch，也可以使用 getters 拿到数据

actions 中不要直接操作 state，而应该是通过 mutation 来达到这个目的

### MapGetter

想减少直接对 store 的访问，我们可以通过 mapGetter 的方法，直接把 getters 中的方法搬过来，其中 mapGetter 中的 Array 参数，即为我们想拿来使用的 getters 方法
main

```js
getters: {
  finalCounter(state) {
    return state.counter * 2;
  },
  normalizedCounter(_, getters) {
    const finalCounter = getters.finalCounter;
    if (finalCounter > 100) return 100;
    return finalCounter;
  },
},
```

子组件

```vue
<template>
  <h3>{{ finalCounter }}</h3>
</template>

<script>
import { mapGetters } from 'vuex';

export default {
  computed: {
    // counter() {
    //   return this.$store.getters.finalCounter;
    // },
    ...mapGetters(['finalCounter']),
  },
};
</script>
```

mapGetters 还可以为导入的方法自定义名称

```js
...mapGetters({
      fc:'finalCounter',
    }),
```

### MapActions

类似于其它 Map Helpers，mapActions 也是可以帮忙直接拿到 actions 里面的方法,具体用法如下
子组件

```vue
<template>
  <button @click="increase({ value: 10 })">Add 10</button>
</template>

<script>
import { mapActions } from 'vuex';

export default {
  methods: {
    // addOne() {
    //   this.$store.dispatch({
    //     type: 'increase',
    //     value: 10,
    //   });
    // },
    ...mapActions(['increase']),
  },
};
</script>
```

mapActions 还可以为导入的方法自定义名称

```js
...mapActions({
      inc:'increment',
    }),
```

### Modules

可以将 store 中的内容拆分管理，然后再次以 modules 的形式导入

```js
const counterModule = {
  state() {
    return {
      counter: 0,
    };
  },
  mutations: {
    increment(state) {
      state.counter = state.counter + 2;
    },
    increase(state, payload) {
      state.counter = state.counter + payload.value;
    },
  },
  actions: {
    increment(context) {
      setTimeout(function () {
        context.commit('increment');
      }, 2000);
    },
    increase(context, payload) {
      console.log(context);
      context.commit('increase', payload);
    },
  },
  getters: {
    finalCounter(state) {
      return state.counter * 3;
    },
    normalizedCounter(_, getters) {
      const finalCounter = getters.finalCounter;
      if (finalCounter < 0) {
        return 0;
      }
      if (finalCounter > 100) {
        return 100;
      }
      return finalCounter;
    },
  },
};

const store = createStore({
  modules: {
    numbers: counterModule,
  },
});
```

module 中的 state 是 local 的，同理的，该 module 内的 mutation，action，getter 方法仅能访问该 module 内的 state 值，无法调用外部的值
但是 getter 可以尝试引入 rootState, rootGetters 的参数，来导入外部的 state

为了更好的管理 module，比如防止 global 里的 mutation 方法与 module 里的 mutation 方法重名，我们可以引入 namespaced，这样任何子组件对 module 中的访问都必须加入 module name
main

```js
const counterModule = {
  namespaced: true,
  state() {
    return {
      counter: 0,
    };
  },
  ...
}
const store = createStore({
  modules: {
    numbers: counterModule,
  },
  ...
}
```

子组件

```vue
<script>
export default {
  computed: {
    counter() {
      return this.$store.getters['numbers/normalizedCounter'];
    },
  },
};
</script>
```

```vue
<script>
import { mapGetters } from 'vuex';

export default {
  methods: {
    addOne() {
      this.$store.dispatch({
        type: 'numbers/increase',
        value: 10,
      });
    },
  },
  computed: {
    ...mapGetters('numbers', ['numbers/finalCounter']),
  },
};
</script>
```

### restrcutre vuex files

在复杂的项目中，为了更好的管理 vuex 文件，我们可以生成 module 文件夹，然后将原来的 store 文件做更好的拆分
原文件

```js
import { createStore } from 'vuex';

const store = createStore({
  modules: {
    numbers: counterModule,
  },
  state() {
    return {
      isLoggedIn: false,
    };
  },
  mutations: {
    setAuth(state, payload) {
      state.isLoggedIn = payload.isAuth;
    },
  },
  actions: {
    login(context) {
      context.commit('setAuth', { isAuth: true });
    },
    logout(context) {
      context.commit('setAuth', { isAuth: false });
    },
  },
  getters: {
    userIsAuthenticated(state) {
      return state.isLoggedIn;
    },
  },
});

export default store;
```

拆分后的文件
store/
index.js

```
import { createStore } from 'vuex';

import rootMutations from './mutations.js';
import rootActions from './actions.js';
import rootGetters from './getters.js';
import counterModule from './modules/counter';

const store = createStore({
  modules: {
    numbers: counterModule,
  },
  state() {
    return {
      isLoggedIn: false,
    };
  },
  mutations: rootMutations,
  actions: rootActions,
  getters: rootGetters,
});

export default store;
```

mutations.js

```js
export default {
  setAuth(state, payload) {
    state.isLoggedIn = payload.isAuth;
  },
};
```

getters.js

```
export default {
  userIsAuthenticated(state) {
    return state.isLoggedIn;
  },
};
```

actions.js

```
export default {
  login(context) {
    context.commit('setAuth', { isAuth: true });
  },
  logout(context) {
    context.commit('setAuth', { isAuth: false });
  },
};
```

modules/counter
index.js

```js
import counterMutations from './mutations.js';
import counterActions from './actions.js';
import counterGetters from './getters.js';

export default {
  state() {
    return {
      counter: 0,
    };
  },
  mutations: counterMutations,
  actions: counterActions,
  getters: counterGetters,
};
```

mutations.js

```js
export default {
  increment(state) {
    state.counter = state.counter + 2;
  },
  increase(state, payload) {
    state.counter = state.counter + payload.value;
  },
};
```

getters.js

```js
export default {
  finalCounter(state) {
    return state.counter * 3;
  },
  normalizedCounter(_, getters) {
    const finalCounter = getters.finalCounter;
    if (finalCounter < 0) {
      return 0;
    }
    if (finalCounter > 100) {
      return 100;
    }
    return finalCounter;
  },
};
```

actions.js

```js
export default {
  increment(context) {
    setTimeout(function () {
      context.commit('increment');
    }, 2000);
  },
  increase(context, payload) {
    console.log(context);
    context.commit('increase', payload);
  },
};
```

main.js

```js
import { createApp } from 'vue';

import App from './App.vue';
import store from './store';

const app = createApp(App);

app.use(store);
app.config.devtools = true;

app.mount('#app');
```
