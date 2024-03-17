# React Tutorial for Java Delopers

- [Full Stack Development with Spring Boot 3 and React - Fourth Edition](https://learning.oreilly.com/library/view/full-stack-development/9781805122463/)
- 이 책에서 React 관련 부분을 정리한 글

## React

### Create Project

```
npm create vite@latest
npm install
npm run dev
```

### Useful ES6 Features

#### Arrow functions

```javascript
function (x) {
    return x * 2;
}

// ES6 arrow function
x => x * 2

// 위와 같은 익명함수는 호출 불가
// js에서 함수는 일급시민이므로 변수로 선언 가능
const calc = x => x * 2;

// 다음과 같이 변수 이름을 사용하여 함수를 호출할 수 있습니다.
calc(5); // returns 10
```

#### spread operator

```javascript
function MyForm() {
    const [user, setUser] = useState({
        firstName: '',
        lastName: '',
        email: ''
    });
    // Save input box value to state when it has been changed
    const handleChange = (event) => {
        setUser({
            ...user, [event.target.name]:
            event.target.value
        });
    }
    return (
        <form>
            <label>First name</label>
            <input type="text" name="firstName" onChange={handleChange} value={user.firstName}/>
            <br/>
            <label>Last name</label>
            <input type="text" name="lastName" onChange={handleChange} value={user.lastName}/>
            <br/>
            <label>Email</label>
            <input type="email" name="email" onChange={handleChange} value={user.email}/>
            <br/>
            <input type="submit" value="Submit"/>
        </form>
    );
};
```

- `{...user}`는 현재 user 상태의 복사본을 만듦
    - 이는 객체의 스프레드(spread) 연산자를 사용한 것으로, 기존 상태 객체의 모든 키와 값을 새 객체로 복사함
- `{...user, [event.target.name]: event.target.value}` 구문
    - 기존 사용자 상태 객체의 복사본에 사용자가 방금 변경한 특정 필드 값을 업데이트함

#### Template literals

```javascript
let person = {firstName: 'John', lastName: 'Johnson'};
let greeting = `Hello ${person.firstName} ${person.lastName}`;
```

#### Object destructuring

```javascript
const person = {
    firstName: 'John',
    lastName: 'Johnson',
    email: 'j.johnson@mail.com'
};

// You can destructure it using the following statement:
const {firstName, lastName, email} = person;
```

#### Classes and inheritance

```javascript
class Person {
    constructor(firstName, lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
}

class Employee extends Person {
    constructor(firstName, lastName, title, salary) {
        super(firstName, lastName);
        this.title = title;
        this.salary = salary;
    }
}
```

### JSX and styling

- JavaScript XML (JSX) is the syntax extension for JavaScript

```javascript
// we can access a component’s props when using JSX:
function App(props) {
    return <h1>Hello World {props.user}</h1>;
}

<Hello count={2 + 2}/>

// two examples of inline styling
// first one defines the style inside the div element:
<div style={{height: 20, width: 200}}>
    Hello
</div>

// second example creates a style object first
const divStyle = {color: 'red', height: 30};
const MyComponent = () => (
    <div style={divStyle}>Hello</div>
);
```

### Props and state

- Props and state are the input data for rendering a component
- The component is re-rendered when the props or state change.

#### Props

- inputs to components
    - they are a mechanism to pass data from a parent component to its child component
    - Props are JavaScript objects, so they can contain multiple key-value pairs.
- Props are immutable, so a component cannot change its props
    - Props are received from the parent component.
    - A component can access props through the props object that is passed to the function component as a parameter.

```javascript
function Hello() {
    return <h1>Hello John</h1>;
}

// we can pass a name to the Hello component by using props
<Hello user="John"/>

function Hello(props) {
    return <h1>Hello {props.user}</h1>;
}
```

#### State

- component state is an internal data store that holds information that can change over time
    - The state also affects the rendering of the component
    - When the state is updated, React schedules a re-render of the component
    - When the component re-renders, the state retains its latest values
    - State allows components to be dynamic and responsive to user interactions or other events.
- The state is created using the useState hook function
    - `const [state, setState] = React.useState(initialValue);`

#### Stateless components

- The React stateless component is just a pure JavaScript function that takes props as an argument and returns a React
  element.
  ```javascript
  function HeaderText(props) {
    return (
      <h1>
        {props.text}
      </h1>
    )
  }
  export default HeaderText;
  ```
- React provides **React.memo()**, which optimizes the performance of pure functional components.
  ```javascript
  import React, { memo } from 'react';
  function HeaderText(props) {
    return (
      <h1>
        {props.text}
      </h1>
    )
  }
  export default memo(HeaderText);
  ```
    - the component is rendered and memoized. In the next render, React renders a memoized result if the props are not
      changed.

### Conditional rendering

### React hooks

- Hooks allow you to use state and some other React features in functional components
    - Before hooks, you had to write class components if states or complex component logic were needed
- certain important rules for using hooks in React
    - You should always call hooks at the top level in your React function component
    - You shouldn’t call hooks inside loops, conditional statements, or nested functions
    - Hook names begin with the word use, followed by the purpose they serve.

#### useState

```javascript
// Correct -> Function is called when button is pressed
<button onClick={() => setCount(count + 1)}>

    // Wrong -> Function is called in render -> Infinite loop
    <button onClick={setCount(count + 1)}>
```

#### Batching

```javascript
import {useState} from 'react';

function App() {
    const [count, setCount] = useState(0);
    const [count2, setCount2] = useState(0);
    const increment = () => {
        setCount(count + 1); // No re-rendering yet
        setCount2(count2 + 1);
        // Component re-renders after all state updates
    }
    return (
        <>
            <p>Counters: {count} {count2}</p>
            <button onClick={increment}>Increment</button>
        </>
    );
};
export default App;
```

- From React version 18 onward, all state updates will be batched. If you don’t want to use batch updates in some cases,
  you can use the react-dom library’s flushSync API to avoid batching.

```javascript
const increment = () => {
    flushSync(() => {
        setCount(count + 1); // No batch update
    });
}
```

#### useEffect

- can be used to perform side effects in the React function component. The side effect can be, for example, a fetch
  request
    - `useEffect(callback, [dependencies])`
- dependency
    - 생략하면 각 렌더링 후에 useEffect콜백 함수가 호출
    - 지정하면 해당 컴포넌트가 변경되었을 때만
    - 빈 배열을 지정하면 컴포넌트가 처음 렌더링될 때만 호출

#### useRef

- The useRef hook returns a mutable ref object that can be used, for example, to access DOM nodes
    - `const ref = useRef(initialValue)`
- The ref object returned has a current property that is initialized with the argument passed (initialValue)
  ```javascript
  function App() {
      const inputRef = useRef(null);
      return (
          <>
              <input ref={inputRef} />
              <button onClick={() => inputRef.current.focus()}>
              Focus input
              </button>
          </>
      );
  }
  export default App;
  ```
    - we create a ref object called inputRef and initialize it to null
    - Then, we use the JSX element’s ref property and pass our ref object to it
    - Now, it contains our input element, and we can use the current property to execute the input element’s focus
      function
    - Now, when the button is pressed, the input element is focused:

#### Custom hooks

- hooks’ names should start with the use word, and they are JavaScript functions.

```javascript
// useTitle.js
function useTitle(title) {
    useEffect(() => {
        document.title = title;
    }, [title]);
}

export default useTitle;

//...
function App() {
    const [count, setCount] = useState(0);
    useTitle(`You clicked ${count} times`);
    return (
        <>
            <p>Counter = {count}</p>
            <button onClick={() => setCount(count + 1)}>
                Increment
            </button>
        </>
    );
};
export default App;
```

### The Context API

### Handling lists with React

```javascript
const arr = [1, 2, 3, 4];
const resArr = arr.map(x => x * 2); // resArr = [2, 4, 6, 8]
//...
return (
    <>
        <ul>
            {
                data.map((number) =>
                    <li>Listitem {number}</li>)
            }
        </ul>
    </>
);

// unique key
return (
    <>
        <ul>
            {
                data.map((number, index) =>
                    <li key={index}>Listitem {number}</li>)
            }
        </ul>
    </>
);
```

### Handling events with React

- you have to pass a function to the event handler instead of calling it
  ```javascript
  // Correct
  <button onClick={handleClick}>Press Me</button>

  // Wrong
  <button onClick={handleClick()}>Press Me</button>
  ```
- In React, you cannot return false from the event handler to prevent the default behavior.
    - Instead, you should call the event object’s preventDefault() method.

### Handling forms with React

```javascript
function MyForm() {
    const [text, setText] = useState('');
    // Save input element value to state when it has been changed
    const handleChange = (event) => {
        setText(event.target.value);
    }
    const handleSubmit = (event) => {
        alert(`You typed: ${text}`);
        event.preventDefault();
    }
    return (
        <form onSubmit={handleSubmit}>
            <input type="text" onChange={handleChange}
                   value={text}/>
            <input type="submit" value="Press me"/>
        </form>
    );
};
export default MyForm;
```

- React Developer Tools Components tab
    - type something into the input field, we can see how the value of the state changes, and we can inspect the current
      value of both the props and the state.

## TypeScript

```
npm install -g typescript
tsc --version
```

### union type

```typescript
type InputType = string | number;
let name: InputType = "Hello";
let age: InputType = 12;

////////////////
type Fuel = "diesel" | "gasoline" | "electric ";
type NoOfGears = 5 | 6 | 7;
type Car = {
    brand: string;
    fuel: Fuel;
    gears: NoOfGears;
}
```

### Using TypeScript features with React

#### State and props

- name, age를 받는 prop을 인자로 받는 컴포넌트

```typescript
function HelloComponent({name, age}) {
    return (
        <>
            Hello
    {
        name
    }
,
    you
    are
    {
        age
    }
    years
    old!
    < />
)
    ;
}

// render our HelloComponent and pass props to it:
function App() {
    return (
        <HelloComponent name = "Mary"
    age = {12}
    />
)
}

// 아래와 같이 type을 선언해서 사용할 수도 있음
type HelloProps = {
    name: string;
    age?: number; // age is optional
    fn: () => void; // function without arguments
    fn1: (msg: string) => void; // function with one argument
};
```

- FC(function component)
    - a standard React type
    - we can use with arrow functions.
    - This type takes a generic argument that specifies the prop type
      ```typescript
      const HelloComponent: React.FC<HelloProps> = ({ name, age }) => {
          return (
              <>
                Hello {name}, you are {age} years old!
              </>
          );
      };
      ```

#### Event

```typescript
<input
    type = "text"
onChange = {handleChange}
value = {name}
/>

const handleChange = (event) => {
    setName(event.target.value);
}

const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    setName(event.target.value);
} 
```

- [Forms and Events | React TypeScript Cheatsheets](https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/forms_and_events/)

## Rest Call

### async and await

```typescript
const doAsyncCall = async () => {
    const response = await fetch('http ://someapi .com');
    const data = await response.json();
    // Do something with the data
}

const doAsyncCall = async () => {
    try {
        const response = await fetch('http ://someapi .com');
        const data = await response.json();
        // Do something with the data
    } catch (err) {
        console.error(err);
    }
}
```

### Using the fetch API

```typescript
fetch('http://someapi.com',
    {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify(data)
    }
) // 2번째 인자는 optional
    .then(response => {
            if (response.ok)
            // Successful request -> Status 2XX
            else
            // Something went wrong -> Error response
        }
    )
    .then(data => console.log(data))
    .catch(error => console.error(error))
```

### Using the Axios library

`npm install axios`

```typescript
axios.get('htt://someap.com')
    .then(response => console.log(response))
    .catch(error => console.log(error))

axios.post('htt://someap.com', {newObject})
    .then(response => console.log(response))
    .catch(error => console.log(error))

const response = await axios({
    method: 'POST',
    url: 'https ://myapi.com/api/cars',
    headers: {'Content-Type': 'application/json'},
    data: {brand: 'Ford', model: 'Ranger'},
});

axios.get<{ items: Repository[] }>('htt://someap.com')
    .then(response => console.log(response))
    .catch(error => console.log(error))
```

### Using the React Query library

- 많이 사용되는 3ry party lib
    - React Query (https://tanstack.com/query), also known as Tanstack Query
    - SWR (https://swr.vercel.app/).

```typescript
npm
install
@tanstack/
react - query
@4
npm
install
axios
```

- React Query provides the QueryClientProvider and QueryClient components, which handle data caching.

```typescript
const queryClient = new QueryClient();

function App() {
    return (
        <>
            <QueryClientProvider client = {queryClient} >
            </QueryClientProvider>
            < />
    )
}
```

- React Query provides the useQuery hook function, which is used to invoke network requests

```typescript
const query = useQuery({queryKey: ['repositories'], queryFn: getRepositories})
```

- queryKey is a unique key for a query and it is used for caching and refetching data
- queryFn is a function to fetch data, and it should return a promise.
- The query object that the useQuery hook returns contains important properties, such as the status of the query:
  `const { isLoading, isError, isSuccess } = useQuery({ queryKey: ['repositories'], queryFn: getRepositories })`
- isLoading: The data is not yet available.
- isError: The query ended with an error.
- isSuccess: The query ended successfully and query data is available.