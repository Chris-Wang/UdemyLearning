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

## Section 3: GraphQL Basics: Mutations

### Create Mutation

首先，在 Schema 中定义 mutation，并决定所需要的参数和返回类型

```js
  type Mutation {
    createUser(name: String!, email: String!, age: Int):User!
  }
```

然后，在 Resolver 中添加对应的函数

```js
  Mutation: {
    createUser(parent, args, ctx, info) {
      const emailTaken = users.some((user) => user.email === args.email);
      if (emailTaken) {
        throw new Error('Email taken');
      }
      const user = {
        id: uuidv4(),
        name: args.name,
        email: args.email,
        age: args.age,
      };

      users.push(user);

      return user;
    },
  },
```

最后，在 client 调用 mutation

```
mutation{
  createUser(
    name:"Jack",
    email:"jack@ma2.com",
    age:50){
    id
    name
    email
    age
    posts{
      id
      title
    }
  }
}
```

#### uuid

npm i uuid@3.3.2

#### 复习 some

array 中有对象满足 callback 条件时候，返回为 true；没有满足 callback 条件的，返回为 false

```js
const emailTaken = users.some((user) => user.email === args.email);
```

#### babel-plugin-transform-object-rest-spread

npm i babel-plugin-transform-object-rest-spread

#### input type

在 schema 中，我们还可以定义 input，来取代 mutation 中的 argument list；注意，argument 只能由 input 类型取代，input 类型定义中，必须全部是 scalar type； 同时对应的 resolver args 也必须加 data

原本

```js
  type Mutation {
    createUser(name: String!, email: String!, age: Int):User!
    createPost(title: String!, body: String!, published:Boolean!, author: ID!):Post!
    createComment(text: String!, author: ID!, post: ID!): Comment!
  }
```

加入 input 后

```js
  type Mutation {
    createUser(data: CreateUserInput):User!
    createPost(title: String!, body: String!, published:Boolean!, author: ID!):Post!
    createComment(text: String!, author: ID!, post: ID!): Comment!
  }

  input CreateUserInput {
    name: String!
    email:String!
    age: Int
  }
```

```js
createUser(parent, args, ctx, info) {
      const emailTaken = users.some((user) => user.email === args.data.email);
      if (emailTaken) {
        throw new Error('Email taken');
      }
      const user = {
        id: uuidv4(),
        ...args.data,
      };

      users.push(user);

      return user;
    },
```

client 的 query 也需要更新

```
mutation{
  createUser(
    data:{
      name:"Jack",
    	email:"jack@ma2.com",
    	age:50
    	}
    ){
    id
    name
    email
    age
    posts{
      id
      title
    }
  }
}

```

### Delete mutation

首先，定义该 mutation

```js
  type Mutation {
    createUser(data: CreateUserInput!):User!
    deleteUser(id: ID!):User!
  }
```

然后，添加对应的 resolver

```js
    deleteUser(parent, args, ctx, info) {
      const userIndex = users.findIndex((user) => user.id === args.id);
      if (userIndex === -1) {
        throw new Error('User does not exist');
      }

      const deletedUsers = users.splice(userIndex, 1);

      posts = posts.filter((post) => {
        const match = post.author === args.id;
        if (match) {
          comments = comments.filter((comment) => comment.post !== post.id);
        }
        return !match;
      });
      comments = comments.filter((comment) => comment.author !== args.id);

      return deletedUsers[0];
    },
```

最后，通过 client 来验证数据

```
mutation{
  deleteUser(id:"64x12001"){
    id
    name
  }
}
```

##### 复习 findIndex

跟 find 类似，只不过当不存在满足条件的对象时，该函数的返回值为-1，如果找到满足条件的对象，则返回值为该对象的 index

```js
const commentIndex = db.comments.findIndex((comment) => comment.id === args.id);
if (commentIndex === -1) {
  throw new Error('Comment does not exist');
}
```

#### 复习 splice

对指定数组进行删除操作，splice 传入的两个参数，第一个是开始 remove 的 element index，第二个是想要 remove 的 element 数量, 该函数返回的是被删除的元素集合

```js
const deletedUsers = users.splice(userIndex, 1);
```

### Better project structure

1. 建立 schema.graphql 来存放 type definition
2. 修改 graphQLServer 里的 typeDefs

```js
const server = new GraphQLServer({
  typeDefs: './src/schema.graphql',
  resolvers,
});
```

更新 nodemon script

```
"start": "nodemon src/index.js --ext js,graphql --exec babel-node ",
```

3. 将数据 迁移到 db.js

