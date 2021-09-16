## Section 2: Introduction to Node.js and NPM
Node.js PROs:
- Single-threaded, based on event driven, non-blocking 
I/O model
- Perfect for building fast and scalable data-intensive 
apps;
- JavaScript across the entire stack: faster and more 
efficient development
-  NPM: huge library of open-source packages available 
for everyone for free
-  Very active developer community
### Running Javascript Outside the Browser
```
node
.exit// ctrl+d: 快速退出
_*6 // _means previous result
String. // + tab: 显示所有string的function
node index.js // 执行index.js
```
### Node Modules: fs
Node通过require的方式导入需要的库（module），例如
```js
const fs = require('fs');
const textIn = fs.readFileSync("./txt/input.txt", "utf-8");
const textOut = `This is what we know: ${textIn}.\n Created on ${Date.now()}`;
fs.writeFileSync("./txt/output.txt", textOut);
```
https://nodejs.org/dist/latest-v14.x/docs/api/index.html
### Blokcing VS Non-Blokcing
Node.js是单线程的，所以node程序只有一个线程。
因为单线程会产生线程堵塞问题，所以对某些请求或者function可以采用异步执行，即该请求执行后会进去background，在得到相应后，再使用callback执行它下一步需要的操作
当callback不停的回调时，callback可能有N层深，因此会进入callback hell，在es6中我们使用promise来解决这个问题，es8中的async/await可以同样解决

### Reading and Writing Files Asynchronously
注意下面就是callback hell
```js
const fs = require("fs");
// Non-blocking, asynchronous way
fs.readFile("./txt/start.txt", "utf-8", (err, data1) => {
  fs.readFile(`./txt/${data1}.txt`, "utf-8", (err, data2) => {
    fs.readFile(`./txt/append.txt`, "utf-8", (err, data3) => {
      console.log(data2 + data3);
      fs.writeFile("./txt/final.txt", `${data2}\n${data3}`, "utf-8", (err) => {
        console.log("File written.");
      });
    });
  });
});
console.log("Will readFile");
```
### Creating a Simple Web Server
1. build a server
server会执行callback，返回hello from the server
```js
const server = http.createServer((req,res)=>{
  res.end('Hello from the server!');
});
```
2. start the server and listen on the port
监听3050端口，并且第一次执行的时候会执行callback，打印 listen to req on 3050
```js
server.listen(3050, "127.0.0.1", () => {
  console.log("Listen to req on 3050");
});
```
### Routing
使用req.url，来直接获取地址栏的路径
```js
const server = http.createServer((req, res) => {
  const pathName = req.url;
  if (pathName === "/" || pathName === "/overview") {
    res.end("This is overview page");
  } else if (pathName === "/product") {
    res.end("This is product page");
  } else {
    res.writeHead(404, {
      "Content-type": "text/html",
    });
    res.end("Page not found");
  }
});
```
### Building a (Very) Simple API
- ${__dirname}/dev-data/data.json: __dirname 是现在文件所在目录
- JSON.parse会把一个数据读成javascript格式
- res.end()，（）里的必须是string
- 文件头部的代码，仅会执行一次，而不会像下面的一样在持续监听中
```js
const data = fs.readFileSync(`${__dirname}/dev-data/data.json`, "utf-8");
const productData = JSON.parse(data);

const server = http.createServer((req, res) => {
  const pathName = req.url;
  if (pathName === "/" || pathName === "/overview") {
    res.end("This is overview page");
  } else if (pathName === "/product") {
    res.end("This is product page");
  } else if (pathName === "/api") {
    res.writeHead(200, { "Content-type": "application/json" });
    res.end(data);
  } else {
    res.writeHead(404, {
      "Content-type": "text/html",
    });
    res.end("<h1>Page not found<h1>");
  }
});

server.listen(3050, "127.0.0.1", () => {
  console.log("Listen to req on 3050");
});
```
### HTML Templating: Building the Templates
- 使用.replace替换字符串
- 使用/{productName}/g来做全局字符串替换
- let output = temp.replace(/{productName}/g,product.productName)
  - 直接修改argument传来的变量不是一个好的写法，所以后面使用了
  - output = output.replace(/{image}/g,product.image);
