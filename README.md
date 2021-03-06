[![Build Status](https://travis-ci.org/microstates/microstates.js.svg?branch=master)](https://travis-ci.org/microstates/microstates.js)
[![Coverage Status](https://coveralls.io/repos/github/microstates/microstates.js/badge.svg?branch=master)](https://coveralls.io/github/microstates/microstates.js?branch=master)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Gitter](https://badges.gitter.im/microstates/microstates.js.svg)](https://gitter.im/microstates/microstates.js?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)
[![Created by The Frontside](https://img.shields.io/badge/created%20by-frontside.io-blue.svg)](https://frontside.io)

# Microstates

Microstates is a functional runtime type system designed to ease state management in component based applications. It allows you to declaratively compose application state from atomic state machines.

By combining lazy execution, algebraic data types and structural sharing, we created a tool that provides a tiny API to describe complex data structures and provide a mechanism to change the value in an immutable way.

<details>
  <summary><strong>Table of Contents</strong></summary>
<!-- toc -->

- [Microstates](#microstates)
- [Features](#features)
- [What is a Microstate?](#what-is-a-microstate)
- [Types and Type Composition](#types-and-type-composition)
  - [Creating your own microstates](#creating-your-own-microstates)
  - [Array Microstates](#array-microstates)
  - [Object Microstates](#object-microstates)
- [Transitions](#transitions)
  - [Transitions for built-in types](#transitions-for-built-in-types)
  - [Type transitions](#type-transitions)
  - [Chaining transitions](#chaining-transitions)
  - [The `initialize` transition](#the-initialize-transition)
  - [The `set` transition](#the-set-transition)
  - [Transition scope](#transition-scope)
- [State Machines](#state-machines)
  - [Explicit Transitions](#explicit-transitions)
  - [Transition methods](#transition-methods)
  - [Immutable Object vs Immutable Data Structure](#immutable-object-vs-immutable-data-structure)
- [Framework Integrations](#framework-integrations)
- [`microstates` npm package](#microstates-npm-package)
  - [create(Type, value): Microstate](#createtype-value-microstate)
  - [from(any): Microstate](#fromany-microstate)
  - [map(fn, microstate): Microstate](#mapfn-microstate-microstate)
  - [use(middleware, microstate: Microstate): Microstate](#usemiddleware-microstate-microstate-microstate)
- [Observable Microstates](#observable-microstates)
- [The Vision of Microstates](#the-vision-of-microstates)
  - [Shared Solutions](#shared-solutions)
  - [Added Flexibility](#added-flexibility)
  - [Framework Agnostic Solutions](#framework-agnostic-solutions)
- [FAQ](#faq)
  - [What if I can't use class syntax?](#what-if-i-cant-use-class-syntax)
  - [What if I can't use Class Properties?](#what-if-i-cant-use-class-properties)
- [Run Tests](#run-tests)

<!-- tocstop -->
</details>

# Features

With Microstates added to your project, you get:

* 🍇 Composable type system
* 🍱 Reusable state atoms
* 💎 Pure immutable state transitions without writing reducers
* ⚡️ Lazy and synchronous out of the box
* 🦋 Most elegant way to express state machines
* 🎯 Transpilation free type system
* 🔭 Optional integration with Observables
* ⚛ Use in Node.js and browser

But, most imporantly, Microstates makes working with state fun.

**When was the last time you had fun working with state?**

For many, the answer is probably never, because state management in JavaScript is an endless game of compromises. You can choose to go fully immutable and write endless reducers. You can go mutable and everything becomes an observable. Or you can `setState` and lose the benefits of serialization and time travel debugging.

Unlike the view layer, where most frameworks agree on some variation of React's concept of components, none of the current crop of state management tools strike the same balance that React components introduced to the API.

React components have a tiny API. They are functional, simple and extremely reusable. The tiny API gives you high productivity for little necessary knowledge. Functional components are predictable and easy to reason about. They are conceptually simple, but simplicity hides an underlying architecture geared for performance. Their simplicity, predictability and isolation makes them composable and reusable.

These factors combined are what make React style components easy to work with and ultimately fun to write. A Tiny API abstracting a sophisticated architecture that delivers performance and is equaly useful on small and big projects is the outcome that we set out to achieve for state management with Microstates.

It's not easy to find the right balance between simplicity and power, but considering the importance of state management in web applications, we believe it's a worthy challenge. Checkout the [Vision of Microstates](#the-vision-of-microstates) section if you're interested in learning more about where we're going.

<!--
# Why Microstates?

Our tools affect how we solve problems and collaborate.

Two tools can serve the same purpose but foster completely different ecosystems. Take jQuery plugins and React components as examples. Both of these tools provided a way to add custom behaviour to a DOM element but they fostered very different communities.

In the React world today, we see many special purpose components that are easily combined to create sophisticated user experiences. These components tend to have few options but provide just the right API for you to build what you need.

jQuery plugins, on the other hand, offer endsless lists of options that are difficult get just right. In the jQuery ecosystem, libraries tend to be monolithic because plugins from different libraries often do not work well together.

As a result, in the React ecosystem we see components like [react-virtualized](https://github.com/bvaughn/react-virtualized) and [react-dnd](https://github.com/react-dnd/react-dnd) that are made by experienced React developers. These components save companies and developers millions of dollars in wasted development by eliminating the need for everyone to re-invent these components to build their user experiences.

The ability to leverage the experience of other developers in the form of published packages elevates our entire ecosystem. We call this _standing on the shoulders of giants_. Our goal is to bring this level of collaboration to state management.

## M in MVC

If you're a web developer and are using a framework, you might be wondering how Microstates will work within said framework. For now, I will say that we'll have an integration for each framework, but it's important that we're on the same page about the role of Microstates within your application. Once we have that, you'll see more ways to use Microstates than I can cover in this README.

Regardless of the kind of application you're building, your application is made of code that roughly represents data, converts user input into data and presents data to the user. This traditionally has been described as MVC pattern where M, model, is data that is persisted or accumulated through user actions or input. V, view, is how that data is presented to the user, often we describe with components and C, controller, which represents the constraints that exist on the user's actions.

Talking about MVC is a little passé in some communities, and understandbly so. Traditional MVC did not bring a lot of comfort to early adopters of web frameworks. Of the early adopters, only a few MVCish frameworks are left, but regardless of the popularity of the term, the architectural pattern is still a big part of our applications.


What we're seeing now is the discovery of what MVC looks like in modern web applications. One thing that most of us can agree on is that our views are now called components. Over the last 5 years, we saw a lot of competition in the view layer to make the most performant and ergonomic view building developer experience.

## Functional Models

The view boom was in big part ignited by the introduction of React. With React, came the introduction and mass adoption of functional programming ideas in the JavaScript ecosystem. Functional thinking brought a lot of simplicity to the view layer. It is conceptually simple - a component is a function that takes props and returns DOM. This simplicity helped developers learn React and has been adopted to a varied degree by most frameworks. The API that each frameworks exposes to their view is somewhat different but the general idea is the same.

Microstates brings the same kind of simplicity to the model layer. A Microstate is a functional model in a way that a component is a functional view. A component takes props and returns DOM; a Microstate takes value and returns state. DOM is a tree of element instances, state is a tree of model instances. A DOM tree is created by passing data to a root component. A state tree is created from a root type and value. A component is an abstraction that we use to manipulate the DOM tree. A microstate is an abstraction that we use to manipulate the state tree.

When you want to change what the user sees, you could imperatively manipulate the DOM elements with jQuery, but React taught us to change DOM declaratively by changing the data that the DOM reflects. In the same way, when you want to change the state, you must change the value and allow the state to be reflected. -->

# What is a Microstate?

A Microstate is just an object that is created from a value and a type. The value is just data, and the type is what defines how you can transition that data from one form into the next. Unlike normal JavaScript objects, microstates are 100% immutable and cannot be changed. They can only derive new immutable microstates through one of their type's transitions.

# Types and Type Composition

Microstates comes out of the box with 5 primitive types: `Boolean`, `Number`, `String`, `Object` and `Array`.

```js
import { create } from 'microstates';

let meaningOfLifeAndEverything = create(Number, 42);
meaningOfLifeAndEverything.state
//> 42

let greeting = create(String, 'Hello World');
greeting.state
//> Hello World

let isOpen = create(Boolean, true);
isOpen.state
//> true

let foo = create(Object, { foo: 'bar' });
foo.state
//> { foo: 'bar' }

let numbers = create(Array, [ 1, 2, 3, 4 ]);
numbers.state
//> [ 1, 2, 3, 4 ]
```

Apart from these basic types, every other type in Microstates is built by combining other types. So for example, to create a Person type you could define a JavaScript class with two properties: `name` which has type String and `age` which has type Number.

```js
class Person {
  name = String;
  age = Number;
}
```

Once you have a type, you can use that type to create as many people as your application requires:

```js
import { create } from "microstates";

let person = create(Person, { name: "Homer", age: 39 });
```

Every microstate created with a type of `Person` will be an object that looks like this:

```txt
+----------------------+
|                      |       +--------------------+
|  Microstate<Person>  +-name--+                    +-concat()->
|                      |       | Microstate<String> +-set()->
|                      |       |                    +-state: 'Homer'
|                      |       +--------------------+
|                      |
|                      |       +--------------------+
|                      +-age---+                    +-increment()->
|                      |       | Microstate<Number> +-decrement()->
|                      |       |                    +-set()->
|                      |       |                    +-state: 39
|                      |       +--------------------+
|                      |
|                      +-set()->
|                      +-state: Person { name: 'Homer', age: 39 }
+----------------------+
```

For the five built in types, Microstates automatically gives you transitions that you can use to change their value. You don't have to write any code to handle common operations.

## Creating your own microstates

Types can be combined with other types freely and Microstates will take care of handling the transitions for you. This makes it possible to build complex data structures that accurately describe your domain.

Let's define another type that uses the person type.

```js
class Car {
  designer = Person;
  name = String;
}

let theHomerCar = create(Car, {
  designer: { name: "Homer", age: 39 },
  name: "The Homer"
});
```

`theHomerCar` object will have the following shape,

```txt
+-------------------+           +----------------------+
|                   |           |                      |       +--------------------+
|  Microstate<Car>  |           |  Microstate<Person>  +-name--+                    +-concat()->
|                   |           |                      |       | Microstate<String> +-set()->
|                   +-designer--+                      |       |                    +-state: 'Homer'
|                   |           |                      |       +--------------------+
|                   |           |                      |
|                   |           |                      |       +--------------------+
|                   |           |                      +-age---+                    +-increment()->
|                   |           |                      |       | Microstate<Number> +-decrement()->
|                   |           |                      |       |                    +-set()->
|                   |           |                      |       |                    +-state: 39
|                   |           |                      |       +--------------------+
|                   |           |                      |
|                   |           |                      +-set()->
|                   |           |                      +-state: Person<{ name: 'Homer', age: 39 }>
|                   |           +----------------------+
|                   |
|                   |           +--------------------+
|                   +-name------+                    +-concat()->
|                   |           | Microstate<String> +-set()->
|                   |           |                    +-state: 'The Homer'
|                   |           +--------------------+
|                   |
|                   +-set()->
|                   +-state: Car<{ designer: Person<{ name: 'Homer', age: 39 }> }, name: 'The Homer' }>
+-------------------+
```

You can use the object dot notation to access sub microstates. Using the same example from above:

```js
theHomerCar.designer.state;
//> Person<{ name: 'Homer', age: 39 }>

theHomerCar.designer.age.state;
//> 39

theHomerCar.name.state;
//> The Homer
```

The state from sub microstates is also available on the parent object.

```js
theHomerCar.state
//> Car<{ designer: Person<{ name: 'Homer', age: 39 }>, name: 'The Homer' }>
```

## Array Microstates

Quite often it is helpful to describe your data as a collection of types. For example, a blog might have an array of posts. To do this, you can use the array of type notation `[Post]`. This signals that Microstates of this type represent an array whose members are each of the `Post` type.

```js
class Blog {
  posts = [Post];
}

class Post {
  id = Number;
  title = String;
}

let blog = create(Blog, {
  posts: [
    { id: 1, title: "Hello World" },
    { id: 2, title: "Most fascinating blog in the world" }
  ]
});

blog.posts[0].state;
//> Post<{ id: 1, title: 'Hello World' }>

blog.posts[1].state;
//> Post<{ id: 2, title: 'Most fascinating blog in the world' }>
```

When you're working with an array microstate, the shape of the Microstate is determined by the value. In this case, `posts` is created with two items which will, in turn, create a Microstate with two items. Each item will be a Microstate of type `Post`. If you push another item onto the `posts` Microstate, it'll be treated as a `Post`.

```js
let blog2 = blog.posts.push({ id: 3, title: "It is only getter better" });

blog2.posts[2].state;
//> Post<{ id: 3, title: 'It is only getter better' }>
```

Notice how we didn't have to do any extra work to define the state transition of adding another post to the list? That's the power of composition!

## Object Microstates

You can also create an object microstate with `{Post}`. The difference is that the collection is treated as an object. This can be helpful when create normalized data stores.

```js
class Blog {
  posts = { Post };
}

class Post {
  id = Number;
  title = String;
}

let blog = create(Blog, {
  posts: {
    "1": { id: "1", title: "Hello World" },
    "2": { id: 2, title: "Most fascinating blog in the world" }
  }
});

blog.posts["0"].state;
//> Post<{ id: 1, title: 'Hello World' }>

blog.posts["1"].state;
//> Post<{ id: 2, title: 'Most fascinating blog in the world' }>
```

Object type microstates have `Object` transitions, such as `assign`, `put` and `delete`.

```js
let blog2 = blog.posts.put("3", { id: 3, title: "It is only getter better" });

blog2.posts["3"].state;
//> Post<{ id: 3, title: 'It is only getter better' }>
```

# Transitions

Transitions are the operations that let you derive a new state from an existing state. All transitions return another Microstate. You can use state charts to visualize microstates. For example, the `Boolean` type can be described with the following statechart.

<img src="README/boolean-statechart.png" alt="Boolean Statechart" width="500" />

The `Boolean` type has a `toggle` transition which takes no arguments and creates a new microstate with the state that is opposite of the current state.

Here is what this looks like with Microstates.

```js
import { create } from "microstates";

let bool = create(Boolean, false);

bool.state;
//> false

let inverse = bool.toggle();

inverse.state;
//> true
```

> *Pro tip* Remember, Microstate transitions always return a Microstate. This is true both inside and outside the transition function. Using this convention to allow composition to reach crazy levels of complexity.

Let's use a `Boolean` in another type and see what happens.

```js
class App {
  name = String;
  notification = Modal;
}

class Modal {
  text = String;
  isOpen = Boolean;
}

let app = create(App, {
  name: "Welcome to your app",
  notification: {
    text: "Hello there",
    isOpen: false
  }
});

let opened = app.notification.isOpen.toggle();
//> Microstate<App>

opened.valueOf();
//> {
// name: 'Welcome to your app',
// notification: {
//   text: 'Hello there',
//   isOpen: true
// }}
```

Microstate transitions always return the whole object. Notice how we invoked the boolean transition `app.notification.isOpen`, but we didn't get a new Boolean microstate? Instead, we got a completely new App where everything was the same except for that single toggled value.

## Transitions for built-in types

The primitive types have predefined transitions:

* `Boolean`
  * `toggle(): Microstate` - return a Microstate with opposite boolean value
* `String`
  * `concat(str: String): Microstate` - return a Microstate with `str` added to the end of the current value
* `Number`
  * `increment(step = 1: Number): Microstate` - return a Microstate with number increased by `step`, default is 1.
  * `decrement(step = 1: Number): Microstate` - return a Microstate with number decreased by `step`, default is 1.
* `Object`
  * `assign(object): Microstate` - return a Microstate after merging object into current object.
  * `put(key: String, value: Any): Microstate` - return a Microstate after adding value at given key.
  * `delete(key: String): Microstate` - return a Microstate after removing property at given key.
* `Array`
  * `map(fn: (Microstate) => Microstate): Microstate` - return a Microstate with mapping function applied to each element in the array. For each element, the mapping function will receive the microstate for that element. Any transitions performed in the mapping function will be included in the final result.
  * `push(value: any): Microstate` - return a Microstate with value added to the end of the array.
  * `pop(): Microstate` - return a Microstate with last element removed from the array.
  * `shift(): Microstate` - return a Microstate with element removed from the array.
  * `unshift(value: any): Microstate` - return a Microstate with value added to the beginning of the array.
  * `filter(fn: state => boolean): Microstate` - return a Microstate with filtered array. The predicate function will receive state of each element in the array. If you return a falsy value from the predicate, the item will be excluded from the returned microstate.
  * `clear(): Microstate` - return a microstate with an empty array.

Many transitions on primitive type are similar to methods on original classes. The biggest difference is that transitions always return Microstates.

## Type transitions

Define the transitions for your types using methods. Inside of a transition, you can invoke any transitions you like on sub microstates.

```js
import { create } from "microstates";

class Person {
  name = String;
  age = Number;

  changeName(name) {
    return this.name.set(name);
  }
}

let homer = create(Person, { name: 'Homer', age: 39 });

let lisa = homer.changeName('Lisa');
```

## Chaining transitions

Transitions can be composed out of any number of subtransitions. This is often referred to as "batch transitions" or "transactions". Let's say that when we authenticate a session, we need to both store the token and indicate that the user is now authenticated. To do this, we can chain transitions. The result of the last operation will become new microstate.

```js
class Session {
  token = String;
}

class Authentication {
  session = Session;
  isAuthenticated = Boolean;

  authenticate(token) {
    return this
      .session.token.set(token)
      .isAuthenticated.set(true);
  }
}

class App {
  authentication = Authentication
}

let app = create(App, { authentication: {} });

let authenticated = app.authentication.authenticate('SECRET');

authenticated.valueOf();
//> { authentication: { session: { token: 'SECRET' }, isAuthenticated: true } }
```

## The `initialize` transition

Just as every state machine must begin life in its "start state", so too must every microstate begin life in the right state. For those cases where the start state depends on some logic, there is the `initialize` transition. The `initialize` transition is just like any other transition, except that it will be automatically called within create on every Microstate that declares one.

You can even use this mechanism to transition the microstate to one with a completely different type and value.

For example:

```js
class Person {
  firstName = String;
  lastName = String;

  initialize({ firstname, lastname } = {}) {
    let initialized = this;

    if (firstname) {
      initialized = initialized.firstName.set(firstname);
    }

    if (lastname) {
      initialized = initialized.lastName.set(lastname);
    }

    return initialized;
  }
}
```

## The `set` transition

The `set` transition is the only transition that is available on all types. It can be used to replace the value of the current Microstate with another value.

```js
import { create } from 'microstates'

let number = create(Number, 42).set(43);

number.valueOf()
//> 43
```

You can also use then `set` transition to replace the current Microstate with another Microstate. This is especially useful when building state machines because it allows you to change the type of the current Microstate. By changing the type, you're also changing available transitions and how the state is calculated.

```js
import { types } from 'microstates';

class Vehicle {
  weight = Number;
  towing = Vehicle;

  get isTowing() {
    return !!this.towing;
  }

  get towCapacity() {
    throw new Error('not implemented');
  }

  tow() {
    throw new Error('not implemented');
  }
}

class Car extends Vehicle {}

class Truck extends Vehicle {
  towCapacity = Number;

  tow(vehicle) {
    if (vehicle.weight.state < this.towCapacity.state) {
      return this.towing.set(vehicle);
    } else {
      throw new Error(`Unable to tow ${vehicle.weight.state} because it exceeds tow capacity of ${this.towCapacity.state}`);
    }
  }
}

class Friend {
  vehicle = Vehicle;
}

let prius = create(Car, { weight: 3000 });

let mustang = create(Car, { weight: 3500, towCapacity: 1000 });
let f150 = create(Truck, { weight: 5000, towCapacity: 13000 });

let rob = create(Friend).vehicle.set(mustang);
let charles = create(Friend).vehicle.set(f150);

rob.vehicle.tow(prius);
//> Error: not implemented

let result = charles.vehicle.tow(prius)

result.vehicle.state.isTowing
//> true
```

> *Pro tip*: Microstates will never require you to understand Monads in order to use transitions, but if you're interested in learning about the primitives of functional programming that power Microstates, you may want to checkout [funcadelic.js](https://github.com/cowboyd/funcadelic.js).

## Transition scope

Microstates are composable, and they work exactly the same no matter what other microstate they're a part of. For this reason, Microstate transitions only have access to their own transitions and the transitions of the microstates they contain. What they do _not_ have is access to their context. This is similar to how components work. The parent component can render a children and pass data to them, but the child components do not have direct access to the parent component. The same principle applies in Microstates, so as a result, it benefits from the same advantages of isolation and composability that make components awesome.

# State Machines

A state machine is a system that has a predefined set of states. At any given point, the state machine can only be in one of these states. Each state has a predefined set of transitions that can be derived from that state. These constraints are beneficial to application architecture because they provide a way to identify application state and suggest how the application state can change.

From its conception, Microstates was created to be the most convenient way to express state machines. The goal was to design an API that would eliminate the barrier of using state machines and allow for them to be composable. After almost two years of refinement, the result is an API that has evolved significantly beyond what we typically associate with code that expresses state machines.

[xstate](https://github.com/davidkpiano/xstate) for example, is a great specimen of the classic state machine API. It's a fantastic library and it addresses the very real need for state machines and statecharts in modern applications. For purposes of contrast, we'll use it to illustrate the API choices that went into Microstates.

## Explicit Transitions

Most state machine libraries focus on finding the next state given a configuration. For example, this [xstate](https://github.com/davidkpiano/xstate#finite-state-machines) declaration describes what state id to match when in a specific state.

```js
import { Machine } from 'xstate';

const lightMachine = Machine({
  key: 'light',
  initial: 'green',
  states: {
    green: {
      on: {
        TIMER: 'yellow',
      }
    },
    yellow: {
      on: {
        TIMER: 'red',
      }
    },
    red: {
      on: {
        TIMER: 'green',
      }
    }
  }
});
```

Microstates does not do any special state resolution. You explicitly declare what happens on a state transition. Here is what a similar state machine looks like in Microstates.

```js
class LightMachine {
  color = String;

  initialize({ color: 'green' } = {}) {
    return this.color.set(color);
  }

  timer() {
    switch (this.color.state) {
      case 'green': return this.color.set('yellow');
      case 'yellow': return this.color.set('red');
      case 'red':
      default:
        return this.color.set('green');
    }
  }
}
```

With Microstates, you explicitely describe what happens on transition and define the matching mechanism.

## Transition methods

`transitionTo` is often used by state machine libraries to trigger state transition. Here is an example with xstate library,

```js
const nextState = lightMachine.transition('green', 'TIMER').value;

//> 'yellow'
```

Microstates does not have such a method. Instead, it relies on vanilla JavaScript property lookup. The method invocation is equivalent to calling `transitionTo` with name of the transition.

```js
import { create } from 'microstates';

let lightMachine = create(LightMachine);

const nextState = lightMachine.timer();

nextState.color.state
//> 'yellow'
```

## Immutable Object vs Immutable Data Structure

When you create a state machine with xstate, you create an immutable object. When you invoke a transition on an `xstate` state machine, the value of the object is the ID of the next state. All of the concerns of immutable value change as a result of state change are left for you to handle manually.

Microstates treats value as part of the state machine. It allows you to colocate your state transitions with reducers that change the value of the state.

# Framework Integrations

* [React.js](https://github.com/microstates/react)
* [Ember.js](https://github.com/cowboyd/ember-microstates)
* Create a PR if you created an integration that you'd like to add to this list.
* Create an issue if you'd like help integrating Microstates with a framework

# `microstates` npm package

The `microstates` package provides the `Microstate` class and functions that operate on Microstate objects.

You can import the `microstates` package using:

```bash
npm install microstates

# or

yarn add microstates
```

Then import the libraries using:

```js
import Microstate, { create, from, map } from "microstates";
```

## create(Type, value): Microstate

The `create` function is conceptually similar to `Object.create`. It creates a Microstate object from type class and a value. This function is lazy, so it should be safe in most high performant operations even with complex and deeply nested data structures.

```js
import { create } from "microstates";

create(Number, 42);
//> Microstate
```

## from(any): Microstate

`from` allows the conversion of any POJO (plain JavaScript object) into a Microstate. Once you've created a Microstate, you can perform operations on all properties of the value.

```js
import { from } from "microstates";

from("hello world");
//Microstate<String>

from(42).increment();
//> Microstate<Number>

from(true).toggle();
//> Microstate<Boolean>

from([1, 2, 3]);
//> Microstate<Array<Number>>

from({ hello: "world" });
//> Microstate<Object>
```

`from` is lazy, so you can consume any deeply nested POJO and Microstates will allow you to perform transitions with it. The cost of building the objects inside of Microstates is paid whenever you reach for a Microstate inside. For example, `let o = from({ a: { b: c: 42 }})` doesn't do anything until you start to read the properties with dot notiation like `o.a.b.c`.

```js
from({ a: { b: c: 42 }}).a.b.c.increment().valueOf();
// { a: { b: { c: 43 }}}

from({ hello: [ 'world' ]}).hello[0].concat('!!!').valueOf();
// { hello: [ 'world!!!' ]}
```

## map(fn, microstate): Microstate

The `map` function invokes the function for each microstate in an array microstate. It is usually used to map over an array of microstate and return an array of components. The mapping function will receive each microstate in the array. You can invoke transitions on each microstate as you would usually.

```js
let numbers = create([Number], [1, 2, 3, 4]);

<ul>
  {map(number => (
    <li onClick={() => number.increment()}>{number.state}</li>
  ), numbers)}
</ul>
```

## use(middleware, microstate: Microstate): Microstate

The `use` function is used to add middleware to a microstate. This function will return a new microstate with the provided middleware installed.

```js
import { use, from } from "microstates";

let number = from(42);

const loggingMiddleware = next => (microstate, transition, args) => {
  console.log(`before ${transition.name} value is`, microstate.valueOf());
  let result = next(microstate, transition, args);
  console.log(`after ${transition.name} value is`, result.valueOf());
  return result;
}

let loggedNumber = use(loggingMiddleware, number);

loggedNumber.increment();
// before increment value is 42
// after increment value is 43
```

# Observable Microstates

By themselves microstates are purely functional. They have no builtin concept of identity,
time or sequencing. However, it is precisely because of these properties that they can be
used with almost any state management solution that does. In fact, Microstates comes bundled
out of the box with the ability to convert any microstate into a stream of transitions using
the the Observable API.

Microstates provides an easy way to convert a Microstate which represents a single value into
a Observable stream of values. This is done by passing a Microstate to `Observable.from` function.
This function will return a Observable object with a `subscribe` method. You can subscribe to the
stream by passing an observer to the subscribe function. Once you subscribe, you will synchronously
receive a microstate with middleware installed that will cause the result of transitions to be pushed
through the stream.

You should be able to use to any implementation of Observables that supports `Observer.from` using [symbol-observable](https://github.com/benlesh/symbol-observable). We'll use `RxJS` for our example.

```js
import { from } from "rxjs";
import { create } from "microstates";

let homer = create(Person, { firstName: "Homer", lastName: "Simpson" });

let observable = from(homer);

let last;
let subscription = observable.subscribe(next => {
  // capture the next microstate coming through the stream
  last = next;
});

last.firstName.set("Homer J");

last.valueOf();
//> { firstName: 'Homer J', lastName: 'Simpson' }
```

This mechanism provides the starting point for integration between the Observables ecosystem and Microstates.

# The Vision of Microstates

What if switching frameworks were easy? What if a company could build domain specific code that worked across frameworks? Imagine what it would be like if you tools stayed with you as you progressed in your career as an engineer. This is the world what we hope to create with Microstates.

## Shared Solutions

Imagine never having to write another normalized data store again because someone made a normalized data store Microstate that you can compose into your app's Microstate.

In the future (not currently implemented), you will be able to write a normalized data store like this,

```js
import Normalized from "future-normalized-microstate";

class MyApp {
  store = Normalized.of(Product, User, Category);
}
```

The knowledge about building normalized data stores is available in libraries like [Ember Data](https://github.com/emberjs/data), [Orbit.js](https://github.com/orbitjs/orbit), [Apollo](https://www.apollographql.com) and [urql](https://github.com/FormidableLabs/urql), yet many companies end up rolling their own because these tools are coupled to other stacks.

As time and resources permit, we hope to create a solution that will be flexible enough for use in most applications. If you're interested in helping us with this, please reach out.

## Added Flexibility

Imagine if your favourite Calendar component came with a Microstate that allowed you to customize the logic of the calendar without touching the rendered output. It might looks something like this,

```js
import Calendar from "awesome-calendar";

class MyCalendar extends Calendar.Model {
  // make days as events
  days = Day.of([Event]);

  // component renders days from this property
  get visibleDays() {
    return this.days.filter(day => day.status !== "finished");
  }
}

<Calendar.Component model={MyCalendar} />;
```

Currently, this is pseudocode, but Microstates was architected to allow for these kinds of solutions.

## Framework Agnostic Solutions

Competition moves our industry forward but consensus builds ecosystems.

Unfortunately, when it comes to the M(odel) of the MVC pattern, we are seeing neither competition nor consensus. Every framework has its own model layer that is not compatible with others. This makes it difficult to create truly portable solutions that can be used on all frameworks.

It creates lock-in that is detrimental to the businesses that use these frameworks and to the developers who are forced to make career altering decisions before they fully understand their choices.

We don't expect everyone to agree that Microstates is the right solution, but we would like to start the conversation about what a shared primitive for state management in JavaScript might look like. Microstates is our proposal.

In many ways, Microstates is a beginning. We hope you'll join us for the ride and help us create a future where building stateful applications in JavaScript is much easier than it is today.

# FAQ

## What if I can't use class syntax?

Classes are functions in JavaScript, so you should be able to use a function to do most of the same things as you would with classes.

```js
class Person {
  name = String;
  age = Number;
}
```

☝️ is equivalent to 👇

```js
function Person() {
  this.name = String;
  this.age = Number;
}
```

## What if I can't use Class Properties?

Babel compiles Class Properties into class constructors. If you can't use Class Properties, then you
can try the following.

```js
class Person {
  constructor() {
    this.name = String;
    this.age = Number;
  }
}

class Employee extends Person {
  constructor() {
    super();
    this.boss = Person;
  }
}
```

# Run Tests

```shell
$ npm install
$ npm test
```
