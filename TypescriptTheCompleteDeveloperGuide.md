## Section 1: Getting Started with Typescript
Typescipt = Javascript + Type System
Type System:
- Helps us catch errors during development
- Uses 'type annotations' to analyze our code
- Only active during development
- Doesn't provide any performance optimization
- It is best to think of Typescript as being like a friend sitting behind you while you are coding.

### Environment Setup:
- npm install -g typescript ts-node
- tsc index.ts
- ts-node index.ts

### Why typescript
```js
import axios from 'axios';

const url = 'https://jsonplaceholder.typicode.com/todos/1';

axios.get(url).then(response => {
  console.log(response.data);
});
```
interface: define the structure of an object
build interface, to avoid any typo and print:
```ts
import axios from 'axios';

const url = 'https://jsonplaceholder.typicode.com/todos/1';

interface Todo {
  id: number;
  title: string;
  completed: boolean;
}

axios.get(url).then(response => {
  //{ userId: 1, id: 1, title: 'delectus aut autem', completed: false }
  const todo = response.data as Todo;

  const ID = todo.id;
  const title = todo.title;
  const finished = todo.completed;

  console.log(`
    The Todo with ID: ${ID}
    Has a title of: ${title}
    Is it fininshed? ${finished}
  `);
});
```
catch more errors:
```ts
import axios from 'axios';

const url = 'https://jsonplaceholder.typicode.com/todos/1';

interface Todo {
  id: number;
  title: string;
  completed: boolean;
}

axios.get(url).then(response => {
  //{ userId: 1, id: 1, title: 'delectus aut autem', completed: false }
  const todo = response.data as Todo;

  const id = todo.id;
  const title = todo.title;
  const finished = todo.completed;

  logTodo(id, title, finished);

});

const logTodo = (id:number, title:string, completed:boolean) => {
  console.log(`
  The Todo with ID: ${id}
  Has a title of: ${title}
  Is it fininshed? ${completed}
`);
};
```

## Section 2: What is a Type System
"red" is
- a string
- is a value that has all the properties + methods that we assume that a string has
Type is 
- Easy way to refer to the different properties + functions that a value has
- In Typescript, ANY value has a type
Interface 
- define a new type
Types:
- Primitive Types: number, string, boolean, symbol, void, null, undefined
- Object Types: functions, classes, arrays, objects

## Section 3: Type Annotations in Action
Type Annotations
- Code we add to tell Typescript what type of value a variable will refer to
- We tell Typescript the type
- When to use:
  - when we declare a variable on one line then initialize it later
  - When we want a variable to have a type that can't be inferred
  - When a function returns the any type and we need to clarify the value
```ts
// When to use annotations
// 1. Function that returns the any type
const json = '{"x":10,"y":20}';
const coordinates:{x:number; y:number} = JSON.parse(json);
console.log(coordinates);

// 2. when we declare a variable on one line then initialize it later
let words = ['red', 'green', 'yellow'];
let foundWord:boolean;

for(let i = 0; i < words.length; i++) {
  if(words[i] === 'green'){
    foundWord = true;
  }
}

//3. Varialbe whose type cannot be inferred correctly
let numbersy = [-10, -1, 20];
let numberAboveZero:boolean | number= false;

for(let i = 0; i< numbersy.length; i++) {
  if(numbersy[i] > 0){
    numberAboveZero = numbersy[i];
  }
}
```


Type Inference
- Typescript tries to figure out what type of value a variable refers to 
- Typescript guesses the type
- When to use:
  - Always!
- If variable declaration and initialization are on the same line, we don't have to annotate the type


any Type: JSON.parse return any type
- Means TS has no idea what this is -can't check for correct property references
- Avoid variables with 'any' at all cost

## Section 4: Annotations With Functions and Objects
Type annotations for functions:
- code we add to tell Typescript what type of arguments a function will receive and what type of values it will return
```ts
const add = (a:number,b:number):number => {
  return a + b;
};
```

Type inference for functions:
- typescript tries to figure out what type of value a function will return
- No type inference for arguments
- Type inference works out output, but we won't use it!

void:返回为空，function不需要返回值
never：不会返回任何东西，function不会执行到最底部

### Destructuring with Annotations

```ts
const todayWeather = {
  date: new Date(),
  weather: 'SUNNY'
};

const logWeather = (forecast:{date:Date,weather:string}):void=>{
  console.log(forecast.date);
  console.log(forecast.weather);
};

const logWeatherDestruction = ({date,weather}:{date:Date,weather:string}):void=>{
  console.log(date);
  console.log(weather);
};

logWeather(todayWeather);
```
```ts
const profile = {
  name:'alex',
  age:20,
  coords:{
    lat:0,
    lng:15
  },

  setAge(age:number):void{
    this.age = age;
  }
};

const {age}:{age:number} = profile;
const {coords:{lat,lng}}:{coords:{lat:number,lng:number}} = profile;
```
## Section 5: Mastering Typed Arrays
初始化array时，需要加annotation，例如
```ts
const makers: string[] = [];
```
如果初始化不为空，依然可以省略annotation
```ts
const carMakers = ['ford', 'toyota', 'peugeot'];
const dates = [new Date(), new Date()];
const carsByMake = [['f150'], ['corolla'], ['308']];

// Help with map
carMakers.map((car: string): string => {
  return car.toLowerCase();
});

// Flexible types
const importantDates = [new Date(), '2030-10-10'];
const importantDatesB: (Date | string)[] = [new Date()];
importantDatesB.push(new Date());
```