```js
// Demo users
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

// Demo posts
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

// Demo comments
const comments = [
  {
    id: '64x12f01',
    text: 'I dont know Tom',
    author: '64x12002',
    post: '64x12edd',
  },
  {
    id: '64x12f02',
    text: 'I dont know Rom',
    author: '64x12001',
    post: '64x12ede',
  },
  {
    id: '64x12f03',
    text: 'I dont know Mike',
    author: '64x12001',
    post: '64x12edf',
  },
  {
    id: '64x12f04',
    text: 'I saw him too!',
    author: '64x12003',
    post: '64x12ede',
  },
];

const db = {
  users,
  posts,
  comments,
};

export { db as default };
```

4. 将 db 加入 context，然后可以在所有的 resolver 中通过 context 进行调用

```js
const server = new GraphQLServer({
  typeDefs: './src/schema.graphql',
  resolvers,
  context: {
    db,
  },
});
```

```js
const resolvers = {
  Query: {
    users(parent, args, { db }, info) {
      if (!args.query) return db.users;
      return db.users.filter((user) =>
        user.name.toLowerCase().includes(args.query.toLowerCase())
      );
    },
  },
};
```

5. 建立 resolver 文件夹，并建立 Query.js, Mutation.js, User.js, Post.js, Comment.js,
6. 分别把 index 中对应的 resolver 部分导入到对应的 resolver 文件中

```js
//Query.js
const Query = {
  users(parent, args, { db }, info) {
    if (!args.query) return db.users;
    return db.users.filter((user) =>
      user.name.toLowerCase().includes(args.query.toLowerCase())
    );
  },
  posts(parent, args, { db }, info) {
    if (!args.query) return db.posts;
    return db.posts.filter(
      (post) =>
        post.body.toLowerCase().includes(args.query.toLowerCase()) ||
        post.title.toLowerCase().includes(args.query.toLowerCase())
    );
  },
  comments(parent, args, { db }, info) {
    return db.comments;
  },
};

export { Query as default };
```

7. 在 index 中 import resolver 文件，并且导入 resolver

```js
//index.js
import { GraphQLServer } from 'graphql-yoga';
import db from './db';
import Query from './resolvers/Query';
import Mutation from './resolvers/Mutation';
import User from './resolvers/User';
import Post from './resolvers/Post';
import Comment from './resolvers/Comment';

const server = new GraphQLServer({
  typeDefs: './src/schema.graphql',
  resolvers: {
    Query,
    Mutation,
    User,
    Post,
    Comment,
  },
  context: {
    db,
  },
});
```

### Update mutation

首先，在 schema 中定义 mutation，和 input

```js
type Mutation {
  createUser(data: CreateUserInput!): User!
  deleteUser(id: ID!): User!
  updateUser(id: ID!, data:UpdateUserInput!):User!
}

input UpdateUserInput {
  name: String
  email: String
  age: Int
}
```

完成对应的 resolver

```js
updateUser(parent, args, { db }, info) {
    const user = db.users.find((user) => user.id === args.id);
    if (!user) {
      throw new Error('User does not exist');
    }
    if (typeof args.data.email === 'string') {
      const emailTaken = db.users.some(
        (user) => user.email === args.data.email
      );
      if (emailTaken) {
        throw new Error('Email taken');
      }
      user.email = args.data.email;
    }
    if (typeof args.data.name === 'string') user.name = args.data.name;
    if (typeof args.data.age !== 'undefined') user.age = args.data.age;

    return user;
  },
```

最后，使用 client 进行测试

```
mutation{
  updateUser(id:"64x12001",data:{
    name:"Jason"
  }){
    id
    name
    email
    age
  }
}
```

## Section 4: GraphQL Basics: Subscriptions

subscription 可以令 client 的 ui 跟实际的数据保持同步
首先，添加 subscription 的定义

```js
type Subscription {
  count: Int!
  comment(postId: ID!): Comment!
}
```

然后，添加对应的 resolver, 创建 Subscription.js,
resolver 中添加的不是函数，而是一个对象，对象中包含了 subscribe 方法，即每次 subscribe 时会执行的方法；subscribe 返回的是 pubsub 的 asyncIterator，其中 asyncIterator 只有一个参数，这个参数叫做 channel name
pubsub 还有 publish 的方法，其中两个参数，第一个为 channel name，第二个为一个 object，里面包含了我们想要 publish 的数据，publish 的方法也可以在其它的 mutation method 中出现

