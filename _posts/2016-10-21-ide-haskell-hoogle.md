---
layout: page
date: 2016-10-21 22:00:00
category: doc
title: "Hoogle online documentation/search"
order: 0
---

Install package `ide-haskell-hoogle`. Refer to [Atom Flight Manual](http://flight-manual.atom.io/using-atom/sections/atom-packages/) for details on installing packages.

Package requires `hoogle` executable, plus you will need to build hoogle database for hoogle itself to work.

## Setup

1. Install `hoogle` via cabal, stack or any package manager.

    Cabal:

    ```
    cabal install hoogle
    ```

    Stack:

    ```
    stack install hoogle
    ```

2. Build local hoogle database:

    Basic database:

    ```
    hoogle data
    ```

    Extended database **WARNING: this will take a lot of time and memory!**:

    ```
    hoogle data all
    ```

3. Point this package to `hoogle` executable, if it's not in `PATH`:

    Open Atom's settings, Packages, find ide-haskell-hoogle, then set 'Hoogle Path' to path to `hoogle` executable.
