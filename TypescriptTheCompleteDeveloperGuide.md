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
parcel index.html
npmjs.com

Could not find a declaration file for module 'faker':
Type definition file: an adaptor between typescript code and JavaScript library
@types/faker
npm i @types/faker

按住ctrl点击faker，能看到index.d.ts: type definiation file
> 想inspect一个变量，只要按住ctrl再点击就好
class中的object在使用前，必须要先初始化，例如
```ts
import faker from 'faker';

export class User {
  name: string;
  location:{
    lat:number;
    lng:number;
  };

  constructor(){
    this.name = faker.name.firstName();
    this.location = {
      lat: parseFloat(faker.address.latitude()),
      lng: parseFloat(faker.address.longitude())
    }

  }
}
```
> 在文件中export的，都可以在另外一个文件中通过import {}实现调用

### export default
如果一个文件中 export 跟了default例如
```ts
//User.ts
export default 'red';
```
那在另外一个文件import的，就是这个值
```ts
import color from './User';
console.log(color); // 'red'
```
在ts中强烈不建议用 export default

npm install @types/google.maps
/// <reference types="@types/google.maps" />

> Ctrl+P: 输入>fold level 2

### 使用interface来改写addMarker
原本的addMarker方法中，使用了基本相似的结构和代码
```ts
  addUserMarker(user: User): void {
    new google.maps.Marker({
      map: this.googleMap,
      position: {
        lat: user.location.lat,
        lng: user.location.lng,
      },
    });
  }

  addCompanyMarker(company: Company): void {
    new google.maps.Marker({
      map: this.googleMap,
      position: {
        lat: company.location.lat,
        lng: company.location.lng,
      },
    });
  }
```
使用mappable, 只ref共同的attribute
```ts
  addMarker(mappable: User | Company): void {
    new google.maps.Marker({
      map: this.googleMap,
      position: {
        lat: mappable.location.lat,
        lng: mappable.location.lng,
      },
    });
  }
```
使用interface
```ts
interface Item {
  location: {
    lat: number;
    lng: number;
  };
}

addMarker(item: Item): void {
    new google.maps.Marker({
      map: this.googleMap,
      position: {
        lat: item.location.lat,
        lng: item.location.lng,
      },
    });
  }
```
### Optional implement of Interface
implement an interface: tell ts to satisfy the interface
```ts
//Map.js
export interface Item {
  location: {
    lat: number;
    lng: number;
  };

  markerContent(): string;
  color: string;
}
```
```ts
//User.ts
import faker from 'faker';
import { Item } from './Map';

export class User implements Item {
  name: string;
  location: {
    lat: number;
    lng: number;
  };
  color: string = 'red';
  constructor() {
    this.name = faker.name.firstName();
    this.location = {
      lat: parseFloat(faker.address.latitude()),
      lng: parseFloat(faker.address.longitude()),
    };
  }

  markerContent(): string {
    return `User: ${this.name}`;
  }
}
```
instructions on how to be an argument to its methods
Typical Typescript File
- Interface definition for working with this class
- Class definition 

## Section 10: More on Design Patterns
tsc --init
### config tsconfig
in tsconfig.json
```json
  "outDir": "./build", 
  "rootDir": "./src", 
```
tsc -w

### add nodemon and concurrently, to build and run automatically
npm init -y
npm i nodemon concurrently
in package.json
```json
  "scripts": {
    "start:build": "tsc -w",
    "start:run": "nodemon build/index.js",
    "start":"concurrently npm:start:*"
  },
```
String is immutable: 你无法通过str[1]='y'这样来改变str的内容

### sort a string
Union Types: only access properties in both 
```ts
export class CharactersCollection {
  constructor(public data: string) {}

  get length(): number {
    return this.data.length;
  }

  compare(leftIndex: number, rightIndex: number): boolean {
    return (
      this.data[leftIndex].toLowerCase() > this.data[rightIndex].toLowerCase()
    );
  }

  swap(leftIndex: number, rightIndex: number): void {
    const characters = this.data.split('');
    const lefthand = characters[leftIndex];
    characters[leftIndex] = characters[rightIndex];
    characters[rightIndex] = lefthand;

    this.data = characters.join('');
  }
}
```
#### type guard
using to access a union type
typeof:
- narrow type of a value to a primitive type: number, string, boolean, symbol
instanceof:
- narraow down every other type of value: every other value that is created with a contructor function
```ts
class Sorter {
  constructor(public collection: number[] | string) {}

  sort(): void {
    const { length } = this.collection;

    for (let i = 0; i < length; i++) {
      for (let j = 0; j < length - i - 1; j++) {
        if (this.collection instanceof Array) {
          if (this.collection[j] > this.collection[j + 1]) {
            const lefthand = this.collection[j];
            this.collection[j] = this.collection[j + 1];
            this.collection[j + 1] = lefthand;
          }
        }
        if (typeof this.collection === 'string') {
          if (this.collection[j] > this.collection[j + 1]) {
            //TODO: SORT STRING
          }
        }
      }
    }
  }
}
```
#### 三元表达式简写
```ts
compare(leftIndex: number, rightIndex: number): boolean {
    return this.data[leftIndex] > this.data[rightIndex] ? true : false;
  }
```
简写为
```ts
compare(leftIndex: number, rightIndex: number): boolean {
    return this.data[leftIndex] > this.data[rightIndex];
  }
```
#### class 中的getter方法
```ts
export class NumberCollection {
  constructor(public data: number[]) {}
  get length(): number {
    return this.data.length;
  }
}
```
这样想access data的length，可以直接
```ts
const num = new NumberCollection([2,3,1]);
num.length;
```
### 将sorter作为方法加入到collection里
#### Abstract Class
- can't be used to create an object directly
- only be used as a parent class
- can contain real implementation for some methods
- the implementated methos can refer to other methods that don't actually exist yet
- can make child classes promise to implement other methods

把原本的Class改为Abstract Class，然后让NumberCollection等继承
```ts
interface Sortable {
  length: number;
  swap(leftIndex: number, rightIndex: number): void;
  compare(leftIndex: number, rightIndex: number): boolean;
}

export class Sorter {
  constructor(public collection: Sortable) {}

  sort(): void {
    const { length } = this.collection;

    for (let i = 0; i < length; i++) {
      for (let j = 0; j < length - i - 1; j++) {
        if (this.collection.compare(j, j + 1)) {
          this.collection.swap(j, j + 1);
        }
      }
    }
  }
}
```
变为
```ts
export abstract class Sorter {
  
  abstract length: number;
  abstract swap(leftIndex: number, rightIndex: number): void;
  abstract compare(leftIndex: number, rightIndex: number): boolean;
  
  sort(): void {
    const { length } = this;

    for (let i = 0; i < length; i++) {
      for (let j = 0; j < length - i - 1; j++) {
        if (this.compare(j, j + 1)) {
          this.swap(j, j + 1);
        }
      }
    }
  }
}
```

### Interface VS Abstract Classes
In common
- sets up a contract between different classes
Differences:
Interfaces:
- use when we have very different objects that we have to work together
- promotes loose coupling
Abstract Classes:
- use when we are trying to build up a definition of an object
- strongly couples classes together