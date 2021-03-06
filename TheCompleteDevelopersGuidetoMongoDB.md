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

## Section 8: Handling Relational Data
1. Create Post Schema
```js
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

const PostSchema = new Schema({
  title: String,
  content: String,
});

module.export = PostSchema;
```
2. Add Test
```js
const assert = require("assert");
const User = require("../src/user");

describe("Subdocuments", () => {
  it("can create a subdocuments", (done) => {
    const joe = new User({ name: "Joe", posts: [{ title: "PostTitle" }] });

    joe
      .save()
      .then(() => User.findOne({ name: "Joe" }))
      .then((user) => {
        assert(user.posts[0].title === "PostTitle");
        done();
      });
  });
});

```
### Add subdocuments
```js
    joe
      .save()
      .then(()=>User.findOne({ name: "Joe" })).then((user)=>{
        user.posts.push({title:'New Post'});
        user.save();
      })
```
### Remove subdocuments
使用remove()方法来直接移除subdocuments，因为需要save（）才会向mongo发送请求，所以可以直接调用
```js
joe
      .save()
      .then(() => User.findOne({ name: "Joe" }))
      .then((user) => {
        user.posts[0].remove();
        return user.save();
      })
```
### Virtural type
it前面加x，将不会在mocha里进行
1. 添加virtual property 在model里
```js
UserSchema.virtual("postCount").get(function(){
  return this.posts.length;
});
```
2. 添加Test
```js
describe("Virtual Types", () => {
  it("postCount return number of posts", (done) => {
    const joe = new User({
      name: "Joe",
      posts: [{ titel: "PostTitle" }],
    });
    joe
      .save()
      .then(() => User.findOne({ name: "Joe" }))
      .then((user) => {
        assert(joe.postCount === 1);
        done();
      });
  });
});
```
>注意这里没有用箭头函数，不然this会绑定整个User.js

## Section 9: Thinking About Schema Design
### Nested data
1.使用objectId与ref作为subdocuments, 创建与更新Schema
需要注意的是，ref跟的是下面的'user'
```js
//create model
const User = mongoose.model("user", UserSchema);
```
```js
const mongoose = require("mongoose");
const PostSchema = require("./post");
const Schema = mongoose.Schema;

//create schema
const UserSchema = new Schema({
  name: {
    type: String,
    validate: {
      validator: (name) => name.length > 2,
      message: "Name must be longer than 2 chararaters",
    },
    required: [true, "Name is required"],
  },
  posts: [PostSchema],
  likes: Number,
  blogPosts: [
    {
      type: Schema.Types.ObjectId,
      ref: "blogPosts",
    },
  ],
});

//add virtual properties
UserSchema.virtual("postCount").get(function () {
  return this.posts.length;
});

//create model
//register model as 'user'
const User = mongoose.model("user", UserSchema);

//export model
module.exports = User;
```
```js
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

const BlogPostSchema = new Schema({
  title: String,
  conetent: String,

  comments: [{ type: Schema.Types.ObjectId, ref: "comment" }],
});

const BlogPost = mongoose.model("blogPost", BlogPostSchema);

module.exports = BlogPost;
```
```js
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

const CommentSchema = new Schema({
  content: String,
  user: {
    type: Schema.Types.ObjectId,
    ref: "user",
  },
});

const Comment = mongoose.model("comment", CommentSchema);
module.exports = Comment;
```
2. 更新test helper
collection无法同时drop
```js

```

3. 初始化model，兼wiring up
注意wiring up, blogPost to user的时候，同样使用的是push方法，但是这样mongo会自动识别里面的id，做wiring up;   
同理 comment.user= joe
```js
    joe = new User({ name: "Joe" });
    blogPost = new BlogPost({
      title: "JS is Great",
      content: "Yes, it really is",
    });
    comment = new Comment({ content: "Congrats on great post" });

    joe.blogPosts.push(blogPost);
    blogPost.comments.push(comment);
    comment.user = joe;
```
4. 使用Promise.all来实现所有数据储存
```js
  beforeEach((done) => {
    joe = new User({ name: "Joe" });
    blogPost = new BlogPost({
      title: "JS is Great",
      content: "Yes, it really is",
    });
    comment = new Comment({ content: "Congrats on great post" });

    joe.blogPosts.push(blogPost);
    blogPost.comments.push(comment);
    comment.user = joe;

    Promise.all([joe.save(),blogPost.save(),comment.save()]).then(()=>{
      done();
    });
  });
```

