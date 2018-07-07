---
title: inferno-js, inferno-router, and inferno-context-api-store
date: 2018-07-02
category: ["Developer Guide"]
tags: ["Software Development", "Stateful Apps", "Inferno", "JavaScript"]
---

**The Problem:** Imagine that you have an app that has multiple states global to all your components. How do you distribute these states to the components that require them and how do you manage manipulating it? `inferno-context-api-store` allows you to do that with ease!

### Prerequisites to be able to follow along

- You should know how to use or have used either [InfernoJs](https://infernojs.org/) or [ReactJS](https://reactjs.org).
- [NodeJS](https://nodejs.org/en/) installed.

You can verify oif your `NodeJS` has been installed by doing the following:

```c
npm --version
node --version
```

You should see the version of your `npm` and `NodeJS`.

### Scope

In this post, I'll cover the following:

- [Creating inferno app](#creating-an-inferno-app).
- [Running the app](#running-the-app).
- [Setting up `inferno-context-api-store`'s store](#the-Provider).
- [Setting up the pages/routes](#setting-up-the-pages).
- [Navigating around the site](#navigating-around-the-site)
- [Connecting components to the store](#connecting-a-component-to-the-store).
- [Adding and dispatching action handlers and updateStore callback](#action-handlers).
- [Persisting states](#persiting-states).

### What is inferno-context-api-store?

`inferno-context-api-store` is a state management library that is compatible with `InfernoJS`. It offers easy and simple to use API and it was inspired by both [Redux](https://github.com/reduxjs/redux) and [Vuex](https://github.com/vuejs/vuex). Except that `inferno-context-api-store` uses [ReactJS' Context API](https://reactjs.org/docs/context.html) to manage the store. `React's Context API` is not avaiable in inferno out of the box, instead, there's a different package for it called [create-inferno-context](https://github.com/kurdin/create-inferno-context).

`inferno-context-api-store` also supports `persistent states` and `asynchronous actions` out of the box.

<a id="creating-an-inferno-app"></a>
### Creating an Inferno App

First, install [create-inferno-app](https://github.com/infernojs/create-inferno-app) package:

```c
npm install --global create-inferno-app
```

Once that's done, you can now create your project by executing the following commands:

```c
mkdir ~/Desktop/projects
cd ~/Desktop/projects
create-inferno-app my-app-name
```

On the commands above, we created a folder called `projects` on your `Desktop`. Then we `cd` _(change directory)_ onto that directory and we executed `create-inferno-app my-app-name` which will create the application for us. The app will be called `my-app-name` in this case but you can change this according to your preference.

What `create-inferno-app` is really doing is what other developers call **bootstraping** or simply getting started in the fastest possible way. When you executed the command `create-inferno-app my-app-name` you run a script that will create the directories and files and it will also install all the necessary dependencies that will help you get started.

<a id="running-the-app"></a>
### Running the app

Once the app has been installed, you can run the app by executing the following commands:

```c
cd my-app-name
npm install --save inferno-context-api-store
```

Once the install is done, you can run the app by executing `npm start`.

You should see an output similar to the following:

```c
Compiled successfully!

You can now view test-app in the browser.

  Local:            http://localhost:3000/
  On Your Network:  http://192.168.1.9:3000/

Note that the development build is not optimized.
To create a production build, use yarn build.
```

Now you can go to `http://localhost:3000/`, or whatever is shown in your terminal/CMD, and you will see the inferno default homepage.

To get started, look at the `src/App.js` or whatever inferno is telling you in the homepage.

You should see something like this:

```jsx
import './registerServiceWorker';
import { version, Component } from 'inferno';
import Logo from './logo';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <Logo width="80" height="80" />
          <h1>{`Welcome to Inferno ${version}`}</h1>
        </header>
        <p className="App-intro">
          To get started, edit <code>src/App.js</code> and save to reload.
        </p>
      </div>
    );
  }
}

export default App;
```

###### Did you notice the `HTML` inside the JavaScript?

That's called [JSX](https://reactjs.org/docs/introducing-jsx.html). You don't have to worry about it for now, just think of it as **the ability to write HTML inside of your Inferno app**.

### Experiencing any errors?

Try to delete the `package-lock.json` file on the root directory. Then do `npm install`. Once done, do `npm start` again.

Now to install `react-context-api-store`.

- On your terminal, hit `ctrl + c`.
- type in `npm install --save react-context-api-store`.

<a id="the-Provider"></a>
### The Provider

In order to create a store, you must use what's called a `Provider`. Which is basically an `HOC` that initializes the store. You must use it as the top most component of your app.

**Step 1**

First, we import it from the `inferno-context-api-store` package like so:

```jsx
import Provider from 'inferno-context-api-store';
```

**Step 2**

Modify the app by making the `Provider` the top most component like so:

```jsx
const store = {
  todos: [],
  username: 'April'
};

class App extends Component {
  render() {
    return (
      <Provider store={store}>
        <div className="App">
          <header className="App-header">
            <Logo width="80" height="80" />
            <h1>{`Welcome to Inferno ${version}`}</h1>
          </header>
          <p className="App-intro">
            To get started, edit <code>src/App.js</code> and save to reload.
          </p>
        </div>
      </Provider>
    );
  }
}
```

Notice that we passed in a `prop` called `store`. It is a required prop, and it contains all your application's global states. In this case, we have the `todos` and the `username` which has a default value of **April** (but in more complex apps you can have many).

The `store` is nothing especial, it's just an object and that's about it. You create a function that generates the `initial states` for you or whatever. As long as the end result is an object and you pass that object to the `Provider`.

Now your store is all setup. Yeah, it's as easy as that.

<a id="setting-up-the-pages"></a>
### Setting up the pages/routes

First, let's set up the pages we need, in this example, we'll only use two pages. One page to allow the user to change his `username` and another to allow the user to add new stuff to his `todos`.

**Step 1**

To do this, we'll need the help of a `router`. We'll use the package [inferno-router](https://github.com/infernojs/inferno/tree/master/packages/inferno-router). You can install it by executing `npm install inferno-router`.

**Step 2**

Modify the `src/App.js` to be like so:

```jsx
import { Component } from 'inferno';
import { HashRouter, Switch, Route } from 'inferno-router';
import Provider from 'inferno-context-api-store';

const store = {
  todos: [],
  username: 'April'
};

class App extends Component {
  render() {
    return (
      <Provider store={store}>
        <HashRouter>
          <Switch>
            <Route path="/" exact={true} component={Home} />
            <Route path="/todos" exact={true} component={Todos} />
          </Switch>
        </HashRouter>
      </Provider>
    );
  }
}

export default App;
```

I know there are a lot of other files there, but we are not really going to use any css here, until the last part of the tutorial. Right now, what we really need are the `src/App.js` and `src/index.js`, you can safely delete the rest from this point.

Now, when you refresh the page you'll see an error saying something like `'Todos' is not defined  no-undef`, this is the desired output, we'll fix it in the following step.

**Step 3**

Create a folder called `Home` inside the `src` folder, then create a file called `index.js` inside the `src/Home` folder.

The `index.js` file will be the `Home component`, for the mean time, it's just a regular component.

```jsx
import { Component } from 'inferno';

export default class Homepage extends Component {
  render () {
    return (
      <h1>Homepage!</h1>
    );
  }
}
```

**Step 4**

Create a folder called `Todos` inside the `src` folder, then create a file called `index.js` inside the `src/Todos` folder.

The `index.js` file will be the `Todos component`, for the mean time, it's just a regular component.

```jsx
import { Component } from 'inferno';

export default class Todospage extends Component {
  render () {
    return (
      <h1>Todos!</h1>
    );
  }
}
```

**Step 5**

On the `src/App.js`,

after:

```jsx
import Provider from 'inferno-context-api-store';
```

and before:

```jsx
const store = {
  todos: [],
  username: 'April'
};
```

add the following lines:

```jsx
import Home from './Home';
import Todos from './Todos';
```

If you refresh the page, you should see the **Homepage!** in `h1` tag. Then you can go to `http://localhost:3000/#/todos` and you'll see **Todos!**. This means that you have done well.

<a id="navigating-around-the-site"></a>
### Navigating around the site

Of course we don't want to navigate between pages by typing the url on the url bar. We need something that we can click on and navigate to where we want **WITHOUT RELOADING THE WHOLE PAGE**.

You must be thinking of using an `anchor` tag, that will work, but remember that we don't want to reload the whole page, that's the whole point of `SPAs`. We'll use a module from `inferno-router` called the `Link`, which is like an `anchor` tag that serves as a link that the user can click to navigate between pages, except it does not reload the whole page.

Modify the `JSX` in `src/Home/index.js` file, to look like so:

```jsx
<div>
  <h1>Homepage!</h1>
  <Link to="/todos">Go to Todos</Link>
</div>
```

To be able to use the `Link` module, we also need to import it from `inferno-router` like so:

```jsx
import { Link } from 'inferno-router';
```

Do the same for `src/Todos/index.js`, but instead, the `to` property of the `Link` would be equal to `/` like so:

```jsx
<Link to="/">Go to Home</Link>
```

Now, when you refresh the page you'll see an `anchor` tag that you can click on and you'll be directed to the corresponding page. Notice that the page does not refresh when you navigate between pages.

Now we don't want to hard code our navigation links in every pages that we will have, so, what we'll do is add that on to the `App.js` file before the `Switch`. So modify the `JSX` of that file like so:

```jsx
<Provider store={store}>
  <HashRouter>
    <div>
      <Link to="/todos">Go to Todos</Link> | <Link to="/">Go to Home</Link>
      <Switch>
        <Route path="/" exact={true} component={Home} />
        <Route path="/todos" exact={true} component={Todos} />
      </Switch>
    </div>
  </HashRouter>
</Provider>
```

Don't forget this too!

```jsx
import { Link } from 'inferno-router';
```

Before you refresh the site, remove the previous changes that we did in both `src/Home/index.js` and `src/Todos/index.js` related to the `Link` modules.

Now we have our navigation above just before the actual page content.

<a id="connecting-a-component-to-the-store"></a>
### Connecting a component to the store

To do this, we'll need the help of the `connect` module from `inferno-context-api-store`. Modify the `src/Home/index.js` file like so:

```jsx
import { Component } from 'inferno';
import { connect } from 'inferno-context-api-store';

class Homepage extends Component {
  render () {
    return (
      <div>
        <h1>Homepage!</h1>
      </div>
    );
  }
}

export default connect(state => {
  console.log('the state has:', state);

  return {};
})(Homepage);
```

Open up the console by right clicking on the page then click on inspect element, then click on the `console` tab on the upper side of the screen. Refesh the page and you'll see that there's a message there that says something like this:

```js
'the state has:' {todos: Array(0), username: "April"}
```

The `connect` module is a function that accepts two parameters.

1. `mapStateToProps`, is basically a callback that receives the store's updated state and returns an object coming from that state. The component then receives all these as `prop`. We only returned an empty object for now.
2. `mapActionsToProps` is an object that functions. The component will receive these functions as `prop` and can call them at will. Each function is called an `action handler` and receives `store` as it's first parameter. The `store` is an object that contains two things.
    1. `store.state` which is the store's updated store as of the moment the function was called.
    2. `store.updateStore` which is a function that you will call whenever you want to update a part of the store. It accepts an `object`, which is the states that you want to update, as it's first parameter and an optional second parameter which is a `callback function` that will receive the store's updated state as it's first parameter.

        **NOTE:** You should not update the state directly in your component by mutating it because it will not reflect onto other connected components, instead you should always fire an action and call `store.updateStore`.

Now, change the `App.js`, only the import part and replace it with these:

```jsx
export default connect(state => ({
  username: state.username,
  todos: state.todos
}))(Homepage);
```

Here, we are passing a `mapStateToProps function` to `connect` and we are immediately returning an `object`. Now, the `Homepage` component should receive these as part of its `props`. Let's do a `console log` to see the component's `props`. On the `render function`, add this before the `return` statement.

```jsx
console.log(this.props.username);
```

Now if you refresh the page, you should see your username in the console.

Let's display the user's username on the homepage. Change the `JSX` like so:

```jsx
<div>
  <h1>Hello, {this.props.username}!</h1>
</div>
```

Now you should see the _Hello, April!_ or whatever username you have.

<a id="action-handlers"></a>
### Action handlers

The `connect` module can accept a second parameter that is an object with methods. These methods will serve as your **action handlers**. You use them to update the store's state.

Now let's modify the `src/Home/index.js` to have a action handler called `changeUsername`. but before that, on our `Homepage class`, let's add a new method called `handleChangeUsername` which will handle changing username.

```jsx
handleChangeUsername = ev => {
  console.log(ev.target.target);
}
```

Then on the JSX, add the following on the next line after the `h1` tag.

```jsx
<p>Want to change your username?</p>
<input
  type="text"
  value={this.props.username}
  onInput={this.handleChangeUsername} />
```

If you refresh and check your console, you'll see that as you type you get the value of the `username` with the new key that you just pressed, however, the value of the `input` itself does not change, this is what's called `two way data binding` where you keep the value of the UI consistent with the value of the `state`, in this case, `username`, which is a state in our store. So how do we change the value of the state? Well, we need to update the store and we do that via action handlers.

Let's add our `changeUsername` action handler. Replace the whole `export default connect` line with the following:

```jsx
function changeUsername (store, updatedUsername) {
  console.log(store, updatedUsername);
}

export default connect(state => ({
  username: state.username,
  todos: state.todos
}), {
  changeUsername
})(Homepage);
```

Then on the `handleChangeUsername` method, do the following:

```jsx
handleChangeUsername = ev => {
  this.props.changeUsername(ev.target.value);
}
```

If you refresh the page and check your console, you'll see the value of the `store` and the `updatedUsername`. Note that **ALL** action handlers will receive the `store` as its first `argument`, the rest of the `arguments` will be whatever you passed to it, in this case we only have one, which is the `ev.target.value`, which we received and chose to call as `updatedUsername`. You can pass as many `arguments` to your `action handlers` as you need, but make sure to keep in mind that you will receive the store first, then the rest.

You can update the `store`'s state by calling the `updateStore` method on the `store`. Let's do that:

```jsx
function changeUsername (store, updatedUsername) {
  store.updateStore({
    username: updatedUsername
  });
}
```

In our case, we want to update the `username` state in the store. Now as you type on the input text, your `username` should change. The method `updateStore` can also accept an option second argument, which is a `callback function`, which will receive the `store's updated state` like so:

```jsx
function callback (updatedState) {
  console.log(updatedState);
}

function changeUsername (store, updatedUsername) {
  store.updateStore({
    username: updatedUsername
  }, callback);
}
```

There are many ways to add `action handlers` to your components. It can also be like this:

```jsx
function callback (updatedState) {
  console.log(updatedState);
}

export default connect(state => ({
  username: state.username,
  todos: state.todos
}), {
  changeUsername (store, updatedUsername) {
    store.updateStore({
      username: updatedUsername
    }, callback);
  }
})(Homepage);
```

or like this:

```jsx
function callback (updatedState) {
  console.log(updatedState);
}

export default connect(state => ({
  username: state.username,
  todos: state.todos
}), {
  changeUsername: (store, updatedUsername) => store.updateStore({
    username: updatedUsername
  }, callback)
})(Homepage);
```

How you do it is totally up to you.

<a id="persiting-states"></a>
### Persisting states

The `Provider` component can accept an optional property called `persist` which should be an object that has the following properties:

- `storage`. The storage of your choice, It should be synchronous and it should offer the following API:
  - `getItem`, which accepts the key as the first argument.
  - `setItem`, which accepts the key as the first argument and the value as the second argument.
  - `removeItem`, which accepts the key as the first argument.
- `statesToPersist`. A function that would be called when initializing the store. It accepts the `savedState`, which came from the storage, as the first argument. It should return an object of the states you want to persist and their values.

Now let's change our `App.js`'s `render method` to be like so:

```jsx
const persist = {
  storage: window.localStorage,
  statesToPersist (savedState) {
    /**
      * here, we are saying that if we have a
      * username stored on our savedState, we'll use that,
      * otherwise we'll use the value of store.username which is
      * the initial value
      */
    return {
      username: savedState.username || store.username
    };
  }
};

class App extends Component {
  render() {
    return (
      <Provider
        store={store}
        persist={persist}>
        <HashRouter>
          <div>
            <Link to="/todos">Go to Todos</Link> | <Link to="/">Go to Home</Link>
            <Switch>
              <Route path="/" exact={true} component={Home} />
              <Route path="/todos" exact={true} component={Todos} />
            </Switch>
          </div>
        </HashRouter>
      </Provider>
    );
  }
}
```

Now if you refresh the page and change your username, then refresh the page again, you'll see that your username is retained.

The entire source code of the example project is available at [https://github.com/aprilmintacpineda/inferno-context-api-store/tree/master/example](https://github.com/aprilmintacpineda/inferno-context-api-store/tree/master/example). Please do check it out.

### Other links related to inferno-context-api-store

- [GitHub](https://github.com/aprilmintacpineda/inferno-context-api-store).
- [NPM](https://www.npmjs.com/package/inferno-context-api-store).
- [Yarn](https://yarnpkg.com/en/package/inferno-context-api-store).

Corrections of any sort are always welcome. Thanks.