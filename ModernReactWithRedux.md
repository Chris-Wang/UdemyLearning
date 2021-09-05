

## 单词
legible: 易读的，清晰的
## Section 1&2:
Components in React:
- produce JSX, which tells react what we want to show on the screen
- handle user events

JXS Elements:
- tell React to create a normal HTML element 
- tell React to show another component

React VS ReactDOM:
- React: knows how to work with components, called a 'reconciler'
- ReactDOM: knows how to take instructions on what we want to show and turn it into HTML, called a 'renderer'

Create-react-app
npm install -g create-react-app
create-react-app jsx
== 
npx create-react-app jsx

Create-react-app file structure:
- src: source code
- public: static files
- node_modules: dependencies files installed
- package.json: dependencies required and configuration
- package-lock.json: dependencies installed

import vs require
- import: ES6 import statement
- require: commonJS import statement

A component is a 
- Function
OR
- Class
that produces HTML to show the user: using JSX
and handele feedback from user: using EventHandlers

Inline Styling with JSX:
- HTML: 
```HTML
<button style="background-color:blue; color:white;">Submit</button>
```
- JSX: 
<button style={{backgroundColor:'blue',color:'white'}}>Submit</button>
JSX中 inline的""部分因为变成了JS Expression，所以必须变为{}包裹；同时HTML中任何a-b的表达，会变成aB，同时属性值需要加''

Community convention:
- double quotes(双引号)：JSX中，来包裹一个string
- single quote(单引号)：包裹任何一个非JSX的属性值

Class vs ClassName: 
JSX中的class要统一变为className，类似的，还有for变成htmlFor

{}可以表示一个js object，也可以表示reference一个js variable

对于object里的text，我们不能直接引用JS object到html里，例如
```JSX
const buttonText ={text: "Click Me"};
<button style={{ backgroundColor: "blue", color: "white" }}>
        {buttonText}
      </button>
```
而是需要在其中加key
```JSX
const buttonText ={text: "Click Me"};
<button style={{ backgroundColor: "blue", color: "white" }}>
        {buttonText.text}
      </button>
```
对于其它则可以，例如
```JSX
const style = { backgroundColor: "blue", color: "white" };
  return (
    <div>
      <label className="label" for="name">
        Enter Name:
      </label>
      <input id="name" type="text" />
      <button style={style}>{getButtonText()}</button>
    </div>
  );
```

单词：
curly braces：大括号
parentheses: 小括号
made a typo: 拼写错误
semicolon:分号
ternay expression: 三元表达式

## Section3:
Component Nesting
Component Resuability
Component Configuration

create-react-app components

<link
  rel="stylesheet"
  href="https://cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.4.1/semantic.min.css"
/>

https://github.com/marak/Faker.js/
npm install --save faker
- save into local project 
- use ```<img alt="avatar" src={faker.image.avatar()} />``` for faker avatar

Creating a Resuaable Configurable Component:
- Identify the JSX that appears to be duplicated
- What is the purpose of that block of JSX?  Think of a descriptive name for what it does
- Create a new file to house this new component - it should have the same name as the component
- Create a new component in the new file, paste the JSX into it
- Make the new component configurable by using React's 'props' system

Add ```export``` make statment to make the component avaiable to other files inside the project

Props:
- System for passing data from a parent component to a child or nested component
- Goal is to customize or configure a child component
```jsx
<CommentDetail author="Sam"/>
```
- author: name of the prop
- "Sam": value of the prop

传递自定义component给父component并显示：
```jsx
      <ApprovalCard>
        <CommentDetail
          avatar={faker.image.avatar()}
          author="Sam"
          timeAgo="Today at 4:45pm"
          comment="Nice post!"
        />
      </ApprovalCard>
```
在父component中使用```{props.children}```即可
```jsx
const ApprovalCard = (props) => {
  return (
    <div className="ui card">
      <div className="content">{props.children}</div>
      <div className="extra content">
        <div className="ui two buttons">
          <div className="ui basic green button">Approve</div>
          <div className="ui basic red button">Reject</div>
        </div>
        {props.children}
      </div>
    </div>
  );
};
```

## Section 4:
Functional Components VS Class Components
Functional Components: 
- Can produce JSX to show content to the user
Class Components: 
- Can produce JSX to show content to the user
- Can use the Lifecycle Method system to run code at specific points in time
- Can use the 'state' system to update content on the screen

In Hooks Systems, Functional Components:
- Can produce JSX to show content to the user
- Can use Hooks to run code at specific points in time
- Can use Hooks to access state system and update content on screen

create-react-app seasons

Getting a physical location use:
```jsx
window.navigator.geolocation.getCurrentPosition(
    (position) => console.log(position),
    (err) => console.error(err)
  );
```

Timeline of what happens to our application:
- JS file loaded by browser
- App component gets created
- We call geolocation service
- App returns JSX, gets rendered to page as HTML
- We get result of geolocation!

Rules of Class Components:
- Must be a Javascript Class
- Must extend (subclass) React.Component
- Must define a 'render' method that returns some amount of JSX

