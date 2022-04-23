# React Hooks Cheat Sheet

A collection of useful React Hooks

---

### 1.React hook form

React form hook lets you handle all that heavy lifting with just some simple lines of code, your code will be much more concise and you can validate your data and handle errors and show them back to the user with the minimum effort on our side.

```jsx
import { useForm } from "react-hook-form"

export default function App() {
  const {
    register,
    handleSubmit,
    watch,
    formState: { errors },
  } = useForm()

  const onSubmit = (data) => console.log(data)

  console.log(watch("example")) // watch input value by passing the name of it

  return (
    /* "handleSubmit" will validate your inputs before invoking "onSubmit" */
    <form onSubmit={handleSubmit(onSubmit)}>
      {/* register your input into the hook by invoking the "register" function */}
      <input defaultValue="test" {...register("example")} />
      {/* include validation with required or other standard HTML validation rules */}
      <input {...register("exampleRequired", { required: true })} />
      {/* errors will return when field validation fails */}
      {errors.exampleRequired && <span>This field is required</span>}
      <input type="submit" />
    </form>
  )
}
```

### 2. useUpdateEffect

Sometimes in your React application you want to trigger a function only when a certain state is updated, this is basicaly useEffect only on update.

This can be achieved without using any third-party hooks, but it can make your code just a little bit messy.

```jsx
const mounted = useRef(false)

useEffect(() => {
  if (mounted.current) {
    // this function will not be called on the first render
  } else {
    mounted.current = true
  }
}, []) // it will be called everytime the dependecty list is updated
```

Here’s a cleaner way to do this, it is by using the react-use library which includes many other great hooks.

```jsx
import { useUpdateEffect } from "react-use"

const Home = () => {
  useUpdateEffect(() => {
    // will not run on the first render
    console.log("Runs only if the state updates")
  }, [fName])

  return (...)
}
```

### 3. useCopyToClipboard

The hook returns a state and a function to update the state and also copy that state to the clipboard.

```jsx
import { useCopyToClipboard } from "react-use"

const App = () => {
  const [text, setText] = useState("")
  const [state, copyToClipboard] = useCopyToClipboard()

  return (
    <div>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <button type="button" onClick={() => copyToClipboard(text)}>
        copy text
      </button>
    </div>
  )
}
```

### 4. useLocalStorage

Interacting with the browsers local storage could sometimes be frustrating because the localstorage can be accessed only when the component mounts, for that case you'd need to use the local storage only inside the useEffect hook, which could lead you to a messy code, that’s where the use of this hook comes in.

The useLocalStorage hook takes 2 arguments, the first one is the key of the item that you want to set in your local storage, and the second one is the initial value of that item, which means if that item is empty inside the users local storage, we will use that value as the initial value.

The hook also returns an array of 3 Items, first is the value of that item, second is a function to update the value, the third and the last one is a function to remove that item from local storage.

```jsx
import { useLocalStorage } from "react-use"

export default function App() {
  const [token, setToken, removeToken] = useLocalStorage("token", "foo") // initial value is foo

  return (
    <div>
      <div>Value: {token}</div>

      <button onClick={() => setToken("bar")}>bar</button>

      <button onClick={() => setToken("baz")}>baz</button>

      <button onClick={() => removeToken()}>Remove</button>
    </div>
  )
}
```

### 5. useHover

This hook is used to know whether a specific element is hovered or not, but the usage is a bit different from the other hooks.

You pass it a JSX element and it will return an array of two items, the first one is the element that you should use inside your JSX, the second one is the isHovered boolean state.

Here’s how:

```jsx
import { useHover } from "react-use"

const App = () => {
  // you can use the hovered argument from the function here
  const element = (hovered: boolean) => (
    <p>Sample text which is {hovered ? "hovered" : "not hovered"}</p>
  )
  // or you can get the hovered state from the hook
  const [textElement, isHovered] = useHover(element)

  return <div>{textElement}</div>
}
```

### 6. useIdle

This hook is used to know whether the user has gone idle or the user is active, you can pass it two arguments, first is the amount of milliseconds to pass to decide the user has gone idle and the second one is the initial state you can add true or false by default it is set to false

```jsx
import { useIdle } from "react-use"

const App = () => {
  const isIdle = useIdle(3000) // this will return true if the user has gone idle for more than 3 seconds

  return <div>{isIdle ? "User is idle" : "User is not idle"}</div>
}
```

### 7. useClickAway

This hook is useful when we want to trigger a function whenever we click outside of a specific UI component, for example we have an opened modal and we want to close the modal whenever the user clicks outside the modal, basically we are saying if the user clicks anywhere else which is not the modal, we want to run a function (in this case is closing th modal)

```jsx
import { useClickAway } from "react-use"

const App = () => {
  const ref = useRef(null)

  useClickAway(ref, () => {
    console.log("OUTSIDE CLICKED")
  })

  return (
    <div
      ref={ref}
      style={{
        width: 200,
        height: 200,
        background: "red",
      }}
    />
  )
}
```

### 8. useDebounce

The main use case of this hook is when we want to search some queries from an API but we don’t want to send the request everytime the user types a word, because in that case we send so many redundant requests to the API and overload the server which is not a very scalable solution.

What we do instead is we wait for the user to finish typing and then we send the request, which is ideal.

```jsx
import { useDebounce } from "react-use"

const App = () => {
  const [input, setInput] = useState("")
  const [loading, setLoading] = useState(false)

  useDebounce(
    () => {
      setLoading(true)
      // you should now send the request to the api
      // ...
      // ...
      setLoading(false)
    },
    500,
    // the amount of milliseconds to wait before firing the function
    [input] // the dependencies to watch for changes
  )

  return <input value={input} onChange={(e) => setInput(e.target.value)} />
}
```

### 9. useWindowSize

This hook returns the height and width of the screen and will automatically update whenever the screen size changes.

```jsx
import { useWindowSize } from "react-use"

const App = () => {
  // gets the height and width of the window
  const { width, height } = useWindowSize()

  return (
    <div>
      <div>width: {width}</div>

      <div>height: {height}</div>
    </div>
  )
}
```

### 10. useSwr

This hook makes it easy to handle requests when mounting a component.

The hook takes in two arguments, the first one is the url of the request, and the second one is the fetcher function which could be any function that sends a request to a server.

Then it gives you back the isValidating, error, and the response data states, the isValidating state works just like any loading state.

It will also give you a function called mutate() which you can use to refetch the API again, this is useful when you are muating some data for example you are deleting an todo from a todo list, after that action is successful you can call the mutate() function again to get back the latest data.

This example below simply fetchs some data when the component mounts.

```jsx
import useSWR from "swr"

const fetcher = (url: string) => fetch(url).then((r) => r.json())

const App = () => {
  const { data, mutate, error, isValidating } = useSWR(
    "https://jsonplaceholder.typicode.com/todos/1",
    fetcher
  )

  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <div>Todo title: {data.title}!</div>
}
```






































