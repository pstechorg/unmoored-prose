+++
title = 'Vue Object Pool of One'
date = 2024-08-19T07:07:07+01:00
draft = false
+++

First things first, why would anyone want an object pool of size 1? Seems pretty ridiculous. Well, it is but I needed a pool to hang onto a wasm object so I didn't need to recreate it everytime it was needed.

