# Maybe

`Maybe a`

--

--

```js
const {
  chain, compose, curry,
  isNumber, map, option,
  prop, safeLift
} = crocks

const add = curry(
  (x, y) => x + y
)

const safeAdd = curry(
  x => safeLift(isNumber, add(x))
)

// `prop` and `safe` return `Maybe`s.
// The following functions will add to to a value on an object
// and then convert the result to a `String`. The computation
// will only be ran when a `Number` exists on the the key 'value'
// for a given `Object`
const fluent =
  obj => prop('value', obj)
    .chain(safeAdd(10))
    .map(JSON.stringify)
    .option('nope')

const pointfree = compose(
  option('nope'),
  map(JSON.stringify),
  chain(safeAdd(10)),
  prop('value')
)

fluent({ value: 23 })       // => '33'
pointfree({ value: 23 })    // => '33'

fluent({ value: '23' })     // => 'nope'
pointfree({ value: '23' })  // => 'nope'

fluent({ notValue: '23' })    // => 'nope'
pointfree({ notValue: '23' }) // => 'nope'
```

`Maybe` exposes the following functions on the constructor and instance:

| Constructor | Instance |
|:---|:---|
| [`Just`](#just), [`Nothing`](#nothing), [`of`](#of), [`type`](#type), [`zero`](#zero) | [`alt`](#alt), [`ap`](#ap), [`chain`](#chain), [`coalesce`](#coalesce), [`concat`](#concat), [`either`](#either), [`equals`](#equals), [`inspect`](#inspect), [`map`](#map), [`maybe`](#maybe), [`of`](#of), [`option`](#option), [`sequence`](#sequence), [`traverse`](#traverse), [`type`](#type) |

## Constructor

### Nothing

`Maybe m => _ -> m a`

### Just

`Maybe m => a -> m a`

### of

`Maybe m => a -> m a`

### type

`() -> String`

### zero

`Maybe m => () -> m a`

```js
const { Maybe, curry, safe, isNumber, reduce } = crocks
const { zero } = Maybe

// isValid : a -> Maybe Number
const isValid =
  safe(isNumber)

// altMaybe : (a -> Boolean) -> [ a ] -> Maybe Number
const altMaybes = curry(
  liftFn => reduce((m, x) => m.alt(liftFn(x)), zero())
)

// firstNum : [ a ] -> Maybe Number
const firstNum =
  altMaybes(isValid)

firstNum([ 12, null, 34 ])      // => 'Just 12'
firstNum([ '12', null, 34 ])    // => 'Just 34'
firstNum([ '12', null, NaN ])   // => 'Nothing'

```

## Instance

### alt

`Maybe m => m a ~> m a -> m a`

### ap

`Maybe m => m (a -> b) ~> m a -> m b`

### chain

`Maybe m => m a ~> (a -> m b) -> m b`

### coalesce

`Maybe m => m a ~> ((() -> b), (a -> b)) -> m b`

### concat

`Maybe m, Semigroup a => m a ~> m a -> m a`

### either

`Maybe m => m a ~> ((() -> b), (a -> b)) -> b`

### equals

`a -> Boolean`

### inspect

`() -> String`

### map

`Maybe m => m a ~> (a -> b) -> m b`

### of

`Maybe m => a -> m a`

### option

`Maybe m => m a ~> a -> a`

### sequence

`Maybe m, Applicative f => m (f a) ~> (b -> f b) -> f (m a)`

### traverse

`Maybe m, Applicative f => m a ~> ((c -> f c), a -> f b) -> f (m b)`

### type

`() -> String`

### zero

`Maybe m => () -> m a`
