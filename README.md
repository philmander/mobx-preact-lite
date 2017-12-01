Mobx Preact Lite
================

This is an adaptation of the `observer` module (including all relevant tests) from
 [Mobx Preact](https://github.com/nightwolfz/mobx-preact),
made specifically for Preact with some features stripped out:

* Dev tools integration
* Error reporting
* Render reporting

**Provider/inject** features are also not implemented (yet).

If you would like to use this module with any of these missing features
re-integrated, please create an issue or PR.

## Installation

```
npm install mobx-preact-lite --save
```

```javascript
import {observer} from 'mobx-preact-lite';
```

## API documentation

Docs are adapted from [Mobx Preact](https://github.com/nightwolfz/mobx-preact)

### observer(componentClass)

Function (and decorator) that converts a Preact component class or
stand-alone render function into a reactive component, which tracks
which observables are used by `render` and automatically re-renders the
component when one of these values changes.
See the [MobX](https://mobxjs.github.io/mobx/refguide/observer-component.html)
documentation for more details.

```javascript
import {observer} from "mobx-preact-lite";

// ---- ES5 syntax ----

const TodoView = observer(React.createClass({
    displayName: "TodoView",
    render() {
        return <div>{this.props.todo.title}</div>
    }
}));

// ---- ES6 syntax ----

const TodoView  = observer(class TodoView extends React.Component {
    render() {
        return <div>{this.props.todo.title}</div>
    }
})

// ---- ESNext syntax with decorators ----

@observer class TodoView extends React.Component {
    render() {
        return <div>{this.props.todo.title}</div>
    }
}

// ---- or just use a stateless component function: ----

const TodoView = observer(({todo}) => <div>{todo.title}</div>)
```

### `Observer`

`Observer` is a Preact component, which applies `observer` to an anonymous region in your component.
It takes as children a single, argumentless function which should return exactly one React component.
The rendering in the function will be tracked and automatically re-rendered when needed.
This can come in handy when needing to pass render function to external components (for example the React Native listview), or if you
dislike the `observer` decorator / function.

Example:

```javascript
class App extends Component {
  render() {
     return (
         <div>
            {this.props.person.name}
            <Observer>
                {() => <div>{this.props.person.name}</div>}
            </Observer>
        </div>
     )
  }
}

const person = observable({ name: "John" })

React.render(<App person={person} />, document.body)
person.name = "Mike" // will cause the Observer region to re-render
```

### Server Side Rendering with `useStaticRendering`

When using server side rendering, normal lifecycle hooks of PReact components are not fired, as the components are rendered only once.
Since components are never unmounted, `observer` components would in this case leak memory when being rendered server side.
To avoid leaking memory, call `useStaticRendering(true)` when using server side rendering. This makes sure the component won't try to react to any future data changes.
