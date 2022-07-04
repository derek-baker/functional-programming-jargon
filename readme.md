# Functional Programming Jargon

Functional programming (FP) provides many advantages, and its popularity has been increasing as a result. However, each programming paradigm comes with its own unique jargon and FP is no exception. By providing a glossary, we hope to make learning FP easier.

Examples are presented in F#. [Why F#](https://fsharpforfunandprofit.com/why-use-fsharp/)

Where applicable, this document uses terms defined in the [Fantasy Land spec](https://github.com/fantasyland/fantasy-land)

__Translations__
* [Portuguese](https://github.com/alexmoreno/jargoes-programacao-funcional)
* [Spanish](https://github.com/idcmardelplata/functional-programming-jargon/tree/master)
* [Chinese](https://github.com/shfshanyue/fp-jargon-zh)
* [Bahasa Indonesia](https://github.com/wisn/jargon-pemrograman-fungsional)
* [Python World](https://github.com/jmesyou/functional-programming-jargon.py)
* [Scala World](https://github.com/ikhoon/functional-programming-jargon.scala)
* [Rust World](https://github.com/JasonShin/functional-programming-jargon.rs)
* [Korean](https://github.com/sphilee/functional-programming-jargon)
* [Polish](https://github.com/Deloryn/functional-programming-jargon)
* [Haskell Turkish](https://github.com/mrtkp9993/functional-programming-jargon)
* [Haskell Russian](https://github.com/epogrebnyak/functional-programming-jargon)
* [F-#](https://github.com/derek-baker/functional-programming-jargon)

__Table of Contents__
<!-- RM(noparent,notop) -->

* [Arity](#arity)
* [Higher-Order Functions (HOF)](#higher-order-functions-hof)
* [Closure](#closure)
* [Partial Application](#partial-application)
* [Currying](#currying)
* [Auto Currying](#auto-currying)
* [Function Composition](#function-composition)
* [Continuation](#continuation)
* [Pure Function](#pure-function)
* [Side effects](#side-effects)
* [Idempotent](#idempotent)
* [Point-Free Style](#point-free-style)
* [Predicate](#predicate)
* [Contracts](#contracts)
* [Category](#category)
* [Value](#value)
* [letant](#letant)
  * [letant Function](#letant-function)
  * [letant Functor](#letant-functor)
  * [letant Monad](#letant-monad)
* [Functor](#functor)
* [Pointed Functor](#pointed-functor)
* [Lift](#lift)
* [Referential Transparency](#referential-transparency)
* [Equational Reasoning](#equational-reasoning)
* [Lambda](#lambda)
* [Lambda Calculus](#lambda-calculus)
* [Lazy evaluation](#lazy-evaluation)
* [Monoid](#monoid)
* [Monad](#monad)
* [Comonad](#comonad)
* [Applicative Functor](#applicative-functor)
* [Morphism](#morphism)
  * [Endomorphism](#endomorphism)
  * [Isomorphism](#isomorphism)
  * [Homomorphism](#homomorphism)
  * [Catamorphism](#catamorphism)
  * [Anamorphism](#anamorphism)
  * [Hylomorphism](#hylomorphism)
  * [Paramorphism](#paramorphism)
  * [Apomorphism](#apomorphism)
* [Setoid](#setoid)
* [Semigroup](#semigroup)
* [Foldable](#foldable)
* [Lens](#lens)
* [Type Signatures](#type-signatures)
* [Algebraic data type](#algebraic-data-type)
  * [Sum type](#sum-type)
  * [Product type](#product-type)
* [Option](#option)
* [Function](#function)
* [Partial function](#partial-function)
* [Functional Programming Libraries in JavaScript](#functional-programming-libraries-in-javascript)


<!-- /RM -->

## Arity

The number of arguments a function takes. From words like unary, binary, ternary, etc. 

```fs
let sum = fun a b -> a + b 
// The arity of sum is 2 (binary)

let inc = fun a -> a + 1
// The arity of inc is 1 (unary)

let zero = fun _ -> 0
// The arity of zero is 0 (nullary)
```

__Further reading__

* [Arity](https://en.wikipedia.org/wiki/Arity) on wikipedia.

## Higher-Order Functions (HOF)

A function which takes a function as an argument and/or returns a function.

```fs
let filter predicate list: list<int> = 
    list |> List.filter predicate

let isEven = fun x -> 
    match x with 
    | _ when x % 2 = 0 -> true
    | _  -> false

let evenList = filter isEven [0; 1; 2;]   
printfn $"{evenList}" // [0, 2]
```

## Closure

A closure is a scope which captures local variables of a function for access even after the execution has moved out of the block in which it is defined.
This allows the values in the closure to be accessed by returned functions.


```fs
let five = 5
let addToFive x = x + five
addToFive 3 // => 8
```

In this case the `x` is retained in `addToFive`'s closure with the value `5`. `addToFive` can then be called with the `y`
to get back the sum.

__Further reading/Sources__
* [Lambda Vs Closure](http://stackoverflow.com/questions/220658/what-is-the-difference-between-a-closure-and-a-lambda)
* [JavaScript Closures highly voted discussion](http://stackoverflow.com/questions/111102/how-do-javascript-closures-work)

## Partial Application

Partially applying a function means creating a new function by pre-filling some of the arguments to the original function.

```fs
// Helper to create partially applied functions
// Takes a function and some arguments
let partial = (f, ...args) =>
  // returns a function that takes the rest of the arguments
  (...moreArgs) =>
    // and calls the original function with all of them
    f(...args, ...moreArgs)

// Something to apply
let add3 = (a, b, c) => a + b + c

// Partially applying `2` and `3` to `add3` gives you a one-argument function
let fivePlus = partial(add3, 2, 3) // (c) => 2 + 3 + c

fivePlus(4) // 9
```

You can also use `Function.prototype.bind` to partially apply a function in JS:

```fs
let add1More = add3.bind(null, 2, 3) // (c) => 2 + 3 + c
```

Partial application helps create simpler functions from more complex ones by baking in data when you have it. [Curried](#currying) functions are automatically partially applied.


## Currying

The process of converting a function that takes multiple arguments into a function that takes them one at a time.

Each time the function is called it only accepts one argument and returns a function that takes one argument until all arguments are passed.

```fs
let sum = (a, b) => a + b

let curriedSum = (a) => (b) => a + b

curriedSum(40)(2) // 42.

let add2 = curriedSum(2) // (b) => 2 + b

add2(10) // 12

```

## Auto Currying
Transforming a function that takes multiple arguments into one that if given less than its correct number of arguments returns a function that takes the rest. When the function gets the correct number of arguments it is then evaluated.

lodash & Ramda have a `curry` function that works this way.

```fs
let add = (x, y) => x + y

let curriedAdd = _.curry(add)
curriedAdd(1, 2) // 3
curriedAdd(1) // (y) => 1 + y
curriedAdd(1)(2) // 3
```

__Further reading__
* [Favoring Curry](http://fr.umio.us/favoring-curry/)
* [Hey Underscore, You're Doing It Wrong!](https://www.youtube.com/watch?v=m3svKOdZijA)

## Function Composition

The act of putting two functions together to form a third function where the output of one function is the input of the other. This is one of the most important ideas of functional programming.

```fs
let compose = (f, g) => (a) => f(g(a)) // Definition
let floorAndToString = compose((val) => val.toString(), Math.floor) // Usage
floorAndToString(121.212121) // '121'
```

## Continuation

At any given point in a program, the part of the code that's yet to be executed is known as a continuation.

```fs
let printAsString = (num) => console.log(`Given ${num}`)

let addOneAndContinue = (num, cc) => {
  let result = num + 1
  cc(result)
}

addOneAndContinue(2, printAsString) // 'Given 3'
```

Continuations are often seen in asynchronous programming when the program needs to wait to receive data before it can continue. The response is often passed off to the rest of the program, which is the continuation, once it's been received.

```fs
let continueProgramWith = (data) => {
  // Continues program with data
}

readFileAsync('path/to/file', (err, response) => {
  if (err) {
    // handle error
    return
  }
  continueProgramWith(response)
})
```

## Pure Function

A function is pure if the return value is only determined by its input values, and does not produce side effects. The function must always return the same result when given the same input.

```fs
let greet = (name) => `Hi, ${name}`

greet('Brianne') // 'Hi, Brianne'
```

As opposed to each of the following:

```fs
window.name = 'Brianne'

let greet = () => `Hi, ${window.name}`

greet() // "Hi, Brianne"
```

The above example's output is based on data stored outside of the function...

```fs
let greeting

let greet = (name) => {
  greeting = `Hi, ${name}`
}

greet('Brianne')
greeting // "Hi, Brianne"
```

... and this one modifies state outside of the function.

## Side effects

A function or expression is said to have a side effect if apart from returning a value, it interacts with (reads from or writes to) external mutable state.

```fs
let differentEveryTime = new Date()
```

```fs
console.log('IO is a side effect!')
```

## Idempotent

A function is idempotent if reapplying it to its result does not produce a different result.

```fs
Math.abs(Math.abs(10))
```

```fs
sort(sort(sort([2, 1])))
```

## Point-Free Style

Writing functions where the definition does not explicitly identify the arguments used. This style usually requires [currying](#currying) or other [Higher-Order functions](#higher-order-functions-hof). A.K.A Tacit programming.

```fs
// Given
let map = (fn) => (list) => list.map(fn)
let add = (a) => (b) => a + b

// Then

// Not point-free - `numbers` is an explicit argument
let incrementAll = (numbers) => map(add(1))(numbers)

// Point-free - The list is an implicit argument
let incrementAll2 = map(add(1))
```

Point-free function definitions look just like normal assignments without `function` or `=>`. It's worth mentioning that point-free functions are not necessarily better than their counterparts, as they can be more difficult to understand when complex.

## Predicate
A predicate is a function that returns true or false for a given value. A common use of a predicate is as the callback for array filter.

```fs
let predicate = (a) => a > 2

;[1, 2, 3, 4].filter(predicate) // [3, 4]
```

## Contracts

A contract specifies the obligations and guarantees of the behavior from a function or expression at runtime. This acts as a set of rules that are expected from the input and output of a function or expression, and errors are generally reported whenever a contract is violated.

```fs
// Define our contract : int -> boolean
let contract = (input) => {
  if (typeof input === 'number') return true
  throw new Error('Contract violated: expected int -> boolean')
}

let addOne = (num) => contract(num) && num + 1

addOne(2) // 3
addOne('some string') // Contract violated: expected int -> boolean
```

## Category

A category in category theory is a collection of objects and morphisms between them. In programming, typically types
act as the objects and functions as morphisms.

To be a valid category 3 rules must be met:

1. There must be an identity morphism that maps an object to itself.
    Where `a` is an object in some category,
    there must be a function from `a -> a`.
2. Morphisms must compose.
    Where `a`, `b`, and `c` are objects in some category,
    and `f` is a morphism from `a -> b`, and `g` is a morphism from `b -> c`;
    `g(f(x))` must be equivalent to `(g • f)(x)`.
3. Composition must be associative
    `f • (g • h)` is the same as `(f • g) • h`

Since these rules govern composition at very abstract level, category theory is great at uncovering new ways of composing things.

As an example we can define a category Max as a class
```fs

class Max {
  letructor (a) {
    this.a = a
  }
  id () {
    return this
  }
  compose (b) {
    return this.a > b.a ? this : b
  }
  toString () {
    return `Max(${this.a})`
  }
}

new Max(2).compose(new Max(3)).compose(new Max(5)).id().id() // => Max(5)
```

__Further reading__

* [Category Theory for Programmers](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)

## Value

Anything that can be assigned to a variable.

```fs
5
Object.freeze({name: 'John', age: 30}) // The `freeze` function enforces immutability.
;(a) => a
;[1]
undefined
```

## letant

A variable that cannot be reassigned once defined.

```fs
let five = 5
let john = Object.freeze({name: 'John', age: 30})
```

letants are [referentially transparent](#referential-transparency). That is, they can be replaced with the values that they represent without affecting the result.

With the above two letants the following expression will always return `true`.

```fs
john.age + five === ({name: 'John', age: 30}).age + (5)
```

### letant Function

A [curried](#currying) function that ignores its second argument:

```fs
let letant = a => () => a

;[1, 2].map(letant(0)) // => [0, 0]
```

### letant Functor

Object whose `map` doesn't transform the contents. See [Functor](#functor)

```fs
  letant(1).map(n => n + 1) // => letant(1)
```

### letant Monad

Object whose `chain` doesn't transform the contents. See [Monad](#monad)

```fs
  letant(1).chain(n => letant(n + 1)) // => letant(1)
```

## Functor

An object that implements a `map` function that takes a function which is run on the contents of that object. A functor must adhere to two rules:

__Preserves identity__
```
object.map(x => x) ≍ object
```

__Composable__

```
object.map(compose(f, g)) ≍ object.map(g).map(f)
```

(`f`, `g` are arbitrary functions)

A common functor in JavaScript is `Array` since it abides to the two functor rules:

```fs
;[1, 2, 3].map(x => x) // = [1, 2, 3]
```

and

```fs
let f = x => x + 1
let g = x => x * 2

;[1, 2, 3].map(x => f(g(x))) // = [3, 5, 7]
;[1, 2, 3].map(g).map(f) // = [3, 5, 7]
```

## Pointed Functor
An object with an `of` function that puts _any_ single value into it.

ES2015 adds `Array.of` making arrays a pointed functor.

```fs
Array.of(1) // [1]
```

## Lift

Lifting is when you take a value and put it into an object like a [functor](#pointed-functor). If you lift a function into an [Applicative Functor](#applicative-functor) then you can make it work on values that are also in that functor.

Some implementations have a function called `lift`, or `liftA2` to make it easier to run functions on functors.

```fs
let liftA2 = (f) => (a, b) => a.map(f).ap(b) // note it's `ap` and not `map`.

let mult = a => b => a * b

let liftedMult = liftA2(mult) // this function now works on functors like array

liftedMult([1, 2], [3]) // [3, 6]
liftA2(a => b => a + b)([1, 2], [3, 4]) // [4, 5, 5, 6]
```

Lifting a one-argument function and applying it does the same thing as `map`.

```fs
let increment = (x) => x + 1

lift(increment)([2]) // [3]
;[2].map(increment) // [3]
```

Lifting simple values can be simply creating the object.

```fs
Array.of(1) // => [1]
```


## Referential Transparency

An expression that can be replaced with its value without changing the
behavior of the program is said to be referentially transparent.

Given the function greet:

```fs
let greet = () => 'Hello World!'
```

Any invocation of `greet()` can be replaced with `Hello World!` hence greet is
referentially transparent. This would be broken if greet depended on external
state like configuration or a database call. See also [Pure Function](#pure-function) and
[Equational Reasoning](#equational-reasoning).

##  Equational Reasoning

When an application is composed of expressions and devoid of side effects,
truths about the system can be derived from the parts. You can also be confident
about details of your system without having to go through every function.

```fs
let grainToDogs = compose(chickenIntoDogs, grainIntoChicken)
let grainToCats = compose(dogsIntoCats, grainToDogs)
```
In the example above, if you know that `chickenIntoDogs` and `grainIntoChicken`
are pure then you know that the composition is pure. This can be taken further
when more is known about the functions (associative, communtative, idempotent, etc...)

## Lambda

An anonymous function that can be treated like a value.

```fs
;(function (a) {
  return a + 1
})

;(a) => a + 1
```
Lambdas are often passed as arguments to Higher-Order functions.

```fs
;[1, 2].map((a) => a + 1) // [2, 3]
```

You can assign a lambda to a variable.

```fs
let add1 = (a) => a + 1
```

## Lambda Calculus
A branch of mathematics that uses functions to create a [universal model of computation](https://en.wikipedia.org/wiki/Lambda_calculus).

## Lazy evaluation

Lazy evaluation is a call-by-need evaluation mechanism that delays the evaluation of an expression until its value is needed. In functional languages, this allows for structures like infinite lists, which would not normally be available in an imperative language where the sequencing of commands is significant.

```fs
let rand = function*() {
  while (1 < 2) {
    yield Math.random()
  }
}
```

```fs
let randIter = rand()
randIter.next() // Each execution gives a random value, expression is evaluated on need.
```

## Monoid

An object with a function that "combines" that object with another of the same type (semigroup) which has an "identity" value.

One simple monoid is the addition of numbers:

```fs
1 + 1 // 2
```
In this case number is the object and `+` is the function.

When any value is combined with the "identity" value the result must be the original value. The identity must also be commutative.

The identity value for addition is `0`.
```fs
1 + 0 // 1
0 + 1 // 1
1 + 0 === 0 + 1
```

It's also required that the grouping of operations will not affect the result (associativity):

```fs
1 + (2 + 3) === (1 + 2) + 3 // true
```

Array concatenation also forms a monoid:

```fs
;[1, 2].concat([3, 4]) // [1, 2, 3, 4]
```

The identity value is empty array `[]`

```fs
;[1, 2].concat([]) // [1, 2]
```

As a counterexample, subtraction does not form a monoid because there is no commutative identity value:

```fs
0 - 4 === 4 - 0 // false
```

## Monad

A monad is an object with [`of`](#pointed-functor) and `chain` functions. `chain` is like [`map`](#functor) except it un-nests the resulting nested object.

```fs
// Implementation
Array.prototype.chain = function (f) {
  return this.reduce((acc, it) => acc.concat(f(it)), [])
}

// Usage
Array.of('cat,dog', 'fish,bird').chain((a) => a.split(',')) // ['cat', 'dog', 'fish', 'bird']

// Contrast to map
Array.of('cat,dog', 'fish,bird').map((a) => a.split(',')) // [['cat', 'dog'], ['fish', 'bird']]
```

`of` is also known as `return` in other functional languages.
`chain` is also known as `flatmap` and `bind` in other languages.

## Comonad

An object that has `extract` and `extend` functions.

```fs
let CoIdentity = (v) => ({
  val: v,
  extract () {
    return this.val
  },
  extend (f) {
    return CoIdentity(f(this))
  }
})
```

Extract takes a value out of a functor.

```fs
CoIdentity(1).extract() // 1
```

Extend runs a function on the comonad. The function should return the same type as the comonad.

```fs
CoIdentity(1).extend((co) => co.extract() + 1) // CoIdentity(2)
```

## Applicative Functor

An applicative functor is an object with an `ap` function. `ap` applies a function in the object to a value in another object of the same type.

```fs
// Implementation
Array.prototype.ap = function (xs) {
  return this.reduce((acc, f) => acc.concat(xs.map(f)), [])
}

// Example usage
;[(a) => a + 1].ap([1]) // [2]
```

This is useful if you have two objects and you want to apply a binary function to their contents.

```fs
// Arrays that you want to combine
let arg1 = [1, 3]
let arg2 = [4, 5]

// combining function - must be curried for this to work
let add = (x) => (y) => x + y

let partiallyAppliedAdds = [add].ap(arg1) // [(y) => 1 + y, (y) => 3 + y]
```

This gives you an array of functions that you can call `ap` on to get the result:

```fs
partiallyAppliedAdds.ap(arg2) // [5, 6, 7, 8]
```

## Morphism

A transformation function.

### Endomorphism

A function where the input type is the same as the output.

```fs
// uppercase :: String -> String
let uppercase = (str) => str.toUpperCase()

// decrement :: Number -> Number
let decrement = (x) => x - 1
```

### Isomorphism

A pair of transformations between 2 types of objects that is structural in nature and no data is lost.

For example, 2D coordinates could be stored as an array `[2,3]` or object `{x: 2, y: 3}`.

```fs
// Providing functions to convert in both directions makes them isomorphic.
let pairToCoords = (pair) => ({x: pair[0], y: pair[1]})

let coordsToPair = (coords) => [coords.x, coords.y]

coordsToPair(pairToCoords([1, 2])) // [1, 2]

pairToCoords(coordsToPair({x: 1, y: 2})) // {x: 1, y: 2}
```

### Homomorphism

A homomorphism is just a structure preserving map. In fact, a functor is just a homomorphism between categories as it preserves the original category's structure under the mapping.

```fs
A.of(f).ap(A.of(x)) == A.of(f(x))

Either.of(_.toUpper).ap(Either.of('oreos')) == Either.of(_.toUpper('oreos'))
```

### Catamorphism

A `reduceRight` function that applies a function against an accumulator and each value of the array (from right-to-left) to reduce it to a single value.

```fs
let sum = xs => xs.reduceRight((acc, x) => acc + x, 0)

sum([1, 2, 3, 4, 5]) // 15
```

### Anamorphism

An `unfold` function. An `unfold` is the opposite of `fold` (`reduce`). It generates a list from a single value.

```fs
let unfold = (f, seed) => {
  function go (f, seed, acc) {
    let res = f(seed)
    return res ? go(f, res[1], acc.concat([res[0]])) : acc
  }
  return go(f, seed, [])
}
```

```fs
let countDown = n => unfold((n) => {
  return n <= 0 ? undefined : [n, n - 1]
}, n)

countDown(5) // [5, 4, 3, 2, 1]
```

### Hylomorphism

The combination of anamorphism and catamorphism.

### Paramorphism

A function just like `reduceRight`. However, there's a difference:

In paramorphism, your reducer's arguments are the current value, the reduction of all previous values, and the list of values that formed that reduction.

```fs
// Obviously not safe for lists containing `undefined`,
// but good enough to make the point.
let para = (reducer, accumulator, elements) => {
  if (elements.length === 0) { return accumulator }

  let head = elements[0]
  let tail = elements.slice(1)

  return reducer(head, tail, para(reducer, accumulator, tail))
}

let suffixes = list => para(
  (x, xs, suffxs) => [xs, ...suffxs],
  [],
  list
)

suffixes([1, 2, 3, 4, 5]) // [[2, 3, 4, 5], [3, 4, 5], [4, 5], [5], []]
```

The third parameter in the reducer (in the above example, `[x, ... xs]`) is kind of like having a history of what got you to your current acc value.

### Apomorphism

it's the opposite of paramorphism, just as anamorphism is the opposite of catamorphism. Whereas with paramorphism, you combine with access to the accumulator and what has been accumulated, apomorphism lets you `unfold` with the potential to return early.

## Setoid

An object that has an `equals` function which can be used to compare other objects of the same type.

Make array a setoid:

```fs
Array.prototype.equals = function (arr) {
  let len = this.length
  if (len !== arr.length) {
    return false
  }
  for (let i = 0; i < len; i++) {
    if (this[i] !== arr[i]) {
      return false
    }
  }
  return true
}

;[1, 2].equals([1, 2]) // true
;[1, 2].equals([0]) // false
```

## Semigroup

An object that has a `concat` function that combines it with another object of the same type.

```fs
;[1].concat([2]) // [1, 2]
```

## Foldable

An object that has a `reduce` function that applies a function against an accumulator and each element in the array (from left to right) to reduce it to a single value.

```fs
let sum = (list) => list.reduce((acc, val) => acc + val, 0)
sum([1, 2, 3]) // 6
```

## Lens ##
A lens is a structure (often an object or function) that pairs a getter and a non-mutating setter for some other data
structure.

```fs
// Using [Ramda's lens](http://ramdajs.com/docs/#lens)
let nameLens = R.lens(
  // getter for name property on an object
  (obj) => obj.name,
  // setter for name property
  (val, obj) => Object.assign({}, obj, {name: val})
)
```

Having the pair of get and set for a given data structure enables a few key features.

```fs
let person = {name: 'Gertrude Blanch'}

// invoke the getter
R.view(nameLens, person) // 'Gertrude Blanch'

// invoke the setter
R.set(nameLens, 'Shafi Goldwasser', person) // {name: 'Shafi Goldwasser'}

// run a function on the value in the structure
R.over(nameLens, uppercase, person) // {name: 'GERTRUDE BLANCH'}
```

Lenses are also composable. This allows easy immutable updates to deeply nested data.

```fs
// This lens focuses on the first item in a non-empty array
let firstLens = R.lens(
  // get first item in array
  xs => xs[0],
  // non-mutating setter for first item in array
  (val, [__, ...xs]) => [val, ...xs]
)

let people = [{name: 'Gertrude Blanch'}, {name: 'Shafi Goldwasser'}]

// Despite what you may assume, lenses compose left-to-right.
R.over(compose(firstLens, nameLens), uppercase, people) // [{'name': 'GERTRUDE BLANCH'}, {'name': 'Shafi Goldwasser'}]
```

Other implementations:
* [partial.lenses](https://github.com/calmm-js/partial.lenses) - Tasty syntax sugar and a lot of powerful features
* [nanoscope](http://www.kovach.me/nanoscope/) - Fluent-interface

## Type Signatures

Often functions in JavaScript will include comments that indicate the types of their arguments and return values.

There's quite a bit of variance across the community but they often follow the following patterns:

```fs
// functionName :: firstArgType -> secondArgType -> returnType

// add :: Number -> Number -> Number
let add = (x) => (y) => x + y

// increment :: Number -> Number
let increment = (x) => x + 1
```

If a function accepts another function as an argument it is wrapped in parentheses.

```fs
// call :: (a -> b) -> a -> b
let call = (f) => (x) => f(x)
```

The letters `a`, `b`, `c`, `d` are used to signify that the argument can be of any type. The following version of `map` takes a function that transforms a value of some type `a` into another type `b`, an array of values of type `a`, and returns an array of values of type `b`.

```fs
// map :: (a -> b) -> [a] -> [b]
let map = (f) => (list) => list.map(f)
```

__Further reading__
* [Ramda's type signatures](https://github.com/ramda/ramda/wiki/Type-Signatures)
* [Mostly Adequate Guide](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch7.html#whats-your-type)
* [What is Hindley-Milner?](http://stackoverflow.com/a/399392/22425) on Stack Overflow

## Algebraic data type
A composite type made from putting other types together. Two common classes of algebraic types are [sum](#sum-type) and [product](#product-type).

### Sum type
A Sum type is the combination of two types together into another one. It is called sum because the number of possible values in the result type is the sum of the input types.

JavaScript doesn't have types like this but we can use `Set`s to pretend:
```fs
// imagine that rather than sets here we have types that can only have these values
let bools = new Set([true, false])
let halfTrue = new Set(['half-true'])

// The weakLogic type contains the sum of the values from bools and halfTrue
let weakLogicValues = new Set([...bools, ...halfTrue])
```

Sum types are sometimes called union types, discriminated unions, or tagged unions.

There's a [couple](https://github.com/paldepind/union-type) [libraries](https://github.com/puffnfresh/daggy) in JS which help with defining and using union types.

Flow includes [union types](https://flow.org/en/docs/types/unions/) and TypeScript has [Enums](https://www.typescriptlang.org/docs/handbook/enums.html) to serve the same role.

### Product type

A **product** type combines types together in a way you're probably more familiar with:

```fs
// point :: (Number, Number) -> {x: Number, y: Number}
let point = (x, y) => ({ x, y })
```
It's called a product because the total possible values of the data structure is the product of the different values. Many languages have a tuple type which is the simplest formulation of a product type.

See also [Set theory](https://en.wikipedia.org/wiki/Set_theory).

## Option
Option is a [sum type](#sum-type) with two cases often called `Some` and `None`.

Option is useful for composing functions that might not return a value.

```fs
// Naive definition

let Some = (v) => ({
  val: v,
  map (f) {
    return Some(f(this.val))
  },
  chain (f) {
    return f(this.val)
  }
})

let None = () => ({
  map (f) {
    return this
  },
  chain (f) {
    return this
  }
})

// maybeProp :: (String, {a}) -> Option a
let maybeProp = (key, obj) => typeof obj[key] === 'undefined' ? None() : Some(obj[key])
```
Use `chain` to sequence functions that return `Option`s
```fs

// getItem :: Cart -> Option CartItem
let getItem = (cart) => maybeProp('item', cart)

// getPrice :: Item -> Option Number
let getPrice = (item) => maybeProp('price', item)

// getNestedPrice :: cart -> Option a
let getNestedPrice = (cart) => getItem(cart).chain(getPrice)

getNestedPrice({}) // None()
getNestedPrice({item: {foo: 1}}) // None()
getNestedPrice({item: {price: 9.99}}) // Some(9.99)
```

`Option` is also known as `Maybe`. `Some` is sometimes called `Just`. `None` is sometimes called `Nothing`.

## Function
A **function** `f :: A => B` is an expression - often called arrow or lambda expression - with **exactly one (immutable)** parameter of type `A` and **exactly one** return value of type `B`. That value depends entirely on the argument, making functions context-independant, or [referentially transparent](#referential-transparency). What is implied here is that a function must not produce any hidden [side effects](#side-effects) - a function is always [pure](#purity), by definition. These properties make functions pleasant to work with: they are entirely deterministic and therefore predictable. Functions enable working with code as data, abstracting over behaviour:

```fs
// times2 :: Number -> Number
let times2 = n => n * 2

;[1, 2, 3].map(times2) // [2, 4, 6]
```

## Partial function
A partial function is a [function](#function) which is not defined for all arguments - it might return an unexpected result or may never terminate. Partial functions add cognitive overhead, they are harder to reason about and can lead to runtime errors. Some examples:
```fs
// example 1: sum of the list
// sum :: [Number] -> Number
let sum = arr => arr.reduce((a, b) => a + b)
sum([1, 2, 3]) // 6
sum([]) // TypeError: Reduce of empty array with no initial value

// example 2: get the first item in list
// first :: [A] -> A
let first = a => a[0]
first([42]) // 42
first([]) // undefined
// or even worse:
first([[42]])[0] // 42
first([])[0] // Uncaught TypeError: Cannot read property '0' of undefined

// example 3: repeat function N times
// times :: Number -> (Number -> Number) -> Number
let times = n => fn => n && (fn(n), times(n - 1)(fn))
times(3)(console.log)
// 3
// 2
// 1
times(-1)(console.log)
// RangeError: Maximum call stack size exceeded
```

### Dealing with partial functions
Partial functions are dangerous as they need to be treated with great caution. You might get an unexpected (wrong) result or run into runtime errors. Sometimes a partial function might not return at all. Being aware of and treating all these edge cases accordingly can become very tedious.
Fortunately a partial function can be converted to a regular (or total) one. We can provide default values or use guards to deal with inputs for which the (previously) partial function is undefined. Utilizing the [`Option`](#Option) type, we can yield either `Some(value)` or `None` where we would otherwise have behaved unexpectedly:
```fs
// example 1: sum of the list
// we can provide default value so it will always return result
// sum :: [Number] -> Number
let sum = arr => arr.reduce((a, b) => a + b, 0)
sum([1, 2, 3]) // 6
sum([]) // 0

// example 2: get the first item in list
// change result to Option
// first :: [A] -> Option A
let first = a => a.length ? Some(a[0]) : None()
first([42]).map(a => console.log(a)) // 42
first([]).map(a => console.log(a)) // console.log won't execute at all
// our previous worst case
first([[42]]).map(a => console.log(a[0])) // 42
first([]).map(a => console.log(a[0])) // won't execte, so we won't have error here
// more of that, you will know by function return type (Option)
// that you should use `.map` method to access the data and you will never forget
// to check your input because such check become built-in into the function

// example 3: repeat function N times
// we should make function always terminate by changing conditions:
// times :: Number -> (Number -> Number) -> Number
let times = n => fn => n > 0 && (fn(n), times(n - 1)(fn))
times(3)(console.log)
// 3
// 2
// 1
times(-1)(console.log)
// won't execute anything
```
Making your partial functions total ones, these kinds of runtime errors can be prevented. Always returning a value will also make for code that is both easier to maintain as well as to reason about.

## Functional Programming Libraries in JavaScript

* [mori](https://github.com/swannodette/mori)
* [Immutable](https://github.com/facebook/immutable-js/)
* [Immer](https://github.com/mweststrate/immer)
* [Ramda](https://github.com/ramda/ramda)
* [ramda-adjunct](https://github.com/char0n/ramda-adjunct)
* [ramda-extension](https://github.com/tommmyy/ramda-extension)
* [Folktale](http://folktale.origamitower.com/)
* [monet.js](https://cwmyers.github.io/monet.js/)
* [lodash](https://github.com/lodash/lodash)
* [Underscore.js](https://github.com/jashkenas/underscore)
* [Lazy.js](https://github.com/dtao/lazy.js)
* [maryamyriameliamurphies.js](https://github.com/sjsyrek/maryamyriameliamurphies.js)
* [Haskell in ES6](https://github.com/casualjavascript/haskell-in-es6)
* [Sanctuary](https://github.com/sanctuary-js/sanctuary)
* [Crocks](https://github.com/evilsoft/crocks)
* [Fluture](https://github.com/fluture-js/Fluture)
* [fp-ts](https://github.com/gcanti/fp-ts)

---

__P.S:__ This repo is successful due to the wonderful [contributions](https://github.com/hemanth/functional-programming-jargon/graphs/contributors)!
