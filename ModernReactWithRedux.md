
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