## Section 2: GraphQL Basics: Schemas and Queries

https://graphql-demo.mead.io/

### 一般 query

GraphQL 中三个基本的 operation，第一个就是 query

```
query {
  course
  courseInstructor
}
```

GraphQL 是 self documenting 的，对于没有定义的 field，query 的时候会出现错误提示; RestAPI 需要人工来更新 documentation

### nested query

当我们 query 一个 object 的时候，我们要 specify 具体我们需要哪些 field

```
query {
  course
  courseInstructor
  me {
    id
    name
    email
  }
}
```

当我们 query 一个 array 的时候，同理

```
query {
  users{
    name
  }
}
```

### schema 的 type 定义

在 schema 的 type 定义时，如果 field 类型后面没有跟！表示可能没有返回（该 field 为 optional）；如果有！表示一定有返回（该 field 为 required），

```
  type User {
    id: ID!
    name: String!
    email: String!
  }
```

### query 的定义

定义返回类型为 array

```
// 第一个！表示array里的类型一定是User，第二个！表示返回类型一定是array
users:[User!]!
```

定义返回类型为 object

```
me:User!
```

### setup babel

shift + command + p: 展示所有的命令
control+ ~：打开 terminal

```
npm install babel-cli@6.26 babel-preset-env@1.7
```

### import and export module

#### named export

在 js 文件中通过 export {...} 的形式，把 js 文件中的内容导出出去, ...中的内容需要有具体的名字

```js
const message = 'some message';
const name = 'myModule';

export { message, name };
```

#### default export

在 js 文件中通过 export {...} 导出的同时，在导出的名跟上 as default

```js
const location = 'Sydney';

export { message, name, location as default };
```

#### import

import named export:

```js
import { message, name } from './myModule';
```

import default export:

```js
import myLocation from './myModule';
```

#### 相对路径

./ 表示当前文件夹下

### Create GraphQL API

```
npm i graphql-yoga@1.16.7
```

一个 graphQL 的 api，必须包含 Type definitions(Application Schema) 和 Resolvers

#### Type definitions(Application Schema)

定义了可以执行的 operation，和其中的数据及其类型
比如 定义 Query，我们可以在其中定义 query 的名字，和返回的数据类型

```js
const typeDefs = `
  type Query {
    hello: String!
  }
`;
```

#### Resolvers

定义了 Type definitions 里定义的 operation 所对应的 function
一般 Resolvers 的 structure 会对应 type definitions 里 query 的 structure

```js
// Type definitions(Application Schema)
const typeDefs = `
  type Query {
    hello: String!
    name: String!
    location: String!
    bio: String!
  }
`;
// Resolvers
const resolvers = {
  Query: {
    hello() {
      return 'Hello!';
    },
    name() {
      return 'GraphQL API';
    },
    location() {
      return 'Sydney';
    },
    bio() {
      return 'Live in Oatley';
    },
  },
};
```

##### 小技巧

object 里名字和传入的值相等时，可以简写到只用名字

```js
const server = new GraphQLServer({
  typeDefs: typeDefs,
  resolvers: resolvers,
});
```

等同于

```js
const server = new GraphQLServer({
  typeDefs,
  resolvers,
});
```

### GraphQL Scalar Types

graphQL 中的基本类型：String, Boolean, Int, Float, ID
其他的类型比如自定义对象或者 array，均是由上面 5 种基本类型组成

```js
// Scalar types: String, Boolean, Int, Float, ID

// Type definitions(Application Schema)
const typeDefs = `
  type Query {
    id: ID!
    name: String!
    age: Int!
    employed: Boolean!
    gpa: Float
  }
`;
// Resolvers
const resolvers = {
  Query: {
    id() {
      return '64c1fed231';
    },
    name() {
      return 'Tom';
    },
    age() {
      return 27;
    },
    employed() {
      return true;
    },
    gpa() {
      return 3.1;
    },
  },
};
```

### GraphQL Custom Types

首先由 type name 开始，做 type definition

```js
  type User {
    id: ID!
    name: String!
    email: String!
    age: Int
  }
```

然后添加进 query definition，

```js
const typeDefs = `
  type Query {
    me: User!
  }

  type User {
    id: ID!
    name: String!
    email: String!
    age: Int
  }
`;
```

最后添加 resolver

```js
const resolvers = {
  Query: {
    me() {
      return {
        id: '64x12edc',
        name: 'Tom',
        email: 'Tom@demo.com',
        age: 22,
      };
    },
  },
};
```

### Operation Argument

首先在 query 的定义里加入 argument，argument 依然可以用！来代表为 required

```js
const typeDefs = `
  type Query {
    greeting(name: String): String!
    post: Post!
  }

  type Post {
    id: ID!
    title: String!
    body: String!
    published: Boolean!
  }
`;
```

然后在 resolver 中定义 function，在 function 有四个 argument

- parent
- args
- context->ctx
- info
  一般我们想从传入的参数中拿数据，需要用到 args

```js
const resolvers = {
  Query: {
    greeting(parent, args, ctx, info) {
      if (args.name) {
        return `Hello, ${args.name}`;
      } else {
        return 'Hello';
      }
    },
    post() {
      return {
        id: '64x12edc',
        title: 'Tom is here',
        body: 'I just found Tom',
        published: true,
      };
    },
  },
};
```

最后，我们在 client 的 query 中可以加入参数

```js
query {
  greeting(name:"K")
  post {
    id
    title
    published
  }
}
```

### Working with Array

当 Array 为 scalar 类型时