Rules of State:
- Only usable with class components
- You will confuse props with state :(
- 'State' is a JS object that contains data relevant to a component
- Updating 'state' on a component causes the component to (almost) instantly rerender
- State must be initialized when a component is created
- State can only be updated using the function 'setState' 

Initial number in constructor: set to null
```jsx
this.state = { lat: null };
```
We did not directly assign value to a state！！！
```
this.state.lat = position.coords.lat;
```
Only one exception: when we initialize a state
We called setState to assign value to a state
```jsx
this.setState({errorMessage:err.message});
```
setState()括号里跟的是想要更新的object，只会add,change state里的object，而不会删除其中的object

## Section 6
Component Lifecycle
- constructor:Good place to do one-time setup
- render:Avoid doing anything besides returning JSX
- componentDidMount:Good place to do data-loading!
- render
- componentDidUpdate:Good place to do more data-loading when state/props change
- componentWillUnmount:Good place to do cleanup (especially for non-React stuff)

Other lifecycle methods rarely used
- shouldComponentUpdate
- getDerivedStateFromProps
- getSnapshotBeforeUpdate

### Alternate State Initialization:
```jsx
state = { lat: null, errorMessage: "" };
```
等效于
```jsx
  constructor(props) {
    super(props);
    this.state = { lat: null, errorMessage: "" };
  }
```

### 三元表达式改表查询：
```jsx
  const text = season === "winter" ? "Bur, it is chilly" : "Lets hit the beach";
  const iconName = season === "winter" ? "snowflake" : "sun";

  return (
    <div>
      <i className={`${iconName} icon`} />
      <h1>{text}</h1>
      <i className={`${iconName} icon`} />
    </div>
  );
```
```jsx
  const seasonConfig = {
    summer:{
      text:'Lets hit hte beach!',
      iconName: 'sun'
    },
    winter: {
      text:'Bur, it is chilly',
      iconName: 'snowflake'
    }
  };

  const {text, iconName} = seasonConfig[season];

  return (
    <div>
      <i className={`${iconName} icon`} />
      <h1>{text}</h1>
      <i className={`${iconName} icon`} />
    </div>
  );
```
root class name have to match the component class name:
```jsx
const SeasonDisplay = (props) => {
  const season = getSeason(props.lat, new Date().getMonth());
  const { text, iconName } = seasonConfig[season];

  return (
    <div className={`season-display ${season}`}>
      <i className={`icon-left massive ${iconName} icon`} />
      <h1>{text}</h1>
      <i className={`icon-right massive ${iconName} icon`} />
    </div>
  );
};
```

### Default Props:
```jsx
const Spinner = (props) => {
  return (
    <div className="ui active dimmer">
      <div className="ui big text loader">{props.message || "Loading..."}</div>
    </div>
  );
};
```
等效于，并且下面的写法更好
```jsx
const Spinner = (props) => {
  return (
    <div className="ui active dimmer">
      <div className="ui big text loader">{props.message}</div>
    </div>
  );
};

Spinner.defaultProps = {
  message: "Loading...",
};
```
### Using helper methods to:
Avoid having multiple return method inside render method
```jsx
  renderContent(){
    if (this.state.errorMessage && !this.state.lat) {
      return <div>Error: {this.state.errorMessage}</div>;
    }

    if (!this.state.errorMessage && this.state.lat) {
      return <SeasonDisplay lat={this.state.lat} />;
    }

    return <Spinner message="Please accept location request" />;
  }

  // React says we have to define the render
  render() {
    return (
      <div className="border red">
        {this.renderContent()}
      </div>
    );
  }
```

## Section 7
create-react-app pics
### onChange
```jsx
<input type="text" onChange={this.onInputChange}/>
```
当```onChange={this.onInputChange}```写成```onChange={this.onInputChange()}```时，表示每次render执行时，onInputChange()都会被执行一次,在这里
- User clicks on something:onClick
- User changes text in an input:onChange
- User submits a form:onSubmit

### controlled vs uncontrolled
controlled: centralize all of the information that we have inside of our React component  
the entire idea is to make sure it's the react application, driving and storing all data and not the HTML side of things.
```jsx
            <input
              type="text"
              value={this.state.term}
              onChange={(e) => this.setState({ term: e.target.value })}
            />
```
uncontrolled: we have to fetch the information from the HTML DOM
```jsx
            <input
              type="text"
              onChange={(e) => console.log(e)})}
            />
```
### 解决this undefined 的三种办法
1. bind
2. 改用arrow function
3. 调用方法时，使用arrow function，比如，注意，这里方法后面加了()
```jsx
onFormSubmit(event) {
    event.preventDefault();
    console.log(this.state.term);
  }
<form className="ui form" onSubmit={ event => this.onFormSubmit()}>
```
axios vs fetch:
- axios:3rd party package
- fetch:function built into modern browsers

npm install --save axios

```jsx
import React from "react";
import axios from 'axios';
import SearchBar from "./SearchBar";
```
> 一般我们引用的dependencies和第三方library都会放在自己创建的文件之上

```jsx
axios.get('https://api.unsplash.com/search/photos',{
      params:{query: term}
    });
```
等同于
```jsx
axios.get('https://api.unsplash.com/search/photos?query=term',);
```

### 解决axios返回异步的两种方式：
```jsx
    axios
      .get("https://api.unsplash.com/search/photos", {
        params: { query: term },
        headers: {
          Authorization:
            "Client-ID 8kWv6hlbzltKXl8gwsBw3e2KtD_R-OtREAv0Mw3KUss",
        },
      })
      .then((response) => {
        console.log(response.data.results);
      });
```
```jsx
  async onSearchSubmit(term) {
    const response = await axios.get("https://api.unsplash.com/search/photos", {
      params: { query: term },
      headers: {
        Authorization: "Client-ID 8kWv6hlbzltKXl8gwsBw3e2KtD_R-OtREAv0Mw3KUss",
      },
    });

    console.log(response.data.results);
  }
```
> 初始化array state的时候，要写成
> ```state = { images: [] };```
> 初始化number state的时候，要写成
> ```this.state = { lat: null };```
> 初始化string state的时候，要写成
> ```state = { errorMessage: "" };```

### arrow function加异步处理
```jsx
onSearchSubmit = async (term) => {
    const response = await axios.get("https://api.unsplash.com/search/photos", {
      params: { query: term },
      headers: {
        Authorization: "Client-ID 8kWv6hlbzltKXl8gwsBw3e2KtD_R-OtREAv0Mw3KUss",
      },
    });

    this.setState({ images: response.data.results });
  };
```
## Section 9
```jsx
const numbers = [0,1,2,3,4];
```
以下
```jsx
let newNumbers = [];
for(let i = 0; i < numbers.length; i++>) {
  newNumbers.push(numbers[i]*10);
}
```
等效于
```jsx
numbers.map((num) => num * 10);
```
类似用法
```jsx
const users = [
  { id: 1, name: 'Leanne Graham' },
  { id: 2, name: 'Ervin Howell' },
  { id: 3, name: 'Clementine Bauch' },
  { id: 4, name: 'Patricia Lebsack' }
];


export default class App extends React.Component {
    

  render() {
    const urs = users.map(({id, name})=>{return <li key={id}>{name}</li>;});
    return (
        <ul>
            {urs}
        </ul>
    );
  }
}
```
### 参数解构
```jsx
const images = props.images.map((image) => {
    return (
      <img key={image.id} src={image.urls.regular} alt={image.description} />
    );
  });
```
变为
```jsx
const images = props.images.map(({ description, id, urls }) => {
    return <img key={id} src={urls.regular} alt={description} />;
  });
```

## Section 10
### React Refs
- Gives access to a single DOM element
- We create refs in the constructor, assign them to instance variables, then pass to a particular JSX element as props

### Load event
```jsx
      <div>
        <img ref={this.imageRef} alt={description} src={urls.regular} />
      </div>
```
img load有延时，如何等待img加载完成执行callback，可以通过ref添加eventListener，例如
```jsx
constructor(props) {
    super(props);

    this.imageRef = React.createRef();
  }

  componentDidMount() {
    console.log(this.imageRef);
    this.imageRef.current.addEventListener("load", this.setSpans);
  }

  setSpans = () => {
    console.log(this.imageRef.current.clientHeight);
  };

  render() {
    const { description, urls } = this.props.image;

    return (
      <div>
        <img ref={this.imageRef} alt={description} src={urls.regular} />
      </div>
    );
  }
```
The Math.ceil() function always rounds a number up to the next largest integer.

### 输入框与用户输入同步的通常写法
```jsx
            <input
              type="text"
              value={this.state.term}
              onChange={(e) => this.setState({ term: e.target.value })}
            />
```
### createRef用法实例
```jsx
class ImageCard extends React.Component {
  constructor(props) {
    super(props);

    this.state = { spans: 0 };

    this.imageRef = React.createRef();
  }

  componentDidMount() {
    this.imageRef.current.addEventListener("load", this.setSpans);
  }

  setSpans = () => {
    const height = this.imageRef.current.clientHeight;
    const span = Math.ceil(height / 10);
    this.setState({ spans: span });
  };

  render() {
    const { description, urls } = this.props.image;

    return (
      <div style={{ gridRowEnd: `span ${this.state.spans}` }}>
        <img ref={this.imageRef} alt={description} src={urls.regular} />
      </div>
    );
  }
}
```

## Section 11:
Component Design:
In this App, we needs
- SearchBar: for user input
- VideoList：on the right, and contains  
  - VideoItem
- VideoDetails

AIzaSyCEmEDGhsSrNaAjlEgKArFQ8JyrG3hikHY

> const value一般用全大写命名变量

### Deeply Nested Callbacks
Talking from children to parent, use callbacks
```jsx
//App.js
  onVideoSelect = (video) => {
    console.log("From the App!", video);
  };

  render() {
    return (
      <div className="ui container">
        <SearchBar onFormSubmit={this.onTermSubmit} />
        <VideoList
          videos={this.state.videos}
          onVideoSelect={this.onVideoSelect}
        />
      </div>
    );
  }
```
```jsx
//VideoList.js
const VideoList = ({ videos, onVideoSelect }) => {
  const renderedList = videos.map((video) => {
    return <VideoItem video={video} onVideoSelect={onVideoSelect} />;
  });
  return <div className="ui relaxed divided list">{renderedList}</div>;
};
```
```jsx
//VideoItem.js
const VideoItem = ({ video, onVideoSelect }) => {
  return (
    <div className="video-item item" onClick={() => onVideoSelect(video)}>
      <img
        className="ui image"
        src={video.snippet.thumbnails.medium.url}
        alt={video.snippet.title}
      />
      <div className="content">
        <div className="header">{video.snippet.title}</div>
      </div>
    </div>
  );
};
```
### Conditional Rendering
注意，这里condtion被放到了底层
```jsx
const VideoDetail = ({ video }) => {
  if (!video) {
    return <div>Loading...</div>;
  }
  return (
    <div>
      <div className="ui segment">
        <h4 className="ui header">{video.snippet.title}</h4>
        <p>{video.snippet.description}</p>
      </div>
      ;
    </div>
  );
};
```
### Semantic UI Grid
16格的单行划分
```jsx
 render() {
    return (
      <div className="ui container">
        <SearchBar onFormSubmit={this.onTermSubmit} />
        <div className="ui grid">
          <div className="ui row">
            <div className="eleven wide column">
              <VideoDetail video={this.state.selectedVideo} />
            </div>
            <div className="five wide column">
              <VideoList
                videos={this.state.videos}
                onVideoSelect={this.onVideoSelect}
              />
            </div>
          </div>
        </div>
      </div>
    );
  }
```

## Section 12
### Hooks System
- useState: Function that lets you use state in a functional component
- useEffect: Function that lets you use something like lifecycle methods in a functional component
- useRef：Function that lets you create a 'ref' in a function component

### React.Fragment
如何巧妙的去掉<div>，同时又合理的保留key，参考以下
```jsx
  const renderedItems = items.map((item) => {
    return (
      <React.Fragment key={item.title}>
        <div className="title active">
          <i className="dropdown icon"></i>
          {item.title}
        </div>
        <div className="content active">
          <p>{item.content}</p>
        </div>
      </React.Fragment>
    );
  });
```
### Array解构
```jsx
const colors = ['red','blue'];
const [firstElement, secondElement] = colors;
```
### useState
```jsx
const [activeIndex, setActiveIndex] = useState(null);
```
- ```null``：state默认的value，initial value for this piece of state
- activeIndex: piece of state
- setActiveIndex: functions to change this pice of state   
每次调用```setActiveIndex(index)```，都会rerender
- 这个rerender会讲component里的代码全部执行一遍
- 但是useState(null)里的null将不会被重新赋值，而会被setActiveIndex(index)里的index取代，这个初始值仅在初始化的时候会被提取

### class components vs function components
Initialization:
- Class:```state = { activeIndex: 0 }```
- Function:```useState(0)```
Reference:
- Class:```this.state.activeIndex```
- Function:```activeIndex;```
Updates:
- Class:```this.setState({ activeIndex: 10 })```
- Function:```setActiveIndex(10);```

### input field function component
```jsx
const [term, setTerm] = useState("");

  return (
    <div>
      <div className="ui form">
        <div className="field">
          <label>Enter Search Tern</label>
          <input
            className="input"
            value={term}
            onChange={(e) => setTerm(e.target.value)}
          ></input>
        </div>
      </div>
    </div>
  );
```
en.wikipedia.org/w/api.php?action=query&list=search&format=json&origin=*&srsearch=programming

### The 'useEffect' Hook
Allows function components to use something like lifecycle methods, We configure the hook to run some code automatically in one of three scenarios
- When the component is rendered for the first time only
- When the component is rendered for the first time and whenever it rerenders 
- When the component is rendered for the first time and (whenever it rerenders and some piece of data has changed)

### Second argument in UseEffect
for the second argument, you only see
```jsx
useEffect(() =>{
    console.log("what");
  });
```
- Run at initial render
- Run after every rerender
- Run When the component is rendered for the first time and whenever it rerenders 
```jsx
useEffect(() =>{
    console.log("what");
  }, []);
```
- Run at initial render
- Run When the component is rendered for the first time only
```jsx
useEffect(() =>{
    console.log("what");
  }, [term]);
```
- Run at initial render
- Run after every rerender if data has changed since last render
- Run When the component is rendered for the first time and (whenever it rerenders and some piece of data has changed)

# Async Code in useEffect
async是不能直接用在 useEffect里的，像下面这样
```jsx
useEffect(async() =>{
   await console.log("what");
  }, [term]);
```
可以使用以下三种方法解决：
```jsx
  useEffect(() =>{
    const search = async() => {
      await axios.get('term');
    };
    search();
  }, [term]);
```
```jsx
  useEffect(() =>{
    (async() => {
      await axios.get('term');
    })();
  }, [term]);
```
```jsx
  useEffect(() => {
    axios.get("term").then((response) => {
      console.log("api");
    });
  }, [term]);
```
### useEffect's cleanup function
useEffect里的callback fucntion里，只能有一种返回类型，就是function，这个function可以叫cleanup function，它在initial render时并不会执行，但是却会在下次调用useEffect时执行

### 检查用户停止输入再搜索的实现
通过在cleanup function中取消上一个timeout，来检查用户的输入频率并决定call
```jsx
  useEffect(() => {
    const search = async () => {
      const { data } = await axios.get("https://en.wikipedia.org/w/api.php", {
        params: {
          action: "query",
          list: "search",
          origin: "*",
          format: "json",
          srsearch: term,
        },
      });

      setResults(data.query.search);
    };
    const timeoutId = setTimeout(() => {
      if (term) {
        search();
      }
    }, 500);

    return () => {
      clearTimeout(timeoutId);
    };
  }, [term]);
```

### 使用debounce来检查用户输入，调取api, 并解决results.length warning的问题
```jsx
const Search = () => {
  const [term, setTerm] = useState("programming");
  const [debouncedTerm, setDebouncedTerm] = useState(term);
  const [results, setResults] = useState([]);

  useEffect(() => {
    const timerId = setTimeout(() => {
      setDebouncedTerm(term);
    }, 500);

    return () => {
      clearTimeout(timerId);
    };
  }, [term]);

  useEffect(() => {
    const search = async () => {
      const { data } = await axios.get("https://en.wikipedia.org/w/api.php", {
        params: {
          action: "query",
          list: "search",
          origin: "*",
          format: "json",
          srsearch: debouncedTerm,
        },
      });

      setResults(data.query.search);
    };

    search();
  }, [debouncedTerm]);

  const renderedResults = results.map((result, index) => {
    return (
      <div className="item" key={index}>
        <div className="right floated content">
          <a
            className="ui button"
            href={`https://en.wikipedia.org?curid=${result.pageid}`}
          >
            Go
          </a>
        </div>
        <div className="content">
          <div className="header">{result.title}</div>

          <span dangerouslySetInnerHTML={{ __html: result.snippet }}></span>
        </div>
      </div>
    );
  });

  return (
    <div>
      <div className="ui form">
        <div className="field">
          <label>Enter Search Tern</label>
          <input
            className="input"
            value={term}
            onChange={(e) => setTerm(e.target.value)}
          ></input>
        </div>
      </div>
      <div className="ui called list">{renderedResults}</div>
    </div>
  );
};
```

### 三元表达式，写入onclick来控制渲染
```jsx
const Dropdown = ({ options, selected, onSelectChange }) => {
  const [open, setOpen] = useState(false);

  const renderedOptions = options.map((option) => {
    if (option.value === selected.value) {
      return null;
    }

    return (
      <div key={option.value} onClick={() => onSelectChange(option)}>
        {option.label}
      </div>
    );
  });

  return (
    <div className="ui form">
      <div className="field">
        <label className="label">Select a Color</label>
        <div
          className={`ui selection dropdown ${open ? "visible active" : ""}`}
          onClick={() => setOpen(!open)}
        >
          <i className="dropdown icon"></i>
          <div className="text">{selected.label}</div>
          <div className={`menu ${open ? "visible transition" : ""}`}>
            {renderedOptions}
          </div>
        </div>
      </div>
    </div>
  );
};
```

### EventListeners
所有EventListener里，addEventListener的优先级最高，最先被执行

### useRef
使用```ref.current```可以返回当前element的reference,因此可以用来隔绝event listener之间的执行范围，例如
```jsx
const Dropdown = ({ options, selected, onSelectChange }) => {
  const [open, setOpen] = useState(false);
  const ref = useRef();

  useEffect(() => {
    document.body.addEventListener(
      "click",
      (event) => {
        if (ref.current.contains(event.target)) {
          return;
        }
        setOpen(false);
      },
      { capture: true }
    );
  }, []);

  const renderedOptions = options.map((option) => {
    if (option.value === selected.value) {
      return null;
    }

    return (
      <div key={option.value} onClick={() => onSelectChange(option)}>
        {option.label}
      </div>
    );
  });

  return (
    <div ref={ref} className="ui form">
      <div className="field">
        <label className="label">Select a Color</label>
        <div
          className={`ui selection dropdown ${open ? "visible active" : ""}`}
          onClick={() => setOpen(!open)}
        >
          <i className="dropdown icon"></i>
          <div className="text">{selected.label}</div>
          <div className={`menu ${open ? "visible transition" : ""}`}>
            {renderedOptions}
          </div>
        </div>
      </div>
    </div>
  );
};
```
### 使用三元表达式来 控制一整片JSX的渲染
```jsx
const [showDropdown, setShowDropdown] = useState(true);
return (
    <div>
      <button onClick={() => setShowDropdown(!showDropdown)}>
        Toggle Dropdown
      </button>
      {showDropdown ? (
        <Dropdown
          selected={selected}
          onSelectChange={setSelected}
          options={options}
        />
      ) : null}
    </div>
  );
```
AIzaSyCHUCmpR7cT_yDFHC98CZJy2LTms-IwDlM

### props依然可以在useEffect中调用
```jsx
const Convert = ({language, text}) => {
  useEffect(() =>{

  },[language, text]);
  return(
    <div></div>
  );
};
```
### 熟练async在useEffect中的写法
```jsx
useEffect(() => {

    const doTranslation = async () => {
      const {data} = await axios.post(
        "https://translation.googleapis.com/language/translate/v2",
        {},
        {
          params: {
            q: text,
            target: language.value,
            key: "AIzaSyCHUCmpR7cT_yDFHC98CZJy2LTms-IwDlM",
          },
        }
      );

      setTranslated(data.data.translationsp[0].translatedText);
    };

    doTranslation();
  }, [language, text]);
```

## Section 13
### 使用window.location来route
```jsx
const showAccordion = () => {
  if (window.location.pathname === "/") {
    return <Accordion items={items} />;
  }
};

const showList = () => {
  if (window.location.pathname === "/list") {
    return <Search />;
  }
};

const showDropdown = () => {
  if (window.location.pathname === "/dropdown") {
    return <Dropdown />;
  }
};

const showTranslate = () => {
  if (window.location.pathname === "/translate") {
    return <Translate />;
  }
};

const App = () => {
  const [selected, setSelected] = useState(options[0]);

  return (
    <div>
      {showAccordion()}
      {showList()}
      {showDropdown()}
      {showTranslate()}
    </div>
  );
};
```
### window.location 结合component，三元表达式，实现route 功能
```jsx
//Route.js
const Route = ({ path, children }) => {
  return window.location.pathname === path ? children : null;
};

export default Route;
```
```jsx
//App.js
const App = () => {
  const [selected, setSelected] = useState(options[0]);

  return (
    <div>
      <Route path="/">
        <Accordion items={items}></Accordion>
      </Route>
      <Route path="/list">
        <Search />
      </Route>
      <Route path="/dropdown">
        <Dropdown
          label="Select a color"
          options={options}
          selected={selected}
          onSelectChange={setSelected}
        />
      </Route>
      <Route path="/translate">
        <Translate />
      </Route>
    </div>
  );
};
```
### 使用URL的方式来实现导航
```jsx
const Header = () => {
  return (
    <div className="ui secondary pointing menu">
      <a href="/" className="item">
        Accordion
      </a>
      <a href="/list" className="item">
        Search
      </a>
      <a href="/dropdown" className="item">
        Dropdown
      </a>
      <a href="/translate" className="item">
        Translate
      </a>
    </div>
  );
};
```
但是以上的问题，是每次点击一个链接都会导致full page reload
### change URL,但是不会reload页面的方法
```jsx
window.history.pushState({},'','/translate')
```
使用这种方法我们希望点击一个<a>时，
- 改变地址栏地址
- 停止页面刷新
- 点击的同时发送一个navevent
- 放置的监听器，捕捉到这个event，并执行callback
实现代码如下：
```jsx
//Route.js
const Route = ({ path, children }) => {
  const [currentPath, setCurrentPath] = useState(window.location.pathname);
  useEffect(() => {
    const onLocationChange = () => {
      setCurrentPath(window.location.pathname);
    };

    window.addEventListener("popstate", onLocationChange);

    return () => {
      window.removeEventListener("popstate", onLocationChange);
    };
  }, []);

  return currentPath === path ? children : null;
};
```
```jsx
//Link.js
const Link = ({ className, href, children }) => {
  const onClick = (event) => {
     if (event.metaKey || event.ctrlKey) {
      return;
    }
    event.preventDefault();
    window.history.pushState({}, "", href);

    const navEvent = new PopStateEvent("popstate");
    window.dispatchEvent(navEvent);
  };

  return (
    <a onClick={onClick} className={className} href={href}>
      {children}
    </a>
  );
};
```
### 引用set方法的refactor
```jsx
            <VideoList
              videos={videos}
              onVideoSelect={(video) => setSelectedVideo(video)}
            />
```
以上，完全等同于
```jsx
            <VideoList
              videos={videos}
              onVideoSelect={setSelectedVideo}
            />
```

### Custom Hooks
- Best way to create reusable code in a React project (besides components!)
- Created by extracting hook-related code out of a function component
- Custom hooks always make use of at least one primitive hook internally
- Each custom hook should have one purpose
- Kind of an art form!
- Data-fetching is a great thing to try to make reusable

### Process for Building Custom Hooks
- Identify each line of code related to some single purpose
- Identify the inputs to that code
- Identify the outputs to that code
- Extract all of the code into a separate function, receiving the inputs as arguments, and returning the outputs

### Custom hooks 的返回
既可以写成
```jsx
return [video, onTermSubmit];
```
也可以写成
```jsx
return {video, onTermSubmit};
```
其中onTermSubmit是function

## Section 15
npm install -g vercel
vercel login
//部署
vercel
//重新部署
vercel --prod

## Section 16 Redux
### Redux Cycle
Aciton creator(person drop off the form) => 
Action(the form) => 
dispatch(form receiver) =>
Reducers(departments) =>
State(compiled department data)

### spread
```jsx
return [...oldListOfClaims, action.payload]; 
```
等同于返回一个新的array，里面包含了旧的array，以及加入了新的action.payload
```js
numbers = [1,2,3];
[...numbers,4] //[1,2,3,4]
```

### 参数为undefined时的初始化
```js
const claimsHistory = (oldListOfClaims = [], action) => {
  if (action.type === 'CREATE_CLAIM'){
    return [...oldListOfClaims, action.payload];  
  }
  
  return oldListOfClaims;
};
```
以上，当oldListOfClaims为undefined时候，会初始化为[]

### filter方法
```js
numbers.filter(num => num !== 2) //返回一个新的array,为[1,3]
```
可以用filter来删除元素，例如
```jsx
const policies = (policies = [], action) => {
  if(action.type === 'CREATE_POLICY') {
    return [...policies, action.payload.name];
  }else if (action.type === 'DELETE_POLICY'){
    return policies.filter(name => name!== action.payload.name);
  }
};
```

### Reducers
- Goals: take some existing data, some action, and then modify and return that existing data based upon the contents of an action

### 从代码上理解Redux life cycle：
Aciton creator(person drop off the form) => 
```js
const createPolicy = (name, amount) => {
  return{
    //Action
    type: 'CREATE_POLICY',
    payload:{
      name: name,
      amount: amount
    }
  };
};

const deletePolicy = (name) => {
  return{
    type:'DELETE_POLICY',
    payload:{
      name: name
    }
  };
};

const createClaim = (name, amount) => {
  return{
    type: 'CREATE_CLAIM',
    payload:{
      name:name,
      amount:amount
    }
  };
};

```

Reducers(departments) =>
```js
const claimsHistory = (oldListOfClaims = [], action) => {
  if (action.type === 'CREATE_CLAIM'){
    return [...oldListOfClaims, action.payload];  
  }
  
  return oldListOfClaims;
};

const accounting = (money=100,action) => {
  if(action.type === 'CREATE_CLAIM'){
    return money - action.payload.amount;
  }else if(action.type === 'CREATE_POLICY'){
    return money + action.payload.amount;
  }
  
  return money;
};

const policies = (policies = [], action) => {
  if(action.type === 'CREATE_POLICY') {
    return [...policies, action.payload.name];
  }else if (action.type === 'DELETE_POLICY'){
    return policies.filter(name => name!== action.payload.name);
  }
  
  return policies;
};
```
State(compiled department data)
```js
const {createStore, combineReducers} = Redux;
//下面配置的reducer是按照：key:reducer方法名设置的
const ourDeparments = combineReducers({
  accounting: accounting,
  claimsHistory: claimsHistory,
  policies: policies
});

const store = createStore(ourDeparments);
```
Action(the form) => 
dispatch(form receiver) =>
只有通过dispatch action,才能修改state里的数据
```js
store.dispatch(createPolicy('Alex',30));
store.dispatch(createPolicy('Jim',30));
store.dispatch(createPolicy('Bob',40));

store.dispatch(createClaim('Jim',10));

store.dispatch(deletePolicy('Bob'));
```

## Section 17 React Redux
npm install --save redux react-redux

Provider
Connect

### import pattern
如果导入的是default，可以不用大括号；否则可以加大括号，比如
```js
import {selectSong} from '../actions';
```
ES6中，如果Key与Value相同，则可以简化
```js
export default connect(mapStateToProps, { selectSong: selectSong})(SongList);
```
等同于
```js
export default connect(mapStateToProps, { selectSong})(SongList);
```
### Redux is not magical
- Redux does not automatically detect action creators being called
- Redux does not automatically detect a function returning an object that is an 'action'

## Section 18: Redux thunk
npm install --save redux react-redux axios redux-thunk
redux-thunk: Middleware to help us make requests in a redux application

### General Data Loading with Redux
- Component gets rendered onto the screen
- Component's 'componentDidMount' lifecycle method gets called
- We call action creator from 'componentDidMount'
> Components are generally responsible for fetching data they need by calling an action creator
- Action creator runs code to make an API request
- API responds with data
- Action creator returns an 'action' with the fetched data on the 'payload' property
> Action creators are responsible for making API requests, This is where Redux-Thunk comes into play
- Some reducer sees the action, returns the data off the 'payload'
- Because we generated some new state object, redux/react-redux cause our React app to be rerendered
> We get fetched data into a component by generating new state in our redux store, then getting that into our component through mapStateToProps

### Rules on Action Creator
Action creator must return plain objects, no async actions hould be presented

Normal Rules:
- Action Creators must return action objects
- Actions must have a type property
- Actions can optionally have a 'payload'

### Rules with Redux Thunk
- Action Creators can return action objects
- Action Creators can return functions!
- If an action object gets returned, it must have a type
- If an action object gets returned, it can optionally have a 'payload'

github.com/reduxjs/redux-thunk

### hookup react-thunk
```jsx
import { createStore, applyMiddleware} from "redux";
import thunk from 'redux-thunk';

const store = createStore(reducers, applyMiddleware(thunk));

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

### arrow function简写
```jsx
export const fetchPosts = () => {
  return async (dispatch) =>{
    const response = await jsonplaceholder.get("./posts");

    dispatch({ type: "FETCH_POSTS", payload: response });
  };
};

```
可以简化为
```jsx
export const fetchPosts = () => async (dispatch) => {
  const response = await jsonplaceholder.get("./posts");

  dispatch({ type: "FETCH_POSTS", payload: response });
};
```

## Section 19
### Rules of Reducers
- Must return any value besides 'undefined'
- Produces 'state', or data to be used inside of your app using only previous state and the action
- Must not return reach 'out of itself' to decide what value to return  (reducers are pure)
- MISLEADING >> Must not mutate its input 'state' argument
  - mutate:edit, change
  ```js
  export default (state,action) =>{
    //bad
    state[0] = 'sa'
    state.pup()
    state.push()
    state.name='same'
    state.age=30
  }
  ``` 

### Equality of Arrays and Objects
```js
const numbers = [1,2,3];
number === [1,2,3] //false
```
array类型比较的是reference

### Safeupdate in state
#### In array
Removing an element from an array
- state.pop() -> state.filter(element => element !== 'hi')
Adding an element to an array
- state.push('hi') -> [ ...state, 'hi' ]
Replacing an element in an array
- state[0] = 'hi' -> state.map(el => el === 'hi' ? 'bye' : el)

#### In an object
Updating a property in an object
- state.name = 'Sam' -> { ...state, name: 'Sam' }
Adding a property to an object
- state.age = 30 -> { ...state, age: 30 }
Removing a property from an object
- delete state.name ->(不太推荐) { ...state, age: undefined } ->lodashi library: _.omit(state, 'age')

### Reducer的写法
使用if
```js
export default (state=[], action) => {
  if(action.type === 'FETCH_POST'){
    return action.payload;
  }

  return state;
};
```
使用switch
```js
export default (state = [], action) => {
  switch (action.type) {
    case "FETCH_POSTS":
      return action.payload;
    default:
      return state;
  }
};
```
### 从mapStateToProps中获取单个user
如果不想mapStateToProps只是拿取所有的user，而是过滤获得单个user，可以使用如下做法
```js
class UserHeader extends React.Component {
  componentDidMount() {
    this.props.fetchUser(this.props.userId);
  }
  render() {
    const { user } = this.props;
    if (!user) {
      return null;
    }
    return <div>{user.name}</div>;
  }
}

const mapStateToProps = (state, ownProps) => {
  return { user: state.users.find((user) => user.id === ownProps.userId) };
};

export default connect(mapStateToProps, { fetchUser })(UserHeader);

```
### 解决每一个user都被over fetching的方法
#### 使用memoize
该方法虽然只会对相同参数的方法只执行一次api call，但是当服务器端该参数对应的数据发生变动时，无法在本地客户端进行更新
```js
function getUser(id) {
    fetch(id)；
    return 'make a request'
} 

const memGetUser = _.memoize(getUser)
//memoize会wrap getUser,并且执行call
memoizedGetUser(3)
//会返回同一个结果，但是因为参数相同，memoize并不会再次执行call
memoizedGetUser(3)
```
npm install --save lodash
```js
export const fetchUser = function (id) { 
  return async function(dispatch) {
  const response = await jsonplaceholder.get(`/users/${id}`);

  dispatch({ type: "FETCH_USER", payload: response.data });
  };
};
```
```js
export const fetchUser = (id) => (dispatch) => {
  _fetchUser(id, dispatch);
};

const _fetchUser = _.memoize(async (id, dispatch) => {
  const response = await jsonplaceholder.get(`/users/${id}`);

  dispatch({ type: "FETCH_USER", payload: response.data });
});
```
#### 使用lodash来获取unique user id在一个数组中
```js
const userIds = _.uniq(_.map(getState().posts, "userId"));
```
上面_.map 的方法，获取了posts中所有的userId，并返回到一个array里
#### forEach的方法中无法使用Async
```js
userIds.forEach(id=>dispatch(fetchUser(id)));
```
上面的可以改为
```js
await Promise.all(userIds.map(id=>dispatch(fetchUser(id))))
```
#### 使用Action Creators in Action Creators!
```js
import _ from "lodash";
import jsonplaceholder from "../apis/jsonPlaceholder";

export const fetchPostsAndUser = () => async (dispatch, getState) => {
  await dispatch(fetchPosts());
  const userIds = _.uniq(_.map(getState().posts, "userId"));
  userIds.forEach((id) => dispatch(fetchUser(id)));
};

export const fetchPosts = () => async (dispatch) => {
  const response = await jsonplaceholder.get("/posts");

  dispatch({ type: "FETCH_POSTS", payload: response.data });
};

export const fetchUser = (id) => async (dispatch) => {
  const response = await jsonplaceholder.get(`/users/${id}`);

  dispatch({ type: "FETCH_USER", payload: response.data });
};
```
#### lodash chain
```js
const userIds = _.uniq(_.map(getState().posts, "userId"));
  userIds.forEach((id) => dispatch(fetchUser(id)));
```
可以使用lodash chain将以上改写，注意
- chain()里填的参数将作为参数传给下一层调用的方法
- chain的最末尾需要 .value()方法来激活整个chain的执行
```js
_.chain(getState().posts)
    .map('userId')
    .uniq()
    .forEach(id=>dispatch(fetchUser(id)))
    .value();
```

## Section 20 React Router
mkdir streams
npm install --save react-router-dom
### React Router Family
React-router: Core navigation lib - we don't install this manually
react-router-dom: Navigation for dom-based apps (we want this!)
react-router-native: Navigation for react-native apps
react-router-redux: Bindings between Redux and React Router (not necessary)

### Route
```js
<Route path="/" exact component={pageone} />
```
对于一个地址，比如 localhost:3000/page/5,提取的path是"/page/5"，然后比对这个path对应的component，即包含了哪些component，比如 
```js
<Route path="/" component={home} />
<Route path="/page" component={pageone} />
```
他会匹配到两个component，然后将对应的都渲染出来   
只有我们加入exact，即```exact={true}```的简写后，他才会只匹配到home

### Bad Navigation
- You add an <a /> tag to your application with href='/pagetwo' and click it
- Your browser makes a request to localhost:3000/pagetwo
- Development server responds with index.html file
- Browser receives index.html file, dumps old HTML file it was showing (including all of your React/Redux state data!)
- index.html file lists our JS files in script tags - browser downloads and executes these scripts
- Our app starts up

### Router Navigation Process
- User wants to navigate to another page in our app
- User clicks a 'Link' tag
- React Router prevents the browser from navigating to the new page and fetching new index.html file!
- URL still changes
- 'History' sees updated URL, takes URL and sends it to BrowserRouter
- BrowserRouter communicates the URL to Route components
- Route components rerender to show new set of components

### Different Router Types
- BrowserRouter: localhost:3000/pagetwo
  - Uses everything after the TLD (.com, .net) or port as the 'path'
- HashRouter: localhost:3000/#/pagetwo
  - Uses everything after a # as the 'path'
- MemoryRouter: localhost:3000/
  - Doesn't use the URL to track navigation
> VS Code中连续多选，ctrl+D

### Link
通过Link实现导航
```js
const Header = () => {
  return (
    <div className="ui secondary pointing menu">
      <Link to="/" className="item">
        Streamy
      </Link>
      <div className="right menu">
        <Link to="/" className="item">
          All Streams
        </Link>
      </div>
    </div>
  );
};
```
## Section 21 Authentication
90189678095-67fmno0ea6jnkglhm53m16qsv9gc0eq6.apps.googleusercontent.com
https://github.com/google/google-api-javascript-client/blob/master/docs/start.md
```html
<script src="https://apis.google.com/js/api.js"></script>
```
```
gapi.auth2.getAuthInstance().signIn()
gapi.auth2.getAuthInstance().signOut()
```
https://github.com/google/google-api-javascript-client

### 获得auth instance，来完成验证
通过gapi获得auth instance，继而得到sign in状态
```js
class GoogleAuth extends React.Component {
  state = { isSignedIn: null };

  componentDidMount() {
    window.gapi.load("client:auth2", () => {
      window.gapi.client
        .init({
          clientId:
            "90189678095-67fmno0ea6jnkglhm53m16qsv9gc0eq6.apps.googleusercontent.com",
          scope: "email",
        })
        .then(() => {
          this.auth = window.gapi.auth2.getAuthInstance();
          this.setState({ isSignedIn: this.auth.isSignedIn.get() });
          this.auth.isSignedIn.listen(this.onAuthChange);
        });
    });
  }

  onAuthChange = () => {
    this.setState({ isSignedIn: this.auth.isSignedIn.get() });
  };

  onSignInClick = () => {
    this.auth.signIn();
  };

  onSignOutClick = () => {
    this.auth.signOut();
  };

  renderAuthButton() {
    if (this.state.isSignedIn === null) {
      return null;
    } else if (this.state.isSignedIn === true) {
      return (
        <button onClick={this.onSignOutClick} className="ui red google button">
          <i className="google icon" />
          Sign Out
        </button>
      );
    } else {
      return (
        <button onClick={this.onSignInClick} className="ui red google button">
          <i className="google icon" />
          Sign In With Google
        </button>
      );
    }
  }

  render() {
    return <div>{this.renderAuthButton()}</div>;
  }
}
```
### Redux Setup
1. 创建actions
2. 创建reducers
```js
//index.js in reducers 
import { combineReducers } from "redux";

export default combineReducers({
  replaceMe: () => "hello",
});

```
3. 引入library
```js
import { Provider } from "react-redux";
import { createStore} from "redux";
import reducers from "./reducers";
```
4. 创建store
```js
const store = createStore(reducers);
```
5. 放置provider
```js
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```
### 使用Redux
1. 创建actions
```js
// index.js in actions
export const signIn = () => {
  return {
    type: "SIGN_IN",
  };
};

export const signOut = () => {
  return {
    type: "SIGN_OUT",
  };
};
```
2. GoogleAuth component中引入redux
- 引入library与action
```js
//GoogleAuth.js
import { connect } from "react-redux";
import { signIn, signOut } from "../actions";

export default connect(null, { signIn, signOut })(GoogleAuth);
```
3. component 改造并加入action creator
```js
onAuthChange = (isSignedIn) => {
    if (isSignedIn) {
      this.props.signIn();
    } else {
      this.props.signOut();
    }
  };

 componentDidMount() {
    window.gapi.load("client:auth2", () => {
      window.gapi.client
        .init({
          clientId:
            "90189678095-67fmno0ea6jnkglhm53m16qsv9gc0eq6.apps.googleusercontent.com",
          scope: "email",
        })
        .then(() => {
          this.auth = window.gapi.auth2.getAuthInstance();
          this.onAuthChange(this.auth.isSignedIn.get());
          //listen and wait for the change of isSignedIn status in the furture
          this.auth.isSignedIn.listen(this.onAuthChange);
        });
    });
  }
```
4. 创建reducer
```js
//authReducer.js
const INITIAL_STATE = {
  isSignedIn: null

};

export default (state = INITIAL_STATE, action) => {
  switch (action.type) {
    case "SIGN_IN":
      return {...state, isSignedIn: true};
    case "SIGN_OUT":
      return {...state, isSignedIn: false};
    default:
      return state;
  }
};
```
5. 导入新创建的reducer
```js
//index.js in reducers
import authReducer from "./authReducer";

export default combineReducers({
  auth: authReducer,
});
```
6. component改造并加入reducer
- 加入mapStateToProps
```js
//GoogleAuth.js
const mapStateToProps = (state) => {
  return { isSignedIn: state.auth.isSignedIn };
};

export default connect(mapStateToProps, { signIn, signOut })(GoogleAuth);
```
- 使用props，改变逻辑
```js
  renderAuthButton() {
    if (this.props.isSignedIn === null) {
      return null;
    } else if (this.props.isSignedIn === true) {
      return (
        <button onClick={this.onSignOutClick} className="ui red google button">
          <i className="google icon" />
          Sign Out
        </button>
      );
    } else {
      return (
        <button onClick={this.onSignInClick} className="ui red google button">
          <i className="google icon" />
          Sign In With Google
        </button>
      );
    }
  }
```
### 创建Type.js来统一对type的引用
为了避免action与reducer之间type容易出现的拼写错误，可以在action下，建立类型的js文件，然后分别在action与reducer中引用变量
```js
//type.js in actoins
export const SIGN_IN = "SIGN_IN";
export const SIGN_OUT = "SIGN_OUT";
```
```js
//index.js in actions
import { SIGN_IN, SIGN_OUT } from "./type";

export const signIn = () => {
  return {
    type: SIGN_IN,
  };
};

export const signOut = () => {
  return {
    type: SIGN_OUT,
  };
};

```
```js
//authReducer.js in reducers
import { SIGN_IN, SIGN_OUT } from "../actions/type";
const INITIAL_STATE = {
  isSignedIn: null,
};

export default (state = INITIAL_STATE, action) => {
  switch (action.type) {
    case SIGN_IN:
      return { ...state, isSignedIn: true };
    case SIGN_OUT:
      return { ...state, isSignedIn: false };
    default:
      return state;
  }
};

```
## Section 22 Redux dev Tools
https://github.com/zalmoxisus/redux-devtools-extension

http://localhost:3000/?debug_session=logged_in
http://localhost:3000/?debug_session=logged_out

## Section 23 Redux Form
npm install --save redux-form
https://redux-form.com/8.3.0/

### WireUp Redux Form
```js
import { combineReducers } from "redux";
import { reducer as formReducer } from "redux-form";
import authReducer from "./authReducer";

export default combineReducers({
  auth: authReducer,
  form: formReducer,
});

```
### Import redux-form to component
```js
// StreamCreate.js
import {Field, reduxForm } from 'redux-form';
export default reduxForm({
  form: 'streamCreate'
})(StreamCreate);
```
### Using Field
```jsx
renderInput(formProps) {
    return <input  onChange={formProps.input.onChange} value={formProps.input.value}>input</input>;
  }

  render() {
    return (
      <form>
        <Field name="title" component={this.renderInput} />
        <Field name="description" component={this.renderInput} />
        <div>StreamCreate</div>
      </form>
    );
  }
```
等效于
```js
 renderInput({input}) {
    return <input {...input} />;
  }
```
### Customize Field
```js
renderInput({ input, label }) {
    return (
      <div className="field">
        <label>{label}</label>
        <input {...input} />
      </div>
    );
  }

  render() {
    return (
      <form className="ui form">
        <Field name="title" component={this.renderInput} label="Enter Title" />
        <Field
          name="description"
          component={this.renderInput}
          label="Enter Description"
        />
        <div>StreamCreate</div>
      </form>
    );
  }
```
### Handle Submission
```js
onSubmit(formValues) {
  
  };
render() {
    return (
      <form className="ui form" onSubmit={this.props.handleSubmit(this.onSubmis)}>
        <Field name="title" component={this.renderInput} label="Enter Title" />
        <Field
          name="description"
          component={this.renderInput}
          label="Enter Description"
        />
        <button className="ui button primary">Submit</button>
        <div>StreamCreate</div>
      </form>
    );
  }
```

### Validate Input
```js
const validate = (formValues) => {
  const errors = {};
  if (!formValues.title) {
    errors.title = "You must enter a title";
  }

  if (!formValues.description) {
    errors.description = "You must enter a description";
  }

  return errors;
};

export default reduxForm({
  form: "streamCreate",
  validate,
})(StreamCreate);
```
add renderError
```js
  renderError = ({ error, touched }) => {
    if (touched && error) {
      return (
        <div className="ui error message">
          <div className="header">{error}</div>
        </div>
      );
    }
  };

  renderInput = ({ input, label, meta }) => {
    const className = `field ${meta.error && meta.touched ? "error" : ""}`;
    return (
      <div className={className}>
        <label>{label}</label>
        <input {...input} autoComplete="off" />
        {this.renderError(meta)}
      </div>
    );
  };

  render() {
    return (
      <form
        className="ui form error"
        onSubmit={this.props.handleSubmit(this.onSubmit)}
      >
        <Field name="title" component={this.renderInput} label="Enter Title" />
        <Field
          name="description"
          component={this.renderInput}
          label="Enter Description"
        />
        <button className="ui button primary">Submit</button>
      </form>
    );
  }
```