## Section 2: Queries and Schemas

npm i apollo-server graphql

- step 1: import and configure apollo server

```js
const { ApolloServer } = require('apollo-server');

const server = new ApolloServer();

server.listen().then(({ url }) => {
  console.log('Server is up at ' + url);
});
```

- step 2: 加入 type definition（schema） 和 resolver
  - schema：定义我们需要的数据名称和数据类型
  - resolver：定义数据对应的返回值

```js
const typeDefs = gql`
  type Query {
    hello: String
  }
`;

const resolvers = {
  Query: {
    hello: () => {
      return 'Hello World!';
    },
  },
};

const server = new ApolloServer({
  typeDefs,
  resolvers,
});
```

### Scalar type:

String, Int, Float, Boolean
如果 resolver 里返回的类型与 typeDefs 不同，则一定会报错，但是注意，如果返回 null 则不报错

```js
const typeDefs = gql`
  type Query {
    hello: String
    numberOfAnimals: Int
    price: Float
    isCool: Boolean
  }
`;

const resolvers = {
  Query: {
    hello: () => {
      return 'Hello World!';
    },
    numberOfAnimals: () => {
      return 55;
    },
    price: () => {
      return 3.21;
    },
    isCool: () => {
      return true;
    },
  },
};
```

如果一定要防止返回 null 也能返回，则必须在 typeDef 的类型后面加！，表示返回非空

```js
const typeDefs = gql`
  type Query {
    hello: String!
    numberOfAnimals: Int!
    price: Float
    isCool: Boolean
  }
`;
```

### Array types

```js
const typeDefs = gql`
  type Query {
    helloArray: [String]
  }
`;

const resolvers = {
  Query: {
    helloArray: () => {
      return ['Hello', null, 'what'];
    },
  },
};
```

同理，如果要防止返回非空，要加！，这时候存在 null 即会报错

```js
const typeDefs = gql`
  type Query {
    helloArray: [String!]
  }
`;
```

但是这时候，return 的 array 依然可以为 null，
比如下面就不会报错

```js
const resolvers = {
  Query: {
    helloArray: () => {
      return null;
    },
  },
};
```

这里就需要

```js
const typeDefs = gql`
  type Query {
    helloArray: [String!]!
  }
`;
```

### object type

如果想返回某种 object type，我们首先要在 typeDefs 里定义

```js
const typeDefs = gql`
  type Query {
    hello: String
    products: [Product!]!
  }

  type Product {
    name: String!
    description: String!
    quantity: Int!
    onSale: Boolean!
  }
`;
```

然后在 resolver 中填入定义

```js
const resolvers = {
  Query: {
    hello: () => {
      return 'Hello World!';
    },
    products: () => {
      return [
        {
          name: 'Pen',
          description: 'Ultimate pen',
          quantity: 10,
          onSale: false,
        },
        {
          name: 'JustPen',
          description: 'Common pen',
          quantity: 10,
          onSale: false,
        },
      ];
    },
  },
};
```

值得注意的是，当我们做 query 的时候，如果是 object type，必须定义好我们需要的返回值，比如

```
query {
  products {
    name
    quantity
  }
}
```

### query

#### query argument

首先我们需要在 typeDefs 里定义需要的 argument

```js
const typeDefs = gql`
  type Query {
    hello: String
    products: [Product!]!
    product(id: ID): Product
    categories: [Category!]!
    category(id: ID!): Category
  }

  type Product {
    id: ID!
    name: String!
    description: String!
    quantity: Int!
    price: Float!
    onSale: Boolean!
    image: String
  }

  type Category {
    id: ID!
    name: String!
  }
`;
```

然后我们需要改写 resolver，其中 resolver 可以拿到三个参数 parent, args, context，args 即为通过 query 拿到的参数值

```js
const resolvers = {
  Query: {
    hello: () => {
      return 'Hello World!';
    },
    products: () => {
      return products;
    },
    product: (parent, args, context) => {
      const productId = args.id;
      const product = products.find((product) => product.id === productId);
      if (!product) return null;
      return product;
    },
  },
};
```