5. 使用it.only，仅作一项测试
```js
  it.only("save a relation", (done) => {
    User.findOne({ name: "Joe" }).then((user) => {
      console.log(user);
      done();
    });
  })
```
> ```User.findOne({name: 'Joe'})```仅仅返回了一个query，只有后面跟```exec()```或者```then()```才能够执行这个query
6. 使用populate，加载一层associatioin
```js
User.findOne({ name: "Joe" })
      .populate("blogPosts")
      .then((user) => {
        console.log(user);
        done();
      });
```
注意这里populate里面跟的key一定是user model里的attribute;  
populate只能读取一层关系，并不能更深入的读取;
7. 添加assert,判单blogPosts读取情况
```js
  it("save a relation", (done) => {
    User.findOne({ name: "Joe" })
      .populate("blogPosts")
      .then((user) => {
        assert(user.blogPosts[0].title === "JS is Great");
        done();
      });
  });
```
### 读取全部relation关系
populate里面还可以跟configuration，即path与populate
path跟的key即为user里的attribute
populate跟的是，通过path fetch出的blogPosts再做一次populate
model跟的是对应的model
```js
  it("save a full relation graph", (done) => {
    User.findOne({ name: "Joe" })
      .populate({
        path: "blogPosts",
        populate: {
          path: "comments",
          model: "comment",
          populate: {
            path: "user",
            model: "user",
          },
        },
      })
      .then((user) => {
        assert(user.name === "Joe");
        assert(user.blogPosts[0].title === "JS is Great");
        assert(
          user.blogPosts[0].comments[0].content === "Congrats on great post"
        );
        assert(user.blogPosts[0].comments[0].user.name === "Joe");

        done();
      });
  });
```

## Section 10: Mongoose Middleware
Middleware:
run before or after an event
1. Model里添加pre,来调取association
注意：像virutal里一样，这里传入的function是非箭头函数写法的
```js
UserSchema.pre('remove',function () {
  const BlogPost = mongoose.model('blogPost');
})
```
### Operator: $in
Go through blogPosts, 拿到每一个id，并删除
```js
  const BlogPost = mongoose.model('blogPost');
  BlogPost.remove({_id:{$in:this.blogPosts}});
```
## Section 11: Handling Big Collections with Pagination
skip: skip the first N records
limit: limit the number of result of query 
1. find all user
```js
User.find({})
```
2. skip first user, and limit the number of 2
```js
User.find({}).skip(1).limit(2)
```
3. add sort modifier
按照key:name的升序排列，如果是逆序排列，则name:2
```js
User.find({})
      .sort({ name: 1 })
      .skip(1)
      .limit(2)
```

## Section 12: Putting Your Skills to the Test
npm install
### 创建artist, album model
```js
//album.js
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

const AlbumSchema = new Schema({
  title: String,
  date: Date,
  copiesSold: Number,
  numberTracks: Number,
  image: String,
  revenue: Number,
});

module.exports = Album;
```
这里需要注意，因为album本身只是schema，所以不需要注册model
```js
//artist.js
// Todo: Create Artist Model
const mongoose = require("mongoose");
const AlbumSchema = require("./album");
const Schema = mongoose.Schema;

const ArtistSchema = new Schema({
  name: String,
  age: Number,
  yearsActive: Number,
  image: String,
  genre: String,
  website: String,
  netWorth: Number,
  labelName: String,
  retired: Boolean,
  ablums: [AlbumSchema],
});

const Artist = mongoose.model("artist", ArtistSchema);

module.exports = Artist;
```
### 查找Artist
```js
const Artist = require("../models/artist");

/**
 * Finds a single artist in the artist collection.
 * @param {string} _id - The ID of the record to find.
 * @return {promise} A promise that resolves with the Artist that matches the id
 */
module.exports = (_id) => {
  //return Artist.findOne({ _id: _id });
  return Artist.findById(_id);
};
```
### 创建Artist
注意下面直接传了整个props进去
```js
const Artist = require("../models/artist");
module.exports = (artistProps) => {
  const artist = new Artist(artistProps);
  return artist.save();
};
```
### 删除Artist
注意这种直接用model删除的写法，效率更高
```js
const Artist = require("../models/artist");

module.exports = (_id) => {
  return Artist.remove({_id});
};
```
### 编辑Artist
```js
const Artist = require("../models/artist");

module.exports = (_id, artistProps) => {
  // return Artist.findByIdAndUpdate(_id, artistProps);
  return Artist.update({_id}, artistProps);
};
```
### 按照格式，返回大小值
```js
const Artist = require("../models/artist");

/**
 * Finds the lowest and highest age of artists in the Artist collection
 * @return {promise} A promise that resolves with an object
 * containing the min and max ages, like { min: 16, max: 45 }.
 */
module.exports = () => {
  const minQuery = Artist.find({})
    .sort({ age: 1 })
    .limit(1)
    .then((artists) => artists[0].age);
  const maxQuery = Artist.find({})
    .sort({ age: -1 })
    .limit(1)
    .then((artists) => artists[0].age);

  return Promise.all([minQuery, maxQuery]).then((result) => {
    return { min: result[0], max: result[1] };
  });
};
```
### 初始化对象，并向里面注入key, value pair
```js
const sortOrder = {};
const sortProperty = 'name';
sortOrder[sortProperty] = 1;
console.log(sortOrder); //object{name:1}
```
相同的es6写法可以是
```js
{[sortProperty]:1}
```
### 使用offset，与limit调整sort结果window
```js
module.exports = (criteria, sortProperty, offset = 0, limit = 20) => {
  const query = Artist.find({})
    .sort({ [sortProperty]: 1 })
    .skip(limit * offset)
    .limit(limit);
  return Promise.all([query, Artist.count()]).then((results) => {
      return { all: results[0], count: results[1], offset: offset, limit: limit };
    });
}
```
### $gte, $lte 结合，实现多重filter
```js
const query = Artist.find(buildQuery(criteria))
    .sort({ [sortProperty]: 1 })
    .skip(offset)
    .limit(limit);

const buildQuery = (criteria) => {
  const query = {};

  console.log(criteria);
  if (criteria.age) {
    query.age = {
      $gte: criteria.age.min,
      $lte: criteria.age.max,
    };
  }

  if (criteria.yearsActive) {
    query.yearsActive = {
      $gte: criteria.yearsActive.min,
      $lte: criteria.yearsActive.max,
    };
  }

  return query;
};
```
### Add text indexes
mongo
use upstar_music
show dbs
db.artists.createIndex({name:"text"})
### $text for text search
```js
if (criteria.name) {
    query.$text = { $search: criteria.name };
  }
```
### Set retired using $in
```js
const Artist = require("../models/artist");

/**
 * Sets a group of Artists as retired
 * @param {array} _ids - An array of the _id's of of artists to update
 * @return {promise} A promise that resolves after the update
 */
module.exports = (_ids) => {
  return Artist.updateMany({ _id: { $in: _ids } }, { retired: true });
};

```
### faker data
```js
//seed.js
import faker from 'faker';
function createArtist() {
  return {
    name: faker.name.findName(),
    age: randomBetween(15, 45),
    yearsActive: randomBetween(0, 15),
    image: faker.image.avatar(),
    genre: getGenre(),
    website: faker.internet.url(),
    netWorth: randomBetween(0, 5000000),
    labelName: faker.company.companyName(),
    retired: faker.random.boolean(),
    albums: getAlbums()
  };
}
```
## Section 14: MongoDB with Node and Express
npm install --save mongoose express mocha nodemon
Node: engine for running Javascript outside of a browser enviroment. Can work with HTTP requests.
Express: framework to simplify working with HTTP requests.