- 使用.join('')可以将一组array object变成string
```js
//创建replace函数，通过template-card，将json里的数据变成html
const replaceTemplate = (temp, product) => {
  let output = temp.replace(/{productName}/g, product.productName);
  output = output.replace(/{image}/g, product.image);
  output = output.replace(/{price}/g, product.price);
  output = output.replace(/{from}/g, product.from);
  output = output.replace(/{ProductNutrientsName}/g, product.nutrients);
  output = output.replace(/{quantity}/g, product.quantity);
  output = output.replace(/{description}/g, product.description);
  output = output.replace(/{id}/g, product.id);

  if (!product.organic) output = output.replace(/{notOrganic}/g, "not-organic");
  console.log(output);
  return output;
};

const tempOverview = fs.readFileSync(
  `${__dirname}/templates/template-overview.html`,
  "utf-8"
);
const tempProduct = fs.readFileSync(
  `${__dirname}/templates/template-product.html`,
  "utf-8"
);
const tempCard = fs.readFileSync(
  `${__dirname}/templates/template-card.html`,
  "utf-8"
);
const data = fs.readFileSync(`${__dirname}/dev-data/data.json`, "utf-8");
const dataObj = JSON.parse(data);
```
```js
//修改路由，在/的时候将主页的内容替换为json里的card
  if (pathName === "/" || pathName === "/overview") {
    res.writeHead(200, { "Content-type": "text/html" });

    const cardsHtml = dataObj
      .map((el) => replaceTemplate(tempCard, el))
      .join("");
    // console.log(cardsHtml);

    const output = tempOverview.replace("{productCards}", cardsHtml);
    res.end(output);

    // Product page
  }
```
### Parsing Variables from URLs
- 使用url.parse拿到query与pathName
  - const { query, pathname } = url.parse(req.url, true);
- 然后以id拿到data进行渲染
```js
const server = http.createServer((req, res) => {
  const { query, pathname } = url.parse(req.url, true);
  if (pathname === "/product") {
    const product = dataObj[query.id];
    const output = replaceTemplate(tempProduct, product);

    res.writeHead(200, { "Content-type": "text/html" });
    res.end(output);
  }
}
```
### Using Modules 2: Our Own Modules
- Every file in Node.js is treated as a module
- 新建文件，复制函数体
- 使用module.exports = 来导出module
```js
//replaceTemplate.js
module.exports = (temp, product) => {
  let output = temp.replace(/{productName}/g, product.productName);
  output = output.replace(/{image}/g, product.image);
  output = output.replace(/{price}/g, product.price);
  output = output.replace(/{from}/g, product.from);
  output = output.replace(/{ProductNutrientsName}/g, product.nutrients);
  output = output.replace(/{quantity}/g, product.quantity);
  output = output.replace(/{description}/g, product.description);
  output = output.replace(/{id}/g, product.id);

  if (!product.organic) output = output.replace(/{notOrganic}/g, "not-organic");
  return output;
};
```
- 在其它文件中，使用require来导入这个module
```js
//index.js
const replaceTemplate = require('./modules/replaceTemplate');
```
### Introduction to NPM and the package.json File
https://www.npmjs.com/
- not only for front-end development, but also the whole js stack
- npm init:在该目录加入node项目配置信息
- package.json
### Types of Packages and Installs
- regular dependency: code we build upon our project, our project need it to run correctly
  - npm install slugify 
- development dependency: only used for development, like testing library, webpack, not need for production
  - npm install nodemon --save-dev
- folder node_modules is all the dependency files our dependency needs
- npm i nodemon --global
> 技巧：npm 后面可以用 i 来代替 install
> 技巧：node i 可以直接跟tab找到文件
- 使用nodemon来监听并更新文件变化
  - nodemon index.js