```js
const Subscription = {
  count: {
    subscribe(parent, args, { pubsub }, info) {
      let count = 0;

      setInterval(() => {
        count++;
        pubsub.publish('count', {
          count,
        });
      }, 1000);

      return pubsub.asyncIterator('count');
    },
  },
  comment: {
    subscribe(parent, { postId }, { db, pubsub }, info) {
      const post = db.posts.find(
        (post) => post.id === postId && post.published
      );
      if (!post) throw new Error('Post does not exist');

      return pubsub.asyncIterator(`comment in post ${postId}`);
    },
  },
};

export { Subscription as default };
```

```js
//Mutation.js
 createComment(parent, args, { db, pubsub }, info) {
    const userExists = db.users.some((user) => user.id === args.data.author);
    const postExists = db.posts.some(
      (post) => post.id === args.data.post && post.published
    );
    if (!userExists || !postExists) {
      throw new Error('User or post does not exist');
    }
    const comment = {
      id: uuidv4(),
      ...args.data,
    };

    db.comments.push(comment);
    pubsub.publish(`comment in post ${args.data.post}`, {
      comment,
    });
    return comment;
  },
```

接着，更新 index.js，导入 resolver，已经引入 pubsub，并将 pubsub 添加到 context 中

```js
import { GraphQLServer, PubSub } from 'graphql-yoga';

const pubsub = new PubSub();

const server = new GraphQLServer({
  typeDefs: './src/schema.graphql',
  resolvers: {
    Query,
    Mutation,
    Subscription,
    User,
    Post,
    Comment,
  },
  context: {
    db,
    pubsub,
  },
});
```

最后，在 client 上进行测试

```
subscription {
  comment(postId:"64x12ede"){
    id
    text
    author{
      name
    }
  }
}
```

### Expanding mutation subscription

首先，因为我们要清楚的让 client 知道，返回的数据是 create/update/delete 对应产生的，因此需要增加新的 type

```
type Subscription {
  count: Int!
  comment(postId: ID!): Comment!
  post: PostSubscriptionPayload!
}

type PostSubscriptionPayload {
  mutation: String!
  data: Post!
}
```

然后，在对应的 mutation resolver 中添加 publish

```js
//Mutation.js
createPost(parent, args, { db, pubsub }, info) {
    const userExists = db.users.some((user) => user.id === args.data.author);
    if (!userExists) {
      throw new Error('User does not exist');
    }
    const post = {
      id: uuidv4(),
      ...args.data,
    };

    db.posts.push(post);
    if (args.data.published) {
      pubsub.publish('post', {
        post: {
          mutation: 'CREATED',
          data: post,
        },
      });
    }
    return post;
  },
  updatePost(parent, args, { db, pubsub }, info) {
    const { id, data } = args;
    const post = db.posts.find((post) => post.id === id);
    const originalPost = { ...post };
    if (!post) throw new Error('Post does not exist');
    if (typeof data.title === 'string') post.title = data.title;
    if (typeof data.body === 'string') post.body = data.body;
    if (typeof data.published === 'boolean') {
      post.published = data.published;
      if (originalPost.published && !post.published) {
        //delete
        pubsub.publish('post', {
          post: {
            mutation: 'DELETED',
            data: originalPost,
          },
        });
      } else if (!originalPost.published && post.published) {
        //create
        pubsub.publish('post', {
          post: {
            mutation: 'CREATED',
            data: post,
          },
        });
      }
    } else if (post.published) {
      //updated
      pubsub.publish('post', {
        post: {
          mutation: 'UPDATED',
          data: post,
        },
      });
    }
    return post;
  },
  deletePost(parent, args, { db, pubsub }, info) {
    const postIndex = db.posts.findIndex((post) => post.id === args.id);
    if (postIndex === -1) {
      throw new Error('Post does not exist');
    }
    const [post] = db.posts.splice(postIndex, 1);
    db.comments = db.comments.filter((comment) => comment.post !== args.id);
    if (post.published) {
      pubsub.publish('post', {
        post: {
          mutation: 'DELETED',
          data: post,
        },
      });
    }
    return post;
  },

```

#### 复习 []

类似于解构，我们可以直接将 array 中的数据取出，然后对他进行命名，比如 const [a,b,c,d] = [1,2,3,4], 则 a=1, b=2, c=3, d=4

```js
const [post] = db.posts.splice(postIndex, 1);
```

#### 浅拷贝

```js
const originalPost = { ...post };
```

### Enums

1. 可以定义一堆常量
   - UserRole: standard, editor, admin

```
enum UserRole {
  STANDARD
  EDITOR
  ADMIN
}
enum MutationType {
  CREATED
  UPDATED
  DELETED
}
```

2. 同样可以在 schema 中，作为返回类型使用

```
type User {
  role: UserRole!
}
type PostSubscriptionPayload {
  mutation: MutationType!
  data: Post!
}
```

3. 对应的 field value 一定是 enums 中的其中一个常量
