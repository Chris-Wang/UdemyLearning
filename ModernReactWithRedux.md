
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