- 在package.json的scripts中加入：
  - "start": "nodemon index.js"
  - 再使用npm run start，就可以执行start之后的命令，注意这里可以使用已经安装的dependence了
> 技巧：使用npm start，来替代npm run start
### Using Modules 3: 3rd Party Modules
- 使用require即可导入第三方modules
  - const slugify = require('slugify');
  - slugify是一个可以优化string格式的库
    - slugify('Hello Hi', {lower:true})
### Package Versioning and Updating
- Version Num
- Update Packaging
  - npm outdated
  - npm i slugify@1.0.0
  - "slugify": "^1.0.0"
    - 这里的^ 表示接受所有patch和minor release，即1.后面的.0.0两位
    - 如果改为~ 表示仅接受patch release 更新
    - 如果改为* 表示可以接受所有更新 
  - npm update slugify
- Delete Package
  - npm i express
  - npm uninstall express
- npm install
  - 在没有node_modules文件夹的情况下，根据package.json来安装所有的dependency files
- package-lock.json
  - 锁定所有dependency的版本，来保证在其它项目上dependency的版本会一致
  - package.josn与package-lock.json是确保project正确reconstruct的关键
- Prettier:
  - https://prettier.io/docs/en/options.html

## Section 3: Introduction to Back-End Web Development
### An Overview of How the Web Works
- user sent request to server
  - special request to dns, dns look up for ip
  - after have an ip address, TCP/IP socket connection is established between the server and client
  - make a http request
    - HTTPS is encrypted using TLS or SSL
- server sent response to client
  - http response is sent from server
- then process is repeated for each files
### Front-End vs. Back-End Web Development
- Backend:
  - server: store files, run http server and application
### Static vs Dynamic vs API
- Static: 
  - no backend codes, no application runing
- Dynamic = Web Application: server-side rendered
  - database
  - app: fetch data from db, build website
- API: 
  - only send data(in JSON) to browser, not the entire website
- API Powered website: Client-side rendered
  - building API
  - consuming API

## Section 4: How Node.js Works: A Look Behind the Scenes
### Node, V8, Libuv and C++
- V8: writedn in JS&C++ files
- libuv: event loop, thread pool, writen in C++
### Processes, Threads and the Thread Pool
- In a single thread of Nodejs process
  - Initialize program
  - Execute “top-level” code
  - Require moduels
  - Register evet callbacks
  - START EVENT LOOP
- Thread Pool
  - give four threads(can configure it up to 128 threads) sepearted from the main single thread
  - Event Loop can offload heavy work to thread pool
    - heavy work can includes:
      - File system APIs 
      - Cryptography
      - Compression
      - DNS lookups
### Event Loop
- WHERE All the application code that is inside callback functions (non-top-top-level code) executed
- Don't block
  -  Don’t use sync versions of functions in fs, crypto and zlib modules in your callback functions 
  -  Don’t perform complex calculations (e.g. loops inside loops) 
  -  Be careful with JSON in large objects
  -  Don’t use too complex regular expressions (e.g. nested quantifiers)