### Create app with express
```js
const expree = require("express");

const app = express();

module.exports = app;
```
### create index.js using app
```js
//index.js
const app = require("./app");

app.listen(3050, () => {
  console.log("Runing on 3050");
});

```
### handle http request with Express
你在浏览器地址栏输入的，都是发送的get请求
```js
//app.js
const express = require("express");

const app = express();

// Watch for incoming requests of method GET to the route http://localhost:3050/api
app.get("/api", (req, res) => {
  res.send({ hi: "there" });
});

module.exports = app;
```
### Mocha in Express
npm install --save supertest
Create app.test.js
```js
const assert = require("assert");
const request = require("supertest");
const app = require("../app");

describe("The express app", () => {
  it("handles a GET request", (done) => {
    request(app)
      .get("/api")
      .end((err, response) => {
        assert(response.body.hi === "there");
        done();
      });
  });
});
```
supertest, 首先绑定了app，然后再使用了get方法发送/api请求
### 添加routes
```js
//routes.js
module.exports = (app) =>{

  app.get("/api", (req, res) => {
    res.send({ hi: "there" });
  });
};
```
更新app.js
```js
const express = require("express");
const routes = require('./routes/routes');
const app = express();

routes(app);

module.exports = app;
```
### 添加controller
```js
greeting(req, res){
  }
```
等效于
```js
greeting: function(req, res){}
```
完成整个controller
```js
//drivers_controller.js
module.exports = {
  greeting(req, res) {
    res.send({ hi: "there" });
  },
};
```
### refactor routes
```js
//routes.js
const DriverController = require("../controllers/drivers_controller");

module.exports = (app) => {
  app.get("/api", DriverController.greeting);
};
```
### add driver model
```js
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

const DriverSchema = new Schema({
  email: {
    type: String,
    required: true,
  },
  driving: {
    type: Boolean,
    default: false,
  },
});

const Driver = mongoose.model("driver", DriverSchema);
module.exports = Driver;
```
### BodyParser
npm install --save body-parser
add bodyParser to app.js
```js
const express = require("express");
const routes = require("./routes/routes");
const bodyParser = require('body-parser');
const app = express();

app.use(bodyParser.json());
routes(app);

module.exports = app;

```
### send body with POST
```js
//drivers_controller_test.js
const assert = require("assert");
const request = require("supertest");
const app = require("../../app");

describe("Driver Controller", () => {
  it("Post to /api/drivers creates a new driver", (done) => {
    request(app)
      .post("/api/drivers")
      .send({ email: "test@test.com" })
      .end(() => {
        done();
      });
  });
});
```