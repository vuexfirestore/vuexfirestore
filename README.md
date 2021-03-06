# VuexFirestore
> This is a fork from [Vuexfire](https://github.com/posva/vuexfire) to add function of loading firestore data by page.

> The master branch of this repo is NOT for firestore and is NOT the working branch of this fork
 
> This fork is on branch [vuexfirestore](https://github.com/vuexfirestore/vuexfirestore/tree/vuexfirestore)

> SSR ready Firebase binding for [Vuex](https://github.com/vuejs/vuex)

Supports only Vue 2, Vuex 2 and Firebase JavaScript SDK 2/3/4.
If you need an older version check the `v1` branch: `npm i -D vuexfire@v1`

**If you are looking for a version compatible with Firestore, [it's over here](https://github.com/posva/vuexfire/tree/firestore)**

## Installation


2. In module environments, e.g CommonJS:

``` bash
npm install vue firebase vuexfirestore --save
```

## Usage

Add the mutations to your root Store and make sure to define the property you
want to bind in the state first:

``` js
import { firebaseMutations } from 'vuexfirestore'
const store = new Vuex.Store({
  state: {
    todos: [], // Will be bound as an array
    user: null // Will be bound as an object
  },
  mutations: {
    // your mutations
    ...firebaseMutations
  }
})
```

It works with modules as well, but **you don't need to add the mutations there, only add them in the root `Store`**:
```js
const store = new Vuex.Store({
  modules: {
    cart: {
      state: {
        products: [], // Will be bound as an array
        user: null // Will be bound as an object
      }
    }
  },
  mutations: firebaseMutations
})
```

In order to use VuexFire, you have to enhance actions. This action enhancer
takes the actual action and enhances it with two additional parameters in the
context, `bindFirebaseRef` and `unbindFirebaseRef`:

```js
import { firebaseAction } from 'vuexfirestore'

const setTodosRef = firebaseAction(({ bindFirebaseRef, unbindFirebaseRef }, { ref }) => {
  // binding will automatically unbind any previously bound ref so you
  // don't need to unbind before binding over an existing bound key
  bindFirebaseRef('todos', ref)
  // it is possible to unbind a bound key at any time
  unbindFirebaseRef('user')
})
```

Access it as a usual piece of the state:

```js
const Component = {
  template: '<div>{{ todos }}</div>',
  computed: Vuex.mapState(['todos']),
  created () {
    this.$store.dispatch('setTodosRef', { ref: db.ref('todos') })
  }
}
```

To use pagination function, user two methods: bindMultipageRef, unbindMultipageRef, fetchNextMultipageRef in the same way as the bindFirebaseRef function
```js
import { firebaseAction } from 'vuexfirestore'

const setTodosRef = firebaseAction(({ bindMultipageRef, unbindMultipageRef }, { ref }) => {
  bindMultipageRef('todos', ref)
  unbindMultipageRef('user')
  fetchNextMultipageRef('todos')
})
```

## Browser support

VuexFire requires basic `WeakMap` support, which means that if you need to
support any of these browsers:

- IE < 11
- Safari < 7.1
- Android < 5.0

You'll have to include a polyfill. You can
use [atlassian/WeakMap](https://github.com/atlassian/WeakMap)

You can find more information about `WeakMap`
support [here](http://kangax.github.io/compat-table/es6/#test-WeakMap)

## How does it work?

VuexFire uses multiple global mutations prefixed by `vuexfire/` to call the
actual mutations to modify objects and arrays. It listens for updates to your
firebase database and commits mutations to sync your state. Thanks to the action
enhancer `firebaseAction`, it gets access to the local `state` and `commit` so
it works with modules too :+1:

## Examples

You can check out the examples by opening the html files in your browser, or check [this online Demo](https://jsfiddle.net/posva/6w3ks04x/)

## API

### firebaseMutations

This object contains VuexFire internal mutations. They are all prefixed by
`vuexfire/`. This object must be added in the root Store mutations object.

### bindFirebaseRef(key, ref[, options])

_Only available inside of an enhanced action_

Binds a firebase reference to a property in the state. If there was already
another reference bound to the same property, it unbinds it first.

#### options:

```js
{
  cancelCallback: Function, // Cancel callback passed to Firebase when listening for events
  readyCallback: Function, // Callback called once the data has been loaded. Useful for SSR
  errorCallback: Function, // Callback called when there is an error loading the data. Useful for SSR
  wait: Boolean, // (Arrays only) Should Vuexfire wait for the whole array to be populated. Defaults to true
}
```

`wait` can be set to true every time. It's useful to do pagination and SSR.

### unbindFirebaseRef(key)

_Only available inside of an enhanced action_

Unbinds a bound firebase reference to a given property in the state.

## License

[MIT](http://opensource.org/licenses/MIT)

## Support on Beerpay
Hey dude! Help me out for a couple of :beers:!

[![Beerpay](https://beerpay.io/posva/vuexfire/badge.svg?style=beer-square)](https://beerpay.io/posva/vuexfire)  [![Beerpay](https://beerpay.io/posva/vuexfire/make-wish.svg?style=flat-square)](https://beerpay.io/posva/vuexfire?focus=wish)