### Streams
>技巧：``` const server = require("http").createServer();```
使用Streams，来chunk形式的读取一个文件
```js
  const readable = fs.createReadStream("test-file1.txt");
  readable.on("data", (chunk) => {
    res.write(chunk);
  });
  readable.on("end", () => {
    res.end();
  });
  readable.on("error", (err) => {
    console.log(err);
    res.statusCode = 500;
    res.end("File not found");
  });
```
backpressure: 读取文件的速度要远大于写入的速度
要完整解决这个问题，可以直接使用pipe，以上的代码，使用下面两行即可完整实现
```js
  const readable = fs.createReadStream("test-file.txt");
  readable.pipe(res);
```
### Modules
在使用require的时候，其实它就已经把需要的文件包裹，并读取，然后放置到缓存里 
```js
class Calculator {
  add(a, b) {
    return a + b;
  }

  multiply(a, b) {
    return a * b;
  }

  divide(a, b) {
    return a / b;
  }
}

module.exports = Calculator;
```
以上还有种更简略的写法，即直接将export assign一个expression
```js
module.exports = class  {
  add(a, b) {
    return a + b;
  }

  multiply(a, b) {
    return a * b;
  }

  divide(a, b) {
    return a / b;
  }
}
```
export的时候也可以将function加到export里,
```js
exports.add = (a, b) => a + b;
exports.multiply = (a, b) => a * b;
exports.divide = (a, b) => a / b;
```
然后调用的时候，即可以使用export的module，直接调用
```js
const Cal = require("./test-module2");
console.log(Cal.add(2, 3));
```
或者，更简洁的写法：
```js
const {add} = require("./test-module2");
console.log(add(2, 3));
```
对于caching，假如我们定义：
```js
//test-module3.js
console.log("Hello");

module.exports = () => console.log("Log this");
```
再导入的时候
```js
require("./test-module3")();
require("./test-module3")();
require("./test-module3")();
```
实际结果，为
```
Hello
Log this
Log this
Log this
```
因为通过caching，这个module里的code仅仅被load并执行了一次，export里面的东西被存到了node的cache里，每次被call，会从cache里直接调用执行

## Section 5: Asynchronous JavaScript
npm i superagent
### 对callback hell的改造,如何建立promise chain
看下面的代码
```js
fs.readFile(`${__dirname}/dog.txt`, (err, data) => {
  console.log(`Breed: ${data}`);
  superagent
    .get(`https://dog.ceo/api/breed/${data}/images/random`)
    .end((err, res) => {
      if (err) return console.log(err.message);
      console.log(res.body);

      fs.writeFile('dog-img.txt', res.body.message, (err) => {
        console.log('Random write');
      });
    });
});
```
如何把他改造成promise chain的形式：需要保证在每一个then里面返回一个promise
```js
const readFilePro = (file) => {
  return new Promise((resolve, reject) => {
    fs.readFile(file, (err, data) => {
      if (err) reject('failed to find file');
      resolve(data);
    });
  });
};

const writeFilePro = (file, data) => {
  return new Promise((resolve, reject) => {
    fs.writeFile(file, data, (err) => {
      if (err) reject('failed to write file');
      resolve('success');
    });
  });
};

readFilePro(`${__dirname}/dog.txt`)
  .then((data) => {
    console.log(`Breed: ${data}`);
    return superagent.get(`https://dog.ceo/api/breed/${data}/images/random`);
  })
  .then((res) => {
    console.log(res.body);
    return writeFilePro('dog-img.txt', res.body.message);
  })
  .catch((err) => {
    console.log(err.message);
  });

```
### 使用async/await
简单的将promise转为async/await
```js
readFilePro(`${__dirname}/dog.txt`)
  .then((data) => {
    console.log(`Breed: ${data}`);
    return superagent.get(`https://dog.ceo/api/breed/${data}/images/random`);
  })
```
```js
 const data = await readFilePro(`${__dirname}/dog.txt`);
 console.log(`Breed: ${data}`);
 const res = await superagent.get(`https://dog.ceo/api/breed/${data}/images/random`);
```
将以上的promise写法，全部改为async/then:
```js
readFilePro(`${__dirname}/dog.txt`)
  .then((data) => {
    console.log(`Breed: ${data}`);
    return superagent.get(`https://dog.ceo/api/breed/${data}/images/random`);
  })
  .then((res) => {
    console.log(res.body);
    return writeFilePro('dog-img.txt', res.body.message);
  })
  .catch((err) => {
    console.log(err.message);
  });
```
```js
const getDogPic = async () => {
  try {
    const data = await readFilePro(`${__dirname}/dog.txt`);
    console.log(`Breed: ${data}`);

    const res = await superagent.get(
      `https://dog.ceo/api/breed/${data}/images/random`
    );
    console.log(res.body);

    await writeFilePro('dog-img.txt', res.body.message);
  } catch (err) {
    console.log(err.message);
  }
};

getDogPic();

