# State
```haskell
State s a
```
`State` is an Algebraic Data Type that abstracts away state management
associated when working with stateful computations.`State` is parameterized by
two types, a state `s` and a resultant `a`. The `a` may vary it's type, but
the `s` must be fixed to a type that is used by all related stateful
computations.

All `State` instances wrap a function of the form `s -> Pair a s` and can be
constructed by providing a function of this form. In order to get maximum
reuse of existing functions, a few construction helpers are available on
`State` constructor.

`State` is lazy and is required to be run at the edge with some initial state.
Three methods are available on the instance for running the `State` with a
given initial state. `runWith` will return a `Pair a s` with the state s on the
right and the resultant `a` on the left.

The other two are used for extracting either the state or resultant, unwrapping
the values from the `Pair` and discarding the unwanted portion. `evalWith` is
used when the resultant is wanted, while `execWith` is used to pull the state.

```js
const Pair = require('crocks/Pair')
const State = require('crocks/State')

const constant = require('crocks/combinators/constant')

const { get, put } = State

// toUpper :: String -> String
const toUpper =
  x => x.toUpperCase()

// putResultant :: String -> State String String
const putResultant = x =>
  put(x)
    .map(constant(x))

// standard construction
// State String String
State(s => Pair(toUpper(s), s))
  .runWith('nice')
//=> Pair('NICE', 'nice')

// construction helper
// State String String
get(toUpper)
  .runWith('nice')
//=> Pair('NICE', 'nice')

// combine states
get(toUpper)
  .chain(putResultant)
  .runWith('nice')
//=> Pair('NICE', 'NICE')

// pull resultant only
get(toUpper)
  .evalWith('nice')
//=> 'NICE'

// pull state only
get(toUpper)
  .execWith('nice')
//=> 'nice'
```

## Implements
`Functor`, `Apply`, `Chain`, `Applicative`, `Monad`

## Constructor Methods

### get
```haskell
State.get :: () -> State s s
State.get :: (s -> a) -> State s a
```

A construction helper that is used to access the state portion of `State`. To
make the state accessible, `get` will place the state in the resultant portion,
overwriting what was there previously.

`get` may be called with or without a function as an argument. When a function
is not provided, the state will be applied to the resultant as is. The state
will be mapped over any provided function that takes the same type as the
state, with the result deposited in the resultant.

```js
const { get } = require('crocks/State')
const chain = require('crocks/pointfree/chain')
const compose = require('crocks/helpers/compose')
const isNumber = require('crocks/predicates/isNumber')
const option = require('crocks/pointfree/option')
const prop = require('crocks/Maybe/prop')
const safe = require('crocks/Maybe/safe')

// propOr :: (String, (b -> Boolean), a) -> Object -> b
const propOr = (key, pred, def) =>
  compose(option(def), chain(safe(pred)), prop(key))

// safeNumber :: Object -> Number
const safeNumber =
  propOr('number', isNumber, 0)

get(safeNumber)
  .runWith({ number: 23 })
//=> Pair(23, {})

get(safeNumber)
  .evalWith({ number: '23' })
//=> 0

get()
  .map(safeNumber)
  .evalWith({ number: 23 })
//=> 23

get()
  .map(safeNumber)
  .runWith({ string: '47' })
//=> 0
```

### modify
```haskell
State.modify :: (s -> s) -> State s ()
```

A construction helper that can be used to lift an endo-function that matches
the fixed type of the state portion. The lifted function will receive the state
and then replace it with the result of the function. Great care should be taken
to not use functions that will change the type of the state as it may not be
expected in other stateful computations and can result in hard to track down
bugs.

`modify` will replace any resultant with a `Unit` type which can cause some
grief when `chain`ing together stateful computations. If the resultant `modify`
it is usually best to use `constant` to map in the original resultant when
using

```js
```

### put
```haskell
State.put :: s -> State s a
```

[ desc ]

```js
```

### of
```haskell
State.of :: a -> State s a
```

[ desc ]

```js
```

### type
```haskell
State.type :: () -> String
```

`type` provides a string representation of the type name for a given type in
`crocks`. While it is used mostly internally for law validation, it can be
useful to the end user for debugging and building out custom types based on the
standard `crocks` types. While type comparisons can easily be done manually by
calling `type` on a given type, using the `isSameType` function hides much of
the boilerplate. `type` is available on both the Constructor and the Instance
for convenience.

```js
```

## Instance Methods

### map
```haskell
State s a ~> (a -> b) -> State s b
```

[ desc ]

```js
```

### ap
```haskell
State s (a -> b) ~> State s a -> State s b
```

[ desc ]

```js
```

### chain
```haskell
State s a ~> (a -> State s b) -> State s b
```

[ desc ]

```js
```

### runWith
```haskell
State s a ~> s -> Pair a s
```

[ desc ]

```js
```

### evalWith
```haskell
State s a ~> s -> a
```

[ desc ]

```js
```

### execWith
```haskell
State s a ~> s -> s
```

[ desc ]

```js
```
