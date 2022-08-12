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
