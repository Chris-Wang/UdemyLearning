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
- use when we have very different objects that we have to work together`
- promotes loose coupling
Abstract Classes:
- use when we are trying to build up a definition of an object
- strongly couples classes together

## Section 11: Reusable Code
npm init -y
tsc --init
npm i nodemon concurrently

fs.readFileSync(path,[,options])
### Type Definitions files
incorrectly error report by ts, may cause by not installing type definition files
npm i @types/node
### Read and Parse file items
```ts
const matches = fs
  .readFileSync('football.csv', {
    encoding: 'utf-8',
  })
  .split('\n')
  .map((row: string): string[] => {
    return row.split(',');
  });
```
### Basic process in JS
```ts
import fs from 'fs';

const matches = fs
  .readFileSync('football.csv', {
    encoding: 'utf-8',
  })
  .split('\n')
  .map((row: string): string[] => {
    return row.split(',');
  });

console.log(matches);

let manUnitedWins = 0;
for (let match of matches) {
  if (match[1] === 'Man United' && match[5] === 'H') {
    manUnitedWins++;
  }
  if (match[2] === 'Man United' && match[5] === 'A') {
    manUnitedWins++;
  }
}

console.log(`Man United won ${manUnitedWins} games`);
```
### Refactor the code
#### Remove magic string using Enum
before
```ts
const homeWin = 'H';
const awayWin = 'A';
const draw = 'D';


let manUnitedWins = 0;
for (let match of matches) {
  if (match[1] === 'Man United' && match[5] === homeWin) {
    manUnitedWins++;
  }
  if (match[2] === 'Man United' && match[5] === awayWin) {
    manUnitedWins++;
  }
}

console.log(`Man United won ${manUnitedWins} games`);
```
#### Enum VS Objects
```ts
// enum- enumeration
enum MatchResult {
  HomeWin = 'H',
  AwayWin = 'A',
  Draw = 'D',
}
```
```ts
// object
const MatchResult =  {
  HomeWin:'H',
  AwayWin:'A',
  Draw:'D',
}
```
The purpose of the enum is to signal to other engineers that this is a collection of very closely related values, and we create a Type
```ts
const pringResult = ():MatchResult =>{
  if(match[5] == 'H'){
    return MatchResult.HomeWin;
  }
  return MatchResult.AwayWin;
}
```
#### Enum
适用于比较小的，fixed的，key-value pair
- Follow near-identical syntax rules as normal objects
- Creates an object with the same keys and values when converted from TS to JS
- Primary goal is to signal to other engineers that these are all closely related values
- Use whenever we have a small fixed set of values that are all closely related and known at compile time

### Extract File Read
为什么？因为我们以后可能将读取文件这步通过api来取代，那在这步写的逻辑就会被删掉；
因此将之抽取出来，不仅可以复用，而且还能保留。
before
```ts
import fs from 'fs';

const matches = fs
  .readFileSync('football.csv', {
    encoding: 'utf-8',
  })
  .split('\n')
  .map((row: string): string[] => {
    return row.split(',');
  });

console.log(matches);
```
after
```ts
//CsvFileReader.ts
import fs from 'fs';

export class CsvFileReader {
  data: string[][] = [];

  constructor(public filename: string) {}

  read(): void {
    this.data = fs
      .readFileSync(this.filename, {
        encoding: 'utf-8',
      })
      .split('\n')
      .map((row: string): string[] => {
        return row.split(',');
      });
  }
}
```
```ts
//index.ts
import { CsvFileReader } from './CsvFileReader';

const fileReader = new CsvFileReader('football.csv');
fileReader.read();
console.log(fileReader.data);
```
### Refactor enum and Type assertion
使用as，来将property 转变成任一Type
```ts
//MatchResult.ts
export enum MatchResult {
  HomeWin = 'H',
  AwayWin = 'A',
  Draw = 'D',
}

```
```ts
//CsvFileReader.ts
import { MatchResult } from './MatchResult';
export class CsvFileReader {
  data: string[][] = [];
  read(): void {
    this.data = fs
    ...
    .map((row: string[]): any => {
        return [
          dateStringToDate(row[0]),
          row[1],
          row[2],
          parseInt(row[3]),
          parseInt(row[4]),
          row[5] as MatchResult, // 'H','D','A'
          row[6],
        ];
      });
  }
}

```
### Refactor the data array, parse string to different types using tuple
1. Define the tuple as a new type
```ts
type MatchData = [Date, string, string, number, number, MatchResult, string];
```
2. Convert the row of string into the appropriate types
```ts
export class CsvFileReader {
  data: MatchData[] = [];

  constructor(public filename: string) {}

