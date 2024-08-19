+++
title = 'Vue Object Pool of One'
date = 2024-08-19T07:07:07+01:00
draft = false
+++

First things first, why would anyone want an object pool of size 1? Seems pretty ridiculous. 

Well, it is but I needed a pool to hang onto a wasm object. By doing this, the wasm object wouldn't need to recompile everytime I needed it.

First, I needed to create a global pool (object!?) in Vue. To do this, I leveraged a plugin because it worked nicely with the load already in place. It was straightforward enough - create an object with an install method.

```
export const kitobjectpool = {
  install(app, options) {
    let inst = null
    app.config.globalProperties.$getKitInstance = (kitobjectpool) => {
      inst = kitobjectpool
      return inst
    }
  }
}
```