```js
const resolvers = {
  Query: {
    hello: () => {
      return 'Hello World!';
    },
    products: () => {
      return products;
    },
    product: (parent, args, context) => {
      const { id } = args;
      return products.find((p) => p.id === id);
    },
    categories: () => {
      return categories;
    },
    category: (parent, args, context) => {
      return categories.find((c) => c.id === args.id);
    },
  },
};
```

最后我们需要在 query 的时候，传入参数

```
query {
  product(id:"6c8bc709-f675-472e-ae84-f8c7d73e99af"){
    name
    quantity
  }
}
```

传入参数的方式，我们还可以写为

```
query($productId: ID!) {
  product(id: $productId) {
    name
    quantity
  }
}
```

### Relating data

#### One to Many

首先我们需要改造 type 的定义，在其中插入关联的数据

```js
 type Category {
    id: ID!
    name: String!
    products: [Product!]!
  }
```

然后我们需要在 resolver 中，单独为对应的 type 增加查询方法

```js
const resolvers = {
  Query: {
    hello: () => {
      return 'Hello World!';
    },
    products: () => {
      return products;
    },
    product: (parent, args, context) => {
      const { id } = args;
      return products.find((p) => p.id === id);
    },
    categories: () => {
      return categories;
    },
    category: (parent, args, context) => {
      return categories.find((c) => c.id === args.id);
    },
  },
  Category: {
    products: (parent, args, context) => {
      return products.filter((p) => p.categoryId === parent.id);
    },
  },
};
```

最后我们的 query 语句可以写为

```
query($categoryId: ID!) {
  category(id: $categoryId) {
    name
    products {
      name
    }
  }
}
```

#### Many to One

首先改变 type 的定义

```js
 type Product {
    id: ID!
    name: String!
    description: String!
    quantity: Int!
    price: Float!
    onSale: Boolean!
    image: String
    categoryId: ID!
    category: Category!
  }
```

然后更新对应的 solver

```js
  Product: {
    category: (parent, args, context) => {
      return categories.find((c) => c.id === parent.categoryId);
    },
  },
```

这里，单独扩充的 resolver 中可以理解为，需要补充所有可能需要的 sub query，比如我们这里可能需要 product 返回 review，则需要更新为

```js
 category: (parent, args, context) => {
    return context.categories.find((c) => c.id === parent.categoryId);
  },
  reviews: ({ id: productId }, args, { reviews }) => {
    return reviews.filter((r) => r.productId === productId);
  },
```

最后更改对应的 query

```
query{
  products{
    name
    category {
      name
    }
  }
}
```

通过这里我们也可以看出，parent 其实指向了对应 parent 层的数据，类似 this

### reorganizing files

原文件

```js
const { ApolloServer, gql } = require('apollo-server');
const { db } = require('./db');

const products = [
  {
    id: '53a0724c-a416-4cac-ae45-bfaedce1f147',
    name: 'Steel Pot',
    description: 'Silver steel pot that is perfect for cooking',
    quantity: 230,
    price: 42.44,
    image: 'img-1',
    onSale: false,
    categoryId: 'c01b1ff4-f894-4ef2-b27a-22aacc2fca70',
  },
  ...
];

let categories = [
  {
    id: 'c01b1ff4-f894-4ef2-b27a-22aacc2fca70',
    name: 'Kitchen',
  },
  ...
];

const reviews = [
  {
    id: 'b22da5d4-6a4b-4db5-8ec3-acc228c36260',
    date: '2021-01-01',
    title: 'This is bad',
    comment: 'when i bought this it broke the stove',
    rating: 1,
    productId: '53a0724c-a416-4cac-ae45-bfaedce1f147',
  },
  ...
];

const typeDefs = gql`
  type Query {
    hello: String
    products: [Product!]!
    product(id: ID): Product
    categories: [Category!]!
    category(id: ID!): Category
  }

  type Product {
    id: ID!
    name: String!
    description: String!
    quantity: Int!
    price: Float!
    onSale: Boolean!
    image: String
    categoryId: ID!
    category: Category!
  }

  type Category {
    id: ID!
    name: String!
    products: [Product!]!
  }