  read(): void {
    this.data = fs
      .readFileSync(this.filename, {
        encoding: 'utf-8',
      })
      .split('\n')
      .map((row: string): string[] => {
        return row.split(',');
      })
      .map((row: string[]): MatchData => {
        return [
          dateStringToDate(row[0]),
          row[1],
          row[2],
          parseInt(row[3]),
          parseInt(row[4]),
          row[5] as MatchResult,
          row[6],
        ];
      });
  }
}
```
### Refactor CsvFileReader
1. 改写helper function:注意这种写法
```ts
 read(): void {
    this.data = fs
      .readFileSync(this.filename, {
        encoding: 'utf-8',
      })
      .split('\n')
      .map((row: string): string[] => {
        return row.split(',');
      })
      .map((row: string[]): MatchData => {
        return [
          dateStringToDate(row[0]),
          row[1],
          row[2],
          parseInt(row[3]),
          parseInt(row[4]),
          row[5] as MatchResult,
          row[6],
        ];
      });
  }
```
改为
```ts
 read(): void {
    this.data = fs
      .readFileSync(this.filename, {
        encoding: 'utf-8',
      })
      .split('\n')
      .map((row: string): string[] => {
        return row.split(',');
      })
      .map(this.mapRow);
  }

  mapRow(row: string[]):MatchData{
    return [
      dateStringToDate(row[0]),
      row[1],
      row[2],
      parseInt(row[3]),
      parseInt(row[4]),
      row[5] as MatchResult,
      row[6],
    ];
  }
```
2. 抽取可以复用的部分，改写为abstract class
我们希望CsvFileReader可以用来读取所有的csv文件，而不再是football.csv，所以，我们把其中对于football.csv的parse部分，单独提了出来，可以复用的部分，留在了CsvFileReader
```ts
//CsvFileReader.ts
import fs from 'fs';
import { MatchResult } from './MatchResult';

type MatchData = [Date, string, string, number, number, MatchResult, string];

export abstract class CsvFileReader {
  data: MatchData[] = [];

  constructor(public filename: string) {}

  abstract mapRow(row: string[]):MatchData;

  read(): void {
    this.data = fs
      .readFileSync(this.filename, {
        encoding: 'utf-8',
      })
      .split('\n')
      .map((row: string): string[] => {
        return row.split(',');
      })
      .map(this.mapRow);
  }
}
```
```ts
//MatchReader.ts
import { CsvFileReader } from "./CsvFileReader";
import {dateStringToDate} from "./utils";
import {MatchData} from "./CsvFileReader";
import { MatchResult } from './MatchResult';

export class MatchReader implements CsvFileReader {
  mapRow(row: string[]):MatchData{
    return [
      dateStringToDate(row[0]),
      row[1],
      row[2],
      parseInt(row[3]),
      parseInt(row[4]),
      row[5] as MatchResult,
      row[6],
    ];
  }
}
```
#### Generics
- Like function arguments, but for types in class/function definition
- Allow us to define the type of a property/argument/return value at a future point
- Used heavily when writing reusable code
首先来看类似的function argument:
```ts
const addOne = (a:number):number =>{
  return a + 1;
}
const addTwo = (a:number):number =>{
  return a + 2;
}
const addThree = (a:number):number =>{
  return a + 3;
}
```

```ts
const add = (a:number, b:number):number =>{
  return a + b;
}
```
类似的，对于class我们也可以传入这样的argument
```ts
class HoldNumber {
  data:number;
}

class HoldString {
  data: string;
}
const holdNumber = new HoldNumber();
holdNumber.data = 123;

const holdString = new HoldString();
holdString = 'asd';
```
```ts
class HoldAnything<TypeOfData>{
  data: TypeOfData;
  add(a:TypeOfData):TypeOfData{
    return a;
  }
}

const holdNumber = new HoldAnything<number>();
holdNumber.data = 123;
const holdString = new HoldAnything<string>();
holdString = 'asd';
```
3. 讲abstract class中，属于match的部分提取出来，用generic取代
```ts
import fs from 'fs';

export abstract class CsvFileReader<T> {
  data: T[] = [];

  constructor(public filename: string) {}

  abstract mapRow(row: string[]): T;

  read(): void {
    this.data = fs
      .readFileSync(this.filename, {
        encoding: 'utf-8',
      })
      .split('\n')
      .map((row: string): string[] => {
        return row.split(',');
      })
      .map(this.mapRow);
  }
}
export abstract class CsvFileReader<T> {
  data: T[] = [];
  constructor(public filename: string) {}
  abstract mapRow(row: string[]): T;
}
```
```ts
import { CsvFileReader } from './CsvFileReader';
import { dateStringToDate } from './utils';
import { MatchResult } from './MatchResult';

type MatchData = [Date, string, string, number, number, MatchResult, string];

