## Setup
"E:\Program Files\MongoDB\Server\5.0\bin\mongo.exe"  --dbpath="D:\Server\MongoDB\db"
 .\mongod.exe
 .\mongo.exe

## Section 4: A Quick ES6 Refresher
### Promise State
- unresolved
  - waiting for something to finish
- resolved 
  - something finished and all went ok
- rejected
  - something finished and went bad

## Section 5: Core Fundamentals of MongoDB

Mongoose: ODM Object Data Mapping
MongoDB: 
- one instance can have multiple databases
- each database can have multiple collections(类似于表)

## Section 6: A Test Driven Experience
npm init
npm install --save mocha nodemon mongoose
### connect to database
```js
mongoose.connect("mongodb://localhost/users_test");
mongoose.connection
  .once("open", () => console.log("Good to go!"))
  .on("error", (error) => {
    console.log("warning", error);
  });
```
once表示监听mongo产生的'open'事件，成功捕捉到一次则执行回调；
on表示监听'error'事件，成功捕捉到一次则执行回调

### make a model
node test/test_helper.js

```js
const UserSchema = new Schema({
  name: String
});

const User = mongoose.model('user',UserSchema);
```
1. Create a schema called UserSchema
2. Create a collection called 'user', and add UserSchema to it, we call 'User' an 'User model'   
In summary:
```js
//import statement
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

//create schema
const UserSchema = new Schema({
  name: String,
});

//create model
const User = mongoose.model("user", UserSchema);

//export model
module.exports = User;
```
### User Mocha
```js
const assert = require("assert");

//Top leve describe function
//first argument: string we are about to write, it modify the feedback from mocha
//seccond argument: function
describe("Creating records", () => {
  it("save a user", () => {
    assert(1 + 1 === 2);
  });
});

```

it:key to Mocha, tells Mocha to run a test function
assertion: this value eqauls to this value

```js
//package.json
 "scripts": {
    "test": "mocha"
  },
```
npm run test

### Create Test

1. Import User Model
```js
const User = require("../src/user");
```
2. Create a new User instance
```js
const joe = new User({name:'Joe'});
```
3. Save instance to Mongo
```js
joe.save();
```
4. Drop Collectons/Empty Database before and after run the test
- using "done" callback for async drop collection
```js
beforeEach((done) => {
  mongoose.connection.collections.users.drop(() => {
    //Ready to rn the next test!
    done();
  });
});
```
5. Add assertion
```js
 joe.save().
    then(() => {
      assert(!joe.isNew);
    });
```
- Add done to test, tell Mocha move to the next test
一旦引入done，一定要确保使用过它
```js
  it("save a user", (done) => {
    const joe = new User({ name: "Joe" });

    joe.save().then(() => {
      assert(!joe.isNew);
      done();
    });
  });
```

### before vs beforeEach
- before 对整个测试过程，仅执行一次
```js
//test_helper.js
const mongoose = require("mongoose");
mongoose.Promise = global.Promise;

before((done) => {
  mongoose.connect("mongodb://localhost/users_test");
  mongoose.connection
    .once("open", () => {
      done();
    })
    .on("error", (error) => {
      console.log("warning", error);
    });
});

beforeEach((done) => {
  mongoose.connection.collections.users.drop(() => {
    //Ready to rn the next test!
    done();
  });
});
```
### Find Test
1. Setup
```js
const assert = require("assert");
const User = require("../src/user");
describe('Reading users out of the database', () => {

  let joe;
  beforeEach((done) => {
    joe = new User({name: 'Joe'});
    joe.save().then(() => done());
  });

  it('finds all users with a name of joe',()=>{

  })
});
```
#### find vs findOne
- User.find(criteria): find all the users that match the given criteria, return an array
- User.findOne(criteria): find the first user that match the given criteria, return a single record

2. Find user using User model
```js
 it("finds all users with a name of joe", (done) => {
    User.find({ name: "Joe" }).then((users) => {
      console.log(users);
      done();
    });
  });
```
#### _id property
Mongo内自动生成的_id，它的value是ObjectId("613959bb8458ecd3b92c463b"),因此需要拿到值或者比较id的时候，需要_id.toString()

3. 使用 find 完成查询
```js
it("finds all users with a name of joe", (done) => {
    User.find({ name: "Joe" }).then((users) => {
      assert(users[0]._id.toString() === joe._id.toString());
      done();
    });
  });
```
### Nodemon
"test": "nodemon --exec mocha -R min"
- 每次有文件变动，nodemon都会执行mocha -R min

### FindOne Record
```js
it("find a user with a particular id", (done) => {
    User.findOne({ _id: joe._id }).then((user) => {
      assert(user.name === "Joe");
      done();
    });
  });
```

