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
Type Inference