- query 定义时，仅需要在 type 外加[]，例如

```js
const typeDefs = `
  type Query {
    grades: [Int!]!
  }
`;
```

- 作为 query 的参数时，同样，只需要加[]

```js
const typeDefs = `
  type Query {
    grades: [Int!]!
    add(numbers: [Float!]!): Float!
  }
`;

const resolvers = {
  Query: {
    grades(parent, args, ctx, info) {
      return [99, 98, 88];
    },
    add(parent, args, ctx, info) {
      if (args.numbers.length === 0) {
        return 0;
      }
      return args.numbers.reduce(
        (accumulator, currentValue) => accumulator + currentValue
      );
    },
  },
};
```

- 在 client 上做 query 的时候，并不需要规定返回的 field

```
query {
  grades
  add(numbers:[22,12,10])
}
```

#### 复习： reduce method

reduce 可以遍历 array 的同时，压缩里面的数值，它具有两个参数：accumulator, currentValue，在第一次遍历的时候，accumulator 指向 index 为 0 的值，currentValue 指向 index 为 1 的值，此时，回调函数的值将重新赋给下一轮的 accumulator，同时第二次遍历的时候，currentValue 指向 index 为 2 的值，依次类推，当想压缩一个数组求和时，可以用

```js
numbers.reduce((accumulator, currentValue) => accumulator + currentValue);
```

当 Array 为 自定义类型时

- query 定义时，仅需要在 type 外加[]，例如

```js
const typeDefs = `
  type Query {
    users:[User!]!
  }

  type User {
    id: ID!
    name: String!
    email: String!
    age: Int
  }
`;
```

- 在 client 上做 query 的时候，需要规定返回的 field

```
query {
  users{
    id
    name
    email
  }
}
```

同时，在 client 上做 query 的时候，也可以加入参数例如

```
query {
  users(query:"om"){
    id
    name
    email
  }
}
```

对应的 api 如下

```js
const typeDefs = `
  type Query {
    users(query:String):[User!]!
  }

  type User {
    id: ID!
    name: String!
    email: String!
    age: Int
  }
`;

const resolvers = {
  Query: {
    users(parent, args, ctx, info) {
      if (!args.query) return users;
      return users.filter((user) =>
        user.name.toLowerCase().includes(args.query.toLowerCase())
      );
    },
};
```

#### 复习：array filter

遍历所有 array 中的对象，仅仅返回 callback 里条件为真的对象的集合，例如遍历一个 array，仅返回包含目标字段的对象集合

```js
users.filter((user) =>
  user.name.toLowerCase().includes(args.query.toLowerCase());
);
```

### Relational Data

首先，在 type definition 里加入关联的 field

```js
const typeDefs = `
  type Query {
    users(query:String):[User!]!
    posts(query:String):[Post!]!
  }

  type User {
    id: ID!
    name: String!
    email: String!
    age: Int
    posts: [Post!]!
  }

  type Post {
    id: ID!
    title: String!
    body: String!
    published: Boolean!
    author: User!
  }
`;
```

然后，修改相关的数据，当然，有时候如果一方的数据已经定义了关系，也可以不用添加，比如，user 中并没有添加 posts，所以，数据不一定跟 type definition 是 100%的对应关系

```js
const users = [
  {
    id: '64x12001',
    name: 'Tom',
    email: 'Tom@demo.com',
    age: 22,
  },
  {
    id: '64x12002',
    name: 'Rom',
    email: 'Rom@demo.com',
    age: 38,
  },
  {
    id: '64x12003',
    name: 'Mike',
    email: 'Mike@demo.com',
    age: 12,
  },
];

const posts = [
  {
    id: '64x12edd',
    title: 'Tom is here',
    body: 'I just found Tom',
    published: true,
    author: '64x12001',
  },
  {
    id: '64x12ede',
    title: 'Rom is dancing',
    body: 'I just saw Rom dancing',
    published: true,
    author: '64x12001',
  },
  {
    id: '64x12edf',
    title: 'Mike go home',
    body: 'Where is Mike',
    published: true,
    author: '64x12003',
  },
];
```

最后，在 resolver 中添加 function 来告诉 graphQL，怎么找到 post 中的 author（User）

- graphQL 在处理 type definition 里面的非 scalar type 的 field 时候，它会自动寻找 resolver 里，Query 之外关于该 field 的函数，同时对函数自动将原本的 scalar 对象作为 parent 传入
- 例如在下面这个例子里，Post 中除了 author 都是 scalar type，因此当到了 author 时，它会带着 post 中 scalar 的数据作为 parent 找到下面的 Post

```js
const resolvers = {
  Query: {
    users(parent, args, ctx, info) {
      if (!args.query) return users;
      return users.filter((user) =>
        user.name.toLowerCase().includes(args.query.toLowerCase())
      );
    },
    posts(parent, args, ctx, info) {
      if (!args.query) return posts;
      return posts.filter(
        (post) =>
          post.body.toLowerCase().includes(args.query.toLowerCase()) ||
          post.title.toLowerCase().includes(args.query.toLowerCase())
      );
    },
  },
  Post: {
    author(parent, args, ctx, info) {
      return users.find((user) => user.id === parent.author);
    },
  },
  User: {
    posts(parent, args, ctx, info) {
      return posts.filter((post) => {
        return post.author === parent.id;
      });
    },
  },
};
```

#### 复习 find

遍历 array，自动将 callback 第一次匹配的值返回

```js
users.find((user) => user.id === parent.author);
```