```
总结：
- await必须被async包裹，async标记的不一定包含await
- await必须加在一个promise前面
- async/await只是一个语法糖，使代码看起来更同步化，本质还是promise，它并不会将代码改造成sync
- async可以有return，但是这个return是一个resolve的value，如果想外界捕捉异常，需要在async的内部的try catch里throw一个error
### 使用IIEF改造promise then
```js
console.log('1.Will get dog pics');
getDogPic()
  .then((x) => {
    console.log(x);
    console.log('3.Done');
  })
  .catch((err) => {
    console.log(err);
  });
``` 
```js
(async () => {
  try {
    console.log('1: Will get dog pics!');
    const x = await getDogPic();
    console.log(x);
    console.log('3: Done getting dog pics!');
  } catch (err) {
    console.log('ERROR 💥');
  }
})();
```
### 并行运行promise
使用Promise.all
```js
const getDogPic = async () => {
  try {
    console.log('start');
    const data = await readFilePro(`${__dirname}/dog.txt`);
    console.log(`Breed: ${data}`);

    const res1 = superagent.get(
      `https://dog.ceo/api/breed/${data}/images/random`
    );
    const res2 = superagent.get(
      `https://dog.ceo/api/breed/${data}/images/random`
    );
    const res3 = superagent.get(
      `https://dog.ceo/api/breed/${data}/images/random`
    );

    const all = await Promise.all([res1, res2, res3]);
    const imgs = all.map((item) => item.body.message);
    console.log(imgs);

    await writeFilePro('dog-img.txt', imgs.join('\n'));
  } catch (err) {
    console.log(err.message);
  }
  return '2:Ready';
};
```
> 技巧：使用.join('\n')来改造输出string形式
## Section 6: Express: Let's Start Building the Natours API!
### Express
使用send发送一个返回
```js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.status(200).send('Hello from the server');
});

app.listen('3050', () => {
  console.log('App runing');
});
```
使用json发送一个返回
```js
app.get('/', (req, res) => {
  res.status(200).json({ message: 'Hello from the server', app: 'Natours' });
});
```
### APIs and RESTful API Design
- API endpoint不能含有动词，因为动词部分已经被get,post等方法表示
- endpoint应该对应数据库里面的resources,例如
  - /addNewTour -> POST /tours
  - /getTour -> GET /tours/7
  - /updateTour -> PUT /tours/7 OR PATCH /tours/7
  - /deleteTour -> DELETE /tours/7
  - /getToursByUser -> GET /users/3/tours
  - /deleteToursByUser -> DELETE /users/3/tours/9
- JSON VS Javascript Object
  - 同样都是key-value pair
  - JSON的key必须是string，value可以是string，number，boolean等等
  - 我们在send一个response的时候可以使用JSend做formatting
- Stateless RESTful API
  - All state is handled on the client. This means that each 
request must contain all the information necessary to process a certain request. The server should not have to remember previous requests

### API get
```js
const express = require('express');
const app = express();
const fs = require('fs');

app.use(exress.json());
const tours = JSON.parse(
  fs.readFileSync(`${__dirname}/dev-data/data/tours-simple.json`)
);

app.get('/api/tours', (req, res) => {
  res.status(200).json({
    status: 'success',
    results: tours.length,
    data: {
      tours,
    },
  });
});

app.post('/api/tours',(req,res) => {

})

app.listen('3050', () => {
  console.log('App runing');
});

