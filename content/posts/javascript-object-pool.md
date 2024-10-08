+++
title = 'Vue Object Pool'
date = 2024-08-19T07:07:07+01:00
draft = false
+++

First things first, why would anyone want an object pool in Vue? I needed a pool to hang onto a wasm objects. By doing this, the wasm objects wouldn't need to recompile everytime they are leveraged. All I really needed was a reference to the objects that wouldn't disapear - this ensures that garbage collection doesn't clean up the compiled wasm files.

First, I needed to create a global pool in Vue. I leveraged a plugin because it fit nicely with some code already in place. It was straightforward enough - create an object with an install function.

```
export const objectpool = {
  install(app, options) {
    let objInstances = []
    app.config.globalProperties.$setObjectPoolInstance = (key, instObj) => {
      objInstances[key] = instObj
    }
    app.config.globalProperties.$getObjectPoolInstance = (key) => {
      if (objInstances[key] === undefined) {
        return objInstances[key]
      }
      return null
    }
    app.config.globalProperties.$inObjectPoolInstance = (key) => {
      if (objInstances[key] === undefined) {
        return true
      }
      return false
    }
  }
}
```
I created 3 global functions here to leverage.

$setObjectPoolInstance - Inserts an instance object using a particular key.

$getObjectPoolInstance - Retreive an object from the pool using the key.

$inObjectPoolInstance - Do we already have an object with the key name in the pool?

We're ready to use the plugin in our app. Just import the object into main.ts and use app.use to leverage it.
```
import { createApp } from 'vue'
import { objectpool } from './objectpool'

/* ... */
const app = createApp({
  /* ... */
})
/* ... */
app.use(objectpool)
```

Now we can call the functions in our components.
```
async mounted() {
    this.wasmInstance = await wasmCode.load()
    if (!this.$inObjectPoolInstance("wasmObject")) {
      this.$setObjectPoolInstance("wasmObject", this.wasmInstance)
    }
  }
  ```
That's it. Now, on initial load of the component you will see the wasm object download and compile. Everytime after that, it will just be ready to use!