## Section 6: Tuples in Typescript
Tuple: Array-like structure where each element represents some property of a record
```ts
const drink = {
  color: 'brown',
  carbonated: true,
  sugar: 40,
};

// Type alias
type Drink = [string, boolean, number];

const pepsi: Drink = ['brown', true, 40];
const sprite: Drink = ['clear', true, 40];
```

## Section 7: The All-Important Interface
Interfaces: creates a new type, describing the property names and value types of object
在没有使用interface以前：
```ts
const oldCivic = {
  name: 'civic',
  year: 2000,
  broken: true,
};

const printVehicle = (vehicle: {
  name: string;
  year: number;
  broken: boolean;
}): void => {
  console.log(`Name:${vehicle.name} 
  Year: ${vehicle.year}
  Broken? ${vehicle.broken}`);
};

printVehicle(oldCivic);
```
在使用interface后
```ts
interface Vehicle {
  name: string;
  year: number;
  broken: boolean;
}

const oldCivic = {
  name: 'civic',
  year: 2000,
  broken: true,
};

const printVehicle = (vehicle: Vehicle): void => {
  console.log(`Name:${vehicle.name} 
  Year: ${vehicle.year}
  Broken? ${vehicle.broken}`);
};

printVehicle(oldCivic);
```
interface中添加方法，并使用
```ts
interface Vehicle {
  name: string;
  year: number;
  broken: boolean;
  summary(): string;
}
const oldCivic = {
  name: 'civic',
  year: new Date(),
  broken: true,
  summary(): string {
    return `Name:${this.name} 
    Year: ${this.year}
    Broken? ${this.broken}`;
  },
};

const printVehicle = (vehicle: Vehicle): void => {
  console.log(vehicle.summary());
};

printVehicle(oldCivic);

```
做为符合interface的object，它必须满足interface里的所有key-value pair要求，object中多余的key-value pair，ts并不会检查
```ts
interface Reportable {
  summary(): string;
}

const oldCivic = {
  name: 'civic',
  year: new Date(),
  broken: true,
  summary(): string {
    return `Name:${this.name} 
    Year: ${this.year}
    Broken? ${this.broken}`;
  },
};

const printVehicle = (vehicle: Reportable): void => {
  console.log(vehicle.summary());
};

printVehicle(oldCivic);
```
General Strategy for reusable code in TS:
- Create functions that accept arguments that are typed with the interfaces
- Objects/classes can decide to 'implement' a given interface to work with function
```ts
interface Reportable {
  summary(): string;
}

const oldCivic = {
  name: 'civic',
  year: new Date(),
  broken: true,
  summary(): string {
    return `Name:${this.name} 
    Year: ${this.year}
    Broken? ${this.broken}`;
  },
};

const softdrink = {
  color:'brown',
  carbonated: true,
  sugar: 40,
  summary():string{
    return `My drink has ${this.sugar} grams of sugar`;
  }
}

const printSummary = (item: Reportable): void => {
  console.log(item.summary());
};

printSummary(oldCivic);
printSummary(softdrink);
```

## Section 8: Building Functionality with Classes
Class
- blueprint to create an object with some fields(values) and methods(functions) to represents a 'thing'

### Basic Class with methods only
```ts
class Vehicle {
  drive(): void {
    console.log('whuuu');
  }
  honk(): void {
    console.log('beep');
  }
}

const vehicle = new Vehicle();
vehicle.drive();
vehicle.honk();
```
### Basic inheritance
```ts
class Car extends Vehicle {
  drive(): void {
    console.log('vroom');
  }
}

const car = new Car();
car.drive();
car.honk();

```
### Modifier
- public: by default, can be called anywhere
- private: only be called by other methods in this class,在ts中使用的地方是，不希望被其它人called的方法
- protected: be called by other methods in this class, or by other methods in child classes
- cannot be changed in child classes
### Class with fields
```ts
class Vehicle {
  color: string;
  constructor(color: string) {
    this.color = color;
  }
  drive(): void {
    console.log('whuuu');
  }
  protected honk(): void {
    console.log('beep');
  }
}
```
color的初始化可以简写为
```ts
class Vehicle {
  constructor(public color: string) {}
  drive(): void {
    console.log('whuuu');
  }
  protected honk(): void {
    console.log('beep');
  }
}
```
Vehicle的子类可以继续写为
```ts
class Car extends Vehicle {
  constructor(public wheels: number, color: string) {
    super(color);
  }
  drive(): void {
    console.log('vroom');
  }

  startDrivingProcess(): void {
    this.drive();
    this.honk();
  }
}

const car = new Car(4, 'orange');
car.drive();
car.startDrivingProcess();
console.log(car.color);
console.log(car.wheels);
```
interfaces + classes = How we get really strong code resue in TS

## Section 9: Design Patterns with Typescript
npm i -g parcel-bundler