```
### API post
- middleware 是在req与res之间处理他们交互的
- 加入下面这行，可以获取request里的body内容
```js
app.use(exress.json());
```
```js
app.post('/api/tours', (req, res) => {
  console.log(req.body);
  const newId = tours[tours.length - 1].id + 1;
  const newTour = Object.assign({ id: newId }, req.body);

  tours.push(newTour);
  fs.writeFile(
    `${__dirname}/dev-data/data/tours-simple.json`,
    JSON.stringify(tours),
    (err) => {
      res.status(201).json({
        status: 'success',
        data: {
          tour: newTour,
        },
      });
    }
  );
});
```
### Responding to URL Parameters
- 使用req.params拿到parameters
- 在url后面加入？,就可使这个url变为optional，这样即使前端输入的参数不全时，也能通过,例如下面，就算输入/api/tours/6/5 也不会报错
> 技巧：将一个数字string转化为num的方法，就是直接在后面*1，因为从req回来的是json，因此需要这样的string到num转换，比如下面这样
> ```const tour = tours.find((el) => el.id === req.params.id * 1);```
```js
app.get('/api/tours/:id/:x/:y?', (req, res) => {})
```
注意，req里拿到json数据格式都是string，也需要注意，我们sendback的都是json数据
```js
app.get('/api/tours/:id', (req, res) => {
  const tour = tours.find((el) => el.id === req.params.id * 1);
  if (!tour) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }
  res.status(200).json({
    status: 'success',
    data: {
      tour,
    },
  });
});
```
### API PATCH
PATCH VS PUT
- PATCH：仅更新需要更新的属性
- PUT:更新整个object
```js
app.patch('/api/tours/:id', (req, res) => {
  const tour = tours.find((el) => el.id === req.params.id * 1);
  if (!tour) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }
  res.status(200).json({
    status: 'success',
    data: { tour: '<Updated tour>' },
  });
});
```
### API DELETE
- delete返回的data一般是null
```js
app.delete('/api/tours/:id', (req, res) => {
  const tour = tours.find((el) => el.id === req.params.id * 1);
  if (!tour) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }
  res.status(204).json({
    status: 'success',
    data: null,
  });
});
```
### Refactor Router
1. 抽出每个callback
注意，调用callback的时候没有（）
```js
const getAllTours = (req, res) => {
  res.status(200).json({
    status: 'success',
    results: tours.length,
    data: {
      tours,
    },
  });
};

const getTourById = (req, res) => {
  const tour = tours.find((el) => el.id === req.params.id * 1);
  if (!tour) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }
  res.status(200).json({
    status: 'success',
    data: {
      tour,
    },
  });
};

const createTour = (req, res) => {
  console.log(req.body);
  const newId = tours[tours.length - 1].id + 1;
  const newTour = Object.assign({ id: newId }, req.body);

  tours.push(newTour);
  fs.writeFile(
    `${__dirname}/dev-data/data/tours-simple.json`,
    JSON.stringify(tours),
    (err) => {
      res.status(201).json({
        status: 'success',
        data: {
          tour: newTour,
        },
      });
    }
  );
};

const updateTour = (req, res) => {
  const tour = tours.find((el) => el.id === req.params.id * 1);
  if (!tour) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }
  res.status(200).json({
    status: 'success',
    data: { tour: '<Updated tour>' },
  });
};

const deleteTour = (req, res) => {
  const tour = tours.find((el) => el.id === req.params.id * 1);
  if (!tour) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }
  res.status(204).json({
    status: 'success',
    data: null,
  });
};

app.get('/api/tours', getAllTours);
app.get('/api/tours/:id', getTourById);
app.post('/api/tours', createTour);
app.patch('/api/tours/:id', updateTour);
app.delete('/api/tours/:id', deleteTour);
```
2.使用route改善
```js
app.get('/api/tours', getAllTours);
app.get('/api/tours/:id', getTourById);
app.post('/api/tours', createTour);
app.patch('/api/tours/:id', updateTour);
app.delete('/api/tours/:id', deleteTour);
```
可以改为
```js
app.route('/api/tours').get(getAllTours).post(createTour);
app.route('/api/tours/:id').get(getTourById).patch(updateTour).delete(deleteTour);
```
### Middleware and Request-Response Cycle
Everything is middleware(even routers)
- parsing body
- logging
- setting header
- router
Request-Response Cycle
- starting from 'incoming request'
- then execrting all the middleware in middleware stack step by step
- finally sending the response to finish the cycle

### Creating Our Own Middleware
1. 使用user来引用middleware，use里的三个参数跟着request, response和next
2. middleware必须使用next()结束，不然会使程序stuck Request-Response Cycle 中
3. middleware一定是按照顺序执行的
```js
app.use((req, res, next) => {
  req.requestTime = new Date().toLocaleTimeString();
  next();
});
```
### Using 3rd-Party Middleware
morgan: see request data in console
npm i morgan
app.use(morgan('dev'));
https://expressjs.com/en/5x/api.html
https://expressjs.com/en/resources/middleware.html

### Implementing the "Users" Routes
1. add routers for user
```js
app.route('/api/users').get(getAllUsers).post(createUser);
app.route('/api/users/:id').get(getUser).patch(updateUser).delete(deleteUser);
```
2. add handlerfunctions

### Creating and Mounting Multiple Routers
1. Add middleware
2. use express to connect mini router to middleware
```js
const tourRouter = express.Router();
tourRouter.route('/').get(getAllTours).post(createTour);
tourRouter
  .route('/:id')
  .get(getTourById)
  .patch(updateTour)
  .delete(deleteTour);