export class MatchReader extends CsvFileReader<MatchData> {
  mapRow(row: string[]): MatchData {
    return [
      dateStringToDate(row[0]),
      row[1],
      row[2],
      parseInt(row[3]),
      parseInt(row[4]),
      row[5] as MatchResult,
      row[6],
    ];
  }
}
```
### Refactor的另一种思路
这次我们使用interface来refactor，我们希望matchreader里包含的reader，可以是dataReader这个interface下面的，例如csvFileReader
1. 添加interface，增加MatchReader class
```ts
import { dateStringToDate } from './utils';
import { MatchResult } from './MatchResult';

type MatchData = [Date, string, string, number, number, MatchResult, string];

interface DataReader {
  read(): void;
  data: string[][];
}

export class MatchReader {
  matches: MatchData[] = [];
  constructor(public reader: DataReader) {}
  load(): void {}
}
```
2. 改写CsvFileReader class
```ts
import fs from 'fs';

export class CsvFileReader {
  data: string[][] = [];

  constructor(public filename: string) {}

  read(): void {
    this.data = fs
      .readFileSync(this.filename, {
        encoding: 'utf-8',
      })
      .split('\n')
      .map((row: string): string[] => {
        return row.split(',');
      });
  }
}

```
3. 补完MatchReader
```ts
import { dateStringToDate } from './utils';
import { MatchResult } from './MatchResult';

type MatchData = [Date, string, string, number, number, MatchResult, string];

interface DataReader {
  read(): void;
  data: string[][];
}

export class MatchReader {
  matches: MatchData[] = [];
  constructor(public reader: DataReader) {}
  load(): void {
    this.reader.read();
    this.matches = this.reader.data.map((row: string[]): MatchData => {
      return [
        dateStringToDate(row[0]),
        row[1],
        row[2],
        parseInt(row[3]),
        parseInt(row[4]),
        row[5] as MatchResult,
        row[6],
      ];
    });
  }
}

```
4. 在index.ts中调用
```ts
import { MatchReader } from './MatchReader';
import { MatchResult } from './MatchResult';
import { CsvFileReader } from './CsvFileReader';

const csvFileReader = new CsvFileReader('football.csv');
const fileReader = new MatchReader(csvFileReader);
fileReader.load();

let manUnitedWins = 0;
for (let match of fileReader.matches) {
  if (match[1] === 'Man United' && match[5] === MatchResult.HomeWin) {
    manUnitedWins++;
  }
  if (match[2] === 'Man United' && match[5] === MatchResult.AwayWin) {
    manUnitedWins++;
  }
}

console.log(`Man United won ${manUnitedWins} games`);
```
### Inheritance VS Composition
Inheritance:
abstract class: CSVFileReader
- class MatchReader
- class MovieReader
> Characterized by an 'is a' relationship between two classes

Composition:
MatchReader
- reader: interface DataReader
  - class CSVFileReader
  - class ApiReader
> Characterized by an 'has a' relationship between two classes

#### Inheritance
class Window
- open: boolean
- height: number
- width: number
- area():number
- toggleOpen():void

class Wall
- color: string
- height: number
- width: number
- area():number

then we can have an abstract class for these two
class Rectangle
- height: number
- width: number
- area():number

if we have circle window
class Circle
- radius: number
- area():number

class CircleWindow
- radius: number
- area():number
- toggleOpen():void

> 将可以复用的代码抽取出来，放置在abstract class中，再继承调用，并扩展
#### Composition
Window and CircleWindow have some same properties
class Wall
- color: string
- dimensions: Shape
- area():number

class Window
- open: boolean
- dimensions: Shape
- toggleOpen():void
- area():number

Shape Interface could be implemented by 
class Rectangle
- color: string
- height: number
- width: number
- area():number

class Circle
- radius: number
- area():number
>首先将interface写入class，在集成了interface的同时，也集成了interface的方法，class里的方法也可以直接调用interface里的方法
### Using static methods 
Using static methods to return an pre-configured instance of class
```ts
export class Summary {
  static winsAnalysisWithHtmlReport(team: string): Summary {
    return new Summary(new WinsAnalysis('Man United'), new HtmlReport());
  }

  constructor(public analyzer: Analyzer, public outputTarget: OutputTarget) {}
  buildAndPrintReport(matches: MatchData[]): void {
    const output = this.analyzer.run(matches);
    this.outputTarget.print(output);
  }
}
```
```ts
export class MatchReader {
  static fromCsv(filename: string): MatchReader {
    return new MatchReader(new CsvFileReader(filename));
  }

  matches: MatchData[] = [];
  constructor(public reader: DataReader) {}
  load(): void {
    this.reader.read();
    this.matches = this.reader.data.map((row: string[]): MatchData => {
      return [
        dateStringToDate(row[0]),
        row[1],
        row[2],
        parseInt(row[3]),
        parseInt(row[4]),
        row[5] as MatchResult,
        row[6],
      ];
    });
  }
}
```