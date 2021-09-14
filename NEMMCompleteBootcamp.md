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
## Section 6: Express: Let's Start Building the Natours API!
### Express