app.use('/api/tours',tourRouter);
```
其中``` app.use('/api/users',userRouter);```叫做mounting a new Router

### A Better File Structure
1. 建立routes文件夹，与tourRoutes.js, userRoutes.js两个新文件
2. 在tourRoutes.js, userRoutes.js两个文件中，提取出相应get等方法
3. 建立controllers文件夹，与tourController.js， userController.js两个文件，注意这里fs引用的文件地址写法
4. 在两个文件中，写入对应的handler方法，并回到tourRoutes.js, userRoutes.js中做引用
5. 在app.js中导入两个module
6. 创建server.js，将server相关的移入其中,其它可以迁入的还有database configuraion, error handling or enviroment variables
> 技巧：app.js主要做middleware declarations,configure everything with express application
```js
// tourRoutes.js
const express = require('express');
const {
  getAllTours,
  createTour,
  getTourById,
  updateTour,
  deleteTour,
} = require('../controllers/tourController');

const router = express.Router();
router.route('/').get(getAllTours).post(createTour);
router.route('/:id').get(getTourById).patch(updateTour).delete(deleteTour);

module.exports = router;

```
```js
//userRoutes.js
const express = require('express');
const {
  getAllUsers,
  createUser,
  getUser,
  updateUser,
  deleteUser,
} = require('../controllers/userController');

const router = express.Router();
router.route('/').get(getAllUsers).post(createUser);
router.route('/:id').get(getUser).patch(updateUser).delete(deleteUser);

module.exports = router;
```
```js
//tourController.js
const fs = require('fs');

const tours = JSON.parse(
  fs.readFileSync(`${__dirname}/../dev-data/data/tours-simple.json`)
);

exports.getAllTours = (req, res) => {
  res.status(200).json({
    status: 'success',
    results: tours.length,
    data: {
      tours,
    },
  });
};

exports.getTourById = (req, res) => {
  console.log(req.requestTime);
  const tour = tours.find((el) => el.id === req.params.id * 1);
  if (!tour) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }
  res.status(200).json({
    status: 'success',
    data: {
      tour,
    },
  });
};

exports.createTour = (req, res) => {
  console.log(req.body);
  const newId = tours[tours.length - 1].id + 1;
  const newTour = Object.assign({ id: newId }, req.body);

  tours.push(newTour);
  fs.writeFile(
    `${__dirname}/dev-data/data/tours-simple.json`,
    JSON.stringify(tours),
    (err) => {
      res.status(201).json({
        status: 'success',
        data: {
          tour: newTour,
        },
      });
    }
  );
};

exports.updateTour = (req, res) => {
  const tour = tours.find((el) => el.id === req.params.id * 1);
  if (!tour) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }
  res.status(200).json({
    status: 'success',
    data: { tour: '<Updated tour>' },
  });
};

exports.deleteTour = (req, res) => {
  const tour = tours.find((el) => el.id === req.params.id * 1);
  if (!tour) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }
  res.status(204).json({
    status: 'success',
    data: null,
  });
};

