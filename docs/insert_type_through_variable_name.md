# Resolve Type if it exists

## Summary

"Dynamic Scoping" ?

``local test: does_this_type_exist``

## Motivation

Preventing Cylic Modules, by splitting Data and etc. is possible.
But not when you want to have an Object reference another Object and vice-versa since tables are addresses.

Addresses are pointers.

Currently you can export a type a THOUSAND times and pass it along Modules.

&nbsp;

Main reason is I saw a **few code**, where cyclic references existed.

Example, you have a Pots and Plants.

In the event you'd ever want to have ``Box.CurrentStorageZone`` which returns a ``Storage Zone``

```lua
local FlowerPot = {}
FlowerPot.__index

-- etc.

-- at the end you have

export type FlowerPotObject = typeof(FlowerPot.new())
```

```lua
local Plant = {}
Plant.__index

-- etc.

-- at the end you have

export type PlantObject = typeof(Plant.new())
```


Now if Pots is also the Module to create Plants. **Just an assumption** it would have to do ``require(Plants)``

If Plants wanted to store a property like ``Plant.FlowerPot`` it would have to do ``require(FlowerPot)`` **just for the type**.

And this is already a **cyclic issue**.

However if you could do this:
```lua
Plant.FlowerPot = nil :: typeFind<"PlantObject">
```

You can successfully have something like "Global Types". Aslong they're named the same

And that after ``::`` is nothing but text


## Design

```lua
-- Module 1
local test = {}

-- At this time, test.example would get "unknown"
test.example = nil :: typeFind<"TestType">

return test


-- Main
-- Module 1
type TestType = string

local test = require("PathToModule1")

-- This will resolve to TestType
-- Because the current scope has type TestType AND typeFind<"ARG"> is -> type "ARG"
test.example
```


## Drawbacks

Maybe is complex.

## Alternatives

Well, for instance if you look at the Pots and Plants _bad example_.

One can just do this within the Pots script if **they ever needed to actually put a Pot inside a Plant**

```lua
type PotObject -- We defined this here

-- a Plant retrieved
(Plant.Pot :: PotObject)
```

and that's just that