`;

const resolvers = {
  Query: {
    hello: () => {
      return 'Hello World!';
    },
    products: () => {
      return products;
    },
    product: (parent, args, context) => {
      const { id } = args;
      return products.find((p) => p.id === id);
    },
    categories: () => {
      return categories;
    },
    category: (parent, args, context) => {
      return categories.find((c) => c.id === args.id);
    },
  },
  Category: {
    products: (parent, args, context) => {
      return products.filter((p) => p.categoryId === parent.id);
    },
  },
  Product: {
    category: (parent, args, context) => {
      return categories.find((c) => c.id === parent.categoryId);
    },
  },
};

const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: {
    db,
  },
});

server.listen().then(({ url }) => {
  console.log('Server is up at ' + url);
});

```

移动 typeDefs
schema.js

```js
const { gql } = require('apollo-server');

exports.typeDefs = gql`
  type Query {
    hello: String
    products: [Product!]!
    product(id: ID): Product
    categories: [Category!]!
    category(id: ID!): Category
  }

  type Product {
    id: ID!
    name: String!
    description: String!
    quantity: Int!
    price: Float!
    onSale: Boolean!
    image: String
    categoryId: ID!
    category: Category!
  }

  type Category {
    id: ID!
    name: String!
    products: [Product!]!
  }
`;
```

移动所有数据
db.js

```js
const products = [
  {
    id: '53a0724c-a416-4cac-ae45-bfaedce1f147',
    name: 'Steel Pot',
    description: 'Silver steel pot that is perfect for cooking',
    quantity: 230,
    price: 42.44,
    image: 'img-1',
    onSale: false,
    categoryId: 'c01b1ff4-f894-4ef2-b27a-22aacc2fca70',
  },
  ...
];

let categories = [
  {
    id: 'c01b1ff4-f894-4ef2-b27a-22aacc2fca70',
    name: 'Kitchen',
  },
  ...
];

module.exports = {
  products,
  categories,
};
```

对于 resolver，我们需要单独建立文件夹，然后对每一个 resolver 对象以单独文件的形式进行分割
resolvers/Category.js

```js
const { products } = require('../db');

exports.Category = {
  products: (parent, args, context) => {
    return products.filter((p) => p.categoryId === parent.id);
  },
};
```

resolvers/Product.js

```js
const { categories } = require('../db');

exports.Product = {
  category: (parent, args, context) => {
    return categories.find((c) => c.id === parent.categoryId);
  },
};
```

resolvers/Query.js

```js
const { products, categories } = require('../db');

exports.Query = {
  hello: () => {
    return 'Hello World!';
  },
  products: () => {
    return products;
  },
  product: (parent, args, context) => {
    const { id } = args;
    return products.find((p) => p.id === id);
  },
  categories: () => {
    return categories;
  },
  category: (parent, args, context) => {
    return categories.find((c) => c.id === args.id);
  },
};
```

更新
index.js

```js
const { ApolloServer } = require('apollo-server');
const { typeDefs } = require('./schema');
const { Query } = require('./resolvers/Query');
const { Product } = require('./resolvers/Product');
const { Category } = require('./resolvers/Category');

const server = new ApolloServer({
  typeDefs,
  resolvers: {
    Query,
    Product,
    Category,
  },
});

server.listen().then(({ url }) => {
  console.log('Server is up at ' + url);
});
```

### context param

我们可以将在 resolver 中反复使用的数据，以 context 的形式定义好，然后在 resolver 中分别拿出使用
index.js

```js
const { products, categories } = require('./db');

const server = new ApolloServer({
  typeDefs,
  resolvers: {
    Query,
    Product,
    Category,
  },
  context: {
    products,
    categories,
  },
});
```

resolvers/Product.js

```js
exports.Product = {
  category: (parent, args, context) => {
    return context.categories.find((c) => c.id === parent.categoryId);
  },
};
```

resolvers/Query.js

```js
exports.Query = {
  hello: () => 'Hello World!',
  products: (parent, args, { products }) => products,
  product: (parent, { id }, { products }) => products.find((p) => p.id === id),
  categories: (parent, args, { categories }) => categories,
  category: (parent, { id }, { categories }) =>
    categories.find((c) => c.id === id),
};
```