```
```js
//userController.js
const fs = require('fs');

const users = JSON.parse(
  fs.readFileSync(`${__dirname}/../dev-data/data/users.json`)
);

exports.getAllUsers = (req, res) => {
  res.status(200).json({
    status: 'success',
    results: users.length,
    data: {
      users,
    },
  });
};

exports.getUser = (req, res) => {
  console.log(req.requestTime);
  const user = users.find((el) => el.id === req.params.id * 1);
  if (!user) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }
  res.status(200).json({
    status: 'success',
    data: {
      user,
    },
  });
};

exports.createUser = (req, res) => {
  console.log(req.body);
  const newId = users[users.length - 1].id + 1;
  const newUser = Object.assign({ id: newId }, req.body);

  users.push(newUser);
  fs.writeFile(
    `${__dirname}/dev-data/data/users.json`,
    JSON.stringify(users),
    (err) => {
      res.status(201).json({
        status: 'success',
        data: {
          user: newUser,
        },
      });
    }
  );
};

exports.updateUser = (req, res) => {
  const tour = tours.find((el) => el.id === req.params.id * 1);
  if (!tour) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }
  res.status(200).json({
    status: 'success',
    data: { tour: '<Updated tour>' },
  });
};

exports.deleteUser = (req, res) => {
  const tour = tours.find((el) => el.id === req.params.id * 1);
  if (!tour) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }
  res.status(204).json({
    status: 'success',
    data: null,
  });
};

```
```js
//server.js
const app = require('./app');

app.listen('3050', () => {
  console.log('App runing');
});
```
### Param Middleware
- 1. 通过提取每个handler的验证部分，在controller中建立新的middleware handler function，其中通过参数val拿取path里的parameter
- 2. 注意这个checkID function中的return必不能少，因为如果没有return，express会继续执行这个function
- 3. 最后在router中导入并使用，注意调用的时候是router.param()
- 这种将共用功能抽取单独做成middleware function的做法是最符合express思想的, the handler should only concerns what it really needs to do 
```js
//tourController.js
exports.checkID = (req, res, next, val) => {
  if (val > tours.length) {
    return res.status(404).json({
      status: 'fail',
      message: `Couldn't find tour`,
    });
  }

  next();
};
```
```js
//tourRoutes.js
router.param('id', checkID);
```
### Chaining Multiple Middleware Functions
- 1. 在middleware function中想建立新的function，比如创建record前，我想验证body是否合法
- 2. 首先写好middleware function
- 3. 然后在router里引用，写法为```router.route('/').get(getAllTours).post(checkBody, createTour); ```
```js
//tourController.js
exports.checkBody = (req, res, next) => {
  if (!req.body.name || !req.body.price) {
    return res.status(400).json({
      status: 'fail',
      message: `Require name and price`,
    });
  }

  next();
};
```
```js
//tourRoutes.js
router.route('/').get(getAllTours).post(checkBody, createTour);
```

### Serving Static Files
通过middleware来增加对static file的访问，注意
- static后面的path跟的是文件目录，不能是具体文件
- 在前端访问的时候，地址栏输入的是具体文件名，而不能够是目录，比如下面是合法的访问：http://localhost:3050/overview.html
```js
app.use(express.static(`${__dirname}/public`));
```

### Environment Variables
npm i dotenv
1. 安装 dotenv，创建配置文件config.env
2. 在server.js中导入
```js
const dotenv = require('dotenv');
dotenv.config({ path: './config.env' });
```
3. 在其它文件中即可使用
```js
if (process.env.NODE_ENV === 'development') app.use(morgan('dev'));
```
- 读取config.env的过程只会发生一次，因此并不需要反复定义
- 读取环境变量，只需要使用process.env.即可
- 导入与dotenv.config都必须放在 app.js导入之前，不然无法在其中调用

### ESlint
npm i eslint prettier eslint-config-prettier eslint-plugin-prettier eslint-config-airbnb eslint-plugin-node eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react --save-dev
https://eslint.org/docs/rules/

npm i eslint@5