### Delete Record
For Model Class: 'User'
- remove(depreciated)
- deleteOne
```js
  it("model instance remove", (done) => {
    joe
      .remove()
      .then(() => User.findOne({ name: "Joe" }))
      .then((user) => {
        assert(user === null);
        done();
      });
  });
```
- findOneAndRemove
```js
  it("class method findAndRemove", (done) => {
    User.findOneAndDelete({ name: "Joe" })
      .then(() => User.findOne({ name: "Joe" }))
      .then((user) => {
        assert(user === null);
        done();
      });
  });
```
- findByIdAndRemove
```js
 it("class method findByIdAndRemove", (done) => {
    User.findByIdAndRemove(joe._id)
      .then(() => User.findOne({ name: "Joe" }))
      .then((user) => {
        assert(user === null);
        done();
      });
  });
```
For Model Instance: 'joe'
- remove
```js
it("model instance remove", (done) => {
    joe
      .remove()
      .then(() => User.findOne({ name: "Joe" }))
      .then((user) => {
        assert(user === null);
        done();
      });
  });
```

### Update Record
For Model Class: 'User'
- updateOne
updateOne中，放入的第一个变量是查询条件，第二个变量是对查询结果的更改
```js
assertId(User.updateOne({ name: "Joe" }, { name: "Alex" }), done);
```
- findOneAndUpdate
```js
 it("model class can update one record", (done) => {
    assertId(User.findOneAndUpdate({ name: "Joe" }, { name: "Alex" }), done);
  });
```
- findByIdAndUpdate
```js
it("model class can find a record with Id and update", (done) => {
    assertId(User.findByIdAndUpdate(joe._id, { name: "Alex" }), done);
  });
```
For Model Instance: 'joe'
- updateOne
updateOne是一个promise
```js
it("model instance can update", (done) => {
    assertId(joe.updateOne({ name: "Alex" }), done);
  });
```  
- 'set' and 'save'
set不是一个promise
```js
it("model instance update set and save", (done) => {
    joe.set("name", "Alex");
    joe
      .save()
      .then(() => User.findOne({ _id: joe._id }))
      .then((user) => {
        assert(user.name === "Alex");
        done();
      });
  });
```
>一般情况下，当需要update多项数据的时候，先做多项updatefunction，执行完成后，再save
#### 抽象helper function
```jsx
  function assertId(operation, done) {
    operation
      .then(() => User.findOne({ _id: joe._id }))
      .then((user) => {
        assert(user.name === "Alex");
        done();
      });
  }
```

## Section 7:Mongo Operators
https://docs.mongodb.com/manual/reference/operator/update/
1. Update Schema
```jsx
const UserSchema = new Schema({
  name: String,
  postCount: Number
});
```
2. Using inc operator
$inc:{想要更新的key:该key对应value增加的值}
```js
User.updateOne({ name: "Joe"},{$inc:{postCount:1}});
```
3. Updating assert
```js
  it("A user can have their postCount incremented", (done) => {
    User.updateOne({ name: "Joe" }, { $inc: { postCount: 1 } })
      .then(() => User.findOne({ name: "Joe" }))
      .then((user) => {
        assert(user.postCount === 1);
        done();
      });
  });
```
### Mongoose Validatation
1. Upating Schema
```js
const UserSchema = new Schema({
  name: {
    type: String,
    required:[true, 'Name is required']
  },
  postCount: Number,
});
```
requied的第二个参数为，验证失败时的信息
2. 添加validate
#### validateSync VS validate
validateSync是一个sync process
```js
const result = user.validateSync();
const {message} = result.errors.name;
```
我们比较关心的是，其中message的值

3. 添加assert
```js
  it("requires a user name", (done) => {
    const user = new User({ name: undefined });
    const result = user.validateSync();
    const { message } = result.errors.name;

    assert(message === "Name is required");
    done();
  });
```
### Mongoose Validatation Fucntion
1. 更新user schema
添加validator,与message
```js
const UserSchema = new Schema({
  name: {
    type: String,
    validate: {
      validator:(name) => name.length>2,
      message:"Name must be longer than 2 chararaters"
    },
    required: [true, "Name is required"],
  },
  postCount: Number,
});
```
2.添加vaildate与assert
```js
it("requires a user's name longer than 2 charaters", (done) => {
    const user = new User({ name: "Ai" });
    const result = user.validateSync();
    const { message } = result.errors.name;
    assert(message === "Name must be longer than 2 chararaters");
    done();
  });
```
### Handling Failed Inserts
```js
it("disallows invalid records from being saved", (done) => {
    const user = new User({ name: "AI" });
    user.save().catch((result) => {
      const { message } = result.errors.name;
      assert(message === "Name must be longer than 2 chararaters");
      done();
    });
  });
```
## Section 12: Putting Your Skills to the Test
npm install