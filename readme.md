# Functional Programming Jargon (The F# Version)


Functional programming (FP) provides many advantages, and its popularity has been increasing as a result. However, each programming paradigm comes with its own unique jargon and FP is no exception. By providing a glossary, we hope to make learning FP easier.

Examples in this fork are presented in F#. [Why F#](https://fsharpforfunandprofit.com/why-use-fsharp/)

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
* [F# World](https://github.com/derek-baker/functional-programming-jargon)

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
* [Constant](#Constant)
  * [Constant Function](#constant-function)
  * [Constant Functor](#constant-functor)
  * [Constant Monad](#constant-monad)
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


<!-- /RM -->

## Arity

The number of arguments a function takes. From words like unary, binary, ternary, etc. 

```fs
// The arity of sum is 2 (binary)
let sum a b = a + b 

// The arity of inc is 1 (unary)
let inc a = a + 1

// The arity of zero is 0 (nullary)
let zero () = 0
```

__Further reading__

* [Arity](https://en.wikipedia.org/wiki/Arity) on wikipedia.

## Higher-Order Functions (HOF)

A function which takes a function as an argument and/or returns a function.

```fs
let isEven x = 
    match x with 
    | _ when x % 2 = 0 -> true
    | _  -> false

let evenList = [0; 1; 2;] |> List.filter isEven   
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

In this case the variable `five` is retained in `addToFive`'s closure with the value `5`. `addToFive` can then be called with the `y`
to get back the sum.

__Further reading/Sources__
* [Lambda Vs Closure](http://stackoverflow.com/questions/220658/what-is-the-difference-between-a-closure-and-a-lambda)

## Partial Application

Partially applying a function means creating a new function by pre-filling some of the arguments to the original function.

```fs
// Something to apply
let add3 a b c = a + b + c

// Partially applying `2` and `3` to `add3` gives you a one-argument function
let fivePlus = add3 2 3 // (c) -> 2 + 3 + c

fivePlus(4) // 9
```

Partial application helps create simpler functions from more complex ones by baking in data when you have it. [Curried](#currying) functions are automatically partially applied.


## Currying

The process of converting a function that takes multiple arguments into a function that takes them one at a time.

Each time the function is called, it only accepts one argument and returns a function that takes one argument until all arguments are passed.

```fs
let sum = fun a b c -> a + b + c

let curriedSum = fun a -> 
  fun b -> 
    fun c -> a + b + c

curriedSum 38 2 2 // 42

let add2: int -> int -> int = curriedSum 2 

add2 9 1 // 12
```

## Auto Currying
Transforming a function that takes multiple arguments into one that, if provided with fewer than the correct number of arguments, returns a function that takes the rest. When the function gets the correct number of arguments it is then evaluated.

In the example below, functions in F# are auto-curried.

```fs
let add x y = x + y

let sum1: int = add 1 2 // 3
let add1: int -> int = add 1 // (y) => 1 + y
let sum2 = add1 2 // 3
```

__Further reading__
* [Favoring Curry](http://fr.umio.us/favoring-curry/)


## Function Composition

The act of putting two functions together to form a third function where the output of one function is the input of the other. This is one of the most important ideas of functional programming.

```fs
let compose = fun f g -> fun a -> f a |> g 

let getFloor (value: decimal) = System.Math.Floor value
let asString value = value.ToString()

let getFloorAsString = compose getFloor asString

getFloorAsString 121.21m // '121'
```

## Continuation

At any given point in a program, the part of the code that's yet to be executed is known as a continuation.

```fs
let log value = printfn $"{value}"

let printAsString logFunc num = logFunc $"Given {num}"

let addOneAndContinue num next =  
  let result = num + 1
  next log result

addOneAndContinue 2 printAsString // 'Given 3'
```

For more on continuations in F#, see [F# For Fun and Profit](https://fsharpforfunandprofit.com/posts/computation-expressions-continuations/#continuations)

## Pure Function

A function is pure if the return value is only determined by its input values, and does not produce side effects. The function must always return the same result when given the same input.

```fs
let greet name = $"Hi, {name}"

greet "Brianne" // 'Hi, Brianne'
```

As opposed to each of the following:

```fs
let mutable name = "Brianne"

let greet = fun _ -> $"Hi, {name}"

greet // "Hi, Brianne"
```

The above example's output is based on data stored outside of the function...

```fs
let mutable greeting = "Hey"

let greet name = 
  greeting <- $"Hi, {name}" 
  
greet "Brianne"
printfn $"{greeting}" // "Hi, Brianne"
```

... and this one modifies state outside of the function.

## Side effects

A function or expression is said to have a side effect if, apart from returning a value, it interacts with (reads from or writes to) external mutable state.

```fs
let getTime = fun _ -> System.DateTime.Now
let time1 = getTime ()
let time2 = getTime ()

if time1 <> time2 then 
  printfn $"IO is a side effect!"
```

## Idempotent

A function is idempotent if reapplying it to its result does not produce a different result.

```fs
System.Math.Abs -10 |> System.Math.Abs

let predicate a = a 

[2; 1] |> List.sortBy predicate |> List.sortBy predicate |> List.sortBy predicate
```

## Point-Free Style

Writing functions where the definition does not explicitly identify the arguments used. This style usually requires [currying](#currying) or other [Higher-Order functions](#higher-order-functions-hof). A.K.A Tacit programming.

```fs
// Given
let map = fun (fn) -> fun (list) -> list |> List.map fn
let add = fun (a) -> fun (b) -> a + b

// Then

// Not point-free - `numbers` is an explicit argument
let incrementAll: list<int> -> list<int> = fun numbers -> map(add(1))(numbers)

// Point-free - The list is an implicit argument
let incrementAll2: list<int> -> list<int> = map(add(1))
```

It's worth mentioning that point-free functions are not necessarily better than their counterparts, as they can be more difficult to understand when complex.

## Predicate
A predicate is a function that returns true or false for a given value. A common use of a predicate is as the callback for filtering a list.

```fs
let predicate a = a > 2

[1; 2; 3; 4] |> List.filter predicate
```

## Contracts

A contract specifies the obligations and guarantees of the behavior from a function or expression at runtime. This acts as a set of rules that are expected from the input and output of a function or expression, and errors are generally reported whenever a contract is violated.

```fs
let contract = fun input -> 
  if input > 0.0 then 
    true
  else 
    failwith "Contract violated: expected positive input"

let squareRootOfPositiveNum (a: float) =  
  contract a |> ignore
  sqrt a

squareRootOfPositiveNum 4.0 // 2
squareRootOfPositiveNum -2.0 // Contract violated: expected positive input

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

Since these rules govern composition at very abstract level, category theory can be used to uncover new ways of composing things.

__Related content:__

* [Category Theory for the Working Hacker by Philip Wadler](https://www.youtube.com/watch?v=V10hzjgoklA)

* [Category Theory for Programmers](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)

## Value

Anything that can be assigned to a variable.

Note that ["the null value is not normally used in F# for values or variables."](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/values/null-values)

```fs
let five = 5
let anonymousRecord = {| Name = "John"; Age = 30 |} 
let identityFunc a = a
let digitList = [0..9]
let digitSequence = {0..9}
```
## Constant

A variable that cannot be reassigned once defined.

```fs
let five = 5
let john = {| Name = "John"; Age = 30 |}
```

Constants are [referentially transparent](#referential-transparency). That is, they can be replaced with the values that they represent without affecting the result.

The third line in the example above will always return `true`.

### Constant Function

A [curried](#currying) function that ignores its second argument:

```fs
let constant = fun a -> fun _ -> a
let project: obj -> int = constant 0
[1; 2] |> List.map project
```

### Constant Functor

Object whose `map` doesn't transform the contents. See [Functor](#functor)

```fs
type Constant (value: int) =
  member this.value = value
  member this.map (mapping: Constant -> 'a) =
      this

Constant(1).map(fun o -> o.ToString()) // Constant(1)
```

### Constant Monad

Object whose `chain` doesn't transform the contents. See [Monad](#monad)

```fs
type Constant (value: int) =
  member this.value = value
  member this.chain (value: int -> Constant) =
      this

Constant(1).chain (fun n -> Constant (n + 1)) // => Constant(1)
```

## Functor

An object that implements a `map` function that takes a function which is run on the contents of that object. A functor must adhere to two rules:

__Preserves identity__
```fs
let object: int list = [] 
object |> List.map(fun x -> x) = object // true
```

__Composable__

```fs
let f = fun value -> value + 1
let g = fun value -> value + 2

let object = [1; 2; 3]

let composed = object |> List.map (f >> g) 
let pipelined = object |> List.map g |> List.map f

composed = pipelined // true
```

## Pointed Functor
An object with an `of` function that puts _any_ single value into it.

In F#, an example of a similar construct would be `Seq.ofList`.
```fs
Seq.ofList [1; 2; 3] // {1, 2, 3}
```

## Lift

Lifting is when you take a value and put it into an object like a [functor](#pointed-functor). If you lift a function into an [Applicative Functor](#applicative-functor) then you can make it work on values that are also in that functor.

```fs
// Example source: https://stackoverflow.com/questions/53028716/lifting-a-function#answer-53029089

(* 
  "In simple terms, the idea of lifting refers to taking a 
  function f that works with a simple type and creating a 
  new version liftedF that works with a generic type. How do 
  we do this? We take the function f pass it to another 
  function and it returns a "new and improved" version of f. 
*)

// int -> int
let square x = x * x    

// int [] -> int []
let squareArray xA = Array.map square xA  
```

__Further reading__
* [Elevated World](http://fsharpforfunandprofit.com/posts/elevated-world/)


## Referential Transparency

An expression that can be replaced with its value without changing the
behavior of the program is said to be referentially transparent.

Given the function greet:

```fs
let greet = "Hello World!"
```

Any invocation of `greet` can be replaced with `Hello World!` hence greet is
referentially transparent. This would be broken if greet depended on external
state like configuration or a database call. See also [Pure Function](#pure-function) and
[Equational Reasoning](#equational-reasoning).

##  Equational Reasoning

When an application is composed of expressions and devoid of side effects,
truths about the system can be derived from the parts. You can also be confident
about details of your system without having to go through every function.

```fs
let grainToDogs = chickenIntoDogs >> grainIntoChicken
let grainToCats = dogsIntoCats >> grainToDogs
```
In the example above, if you know that `chickenIntoDogs` and `grainIntoChicken`
are pure then you know that the composition is pure. This can be taken further
when more is known about the functions (associative, communtative, idempotent, etc...)

## Lambda

An anonymous function that can be treated like a value.

```fs
fun a -> a + 1
```
Lambdas are often passed as arguments to Higher-Order functions.

```fs
[1; 2] |> List.map (fun a -> a + 1) // [2, 3]
```

You can assign a lambda to a variable.

```fs
let add1 = fun a -> a + 1
```

## Lambda Calculus
A branch of mathematics that uses functions to create a [universal model of computation](https://en.wikipedia.org/wiki/Lambda_calculus).

## Lazy evaluation

Lazy evaluation is a call-by-need evaluation mechanism that delays the evaluation of an expression until its value is needed. 

```fs
let result: Lazy<int> = lazy (5 + 10)
result.Force() // 15
```

In functional languages, lazy evaluation allows for structures like infinite lists, which would not normally be available in an imperative language where the sequencing of commands is significant.

```fs
// Example source: https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/sequences#code-try-15
let seqInfinite =
    Seq.initInfinite (fun index ->
        let n = float (index + 1)
        1.0 / (n * n * (if ((index + 1) % 2 = 0) then 1.0 else -1.0)))

seqInfinite |> Seq.iter (fun el -> printfn $"{el}") // Prints elements from infinite sequence
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
1 + 0 = 0 + 1 // true
```

It's also required that the grouping of operations will not affect the result (associativity):

```fs
1 + (2 + 3) = (1 + 2) + 3 // true
```

Array concatenation also forms a monoid:

```fs
[1; 2] @ [3; 4] // [1, 2, 3, 4]
```

The identity value is empty array `[]`

```fs
[1; 2] @ [] // [1, 2]
```

As a counterexample, subtraction does not form a monoid because there is no commutative identity value:

```fs
0 - 4 = 4 - 0 // false
```

## Monad

A monad is an object with [`of`](#pointed-functor) and `chain` functions. `chain` is like [`map`](#functor) except it un-nests the resulting nested object.

```fs
// Implementation
type ListMonad(list: string list) =  
    member this.list = list
    static member Of (list) = new ListMonad(list)
    member this.Chain fn = ([], this.list) ||> List.fold (fun acc el -> acc @ (fn el))  

// Usage
ListMonad.Of(["cat,dog"; "fish,bird"]).Chain(fun el -> el.Split(",") |> Array.toList) // ['cat', 'dog', 'fish', 'bird']
```

The function `of` is is sometimes replaced with `return` in other functional languages. The function `chain` is also known as `flatmap` and `bind` in other languages.

## Comonad

An object that has `extract` and `extend` functions.

```fs
type Comonad<'a> =
    abstract value: 'a
    abstract extract: unit -> 'a
    abstract extend : (Comonad<'a> -> 'a) -> Comonad<'a>

let rec CoIdentity = fun v -> {
    new Comonad<'a> with
        member this.value = v
        member this.extract () = this.value 
        member this.extend fn = CoIdentity (fn this) }

let extend fn x = CoIdentity (x |> fn)

// Extract takes a value out of a functor.
CoIdentity(1).extract()

// Extend runs a function on the comonad. The function should return the same type as the comonad.
CoIdentity(1).extend (fun (co: Comonad<'a>) -> co.extract () + 1) // CoIdentity(2)
```

## Applicative Functor

An applicative functor is an object with an `ap` function. `ap` applies a function in the object to a value in another object of the same type.

```fs
// Implementation
type ApplicativeFunctor<'a, 'b>(fnList: list<'a -> 'b>) =
    member private this.fnList = fnList
    member this.ap apTarget = 
        ([], this.fnList) ||> List.fold (fun acc fn -> acc @ (apTarget |> List.map fn))

// Usage
let add1 a = a + 1
ApplicativeFunctor([add1]).ap([1]) // [2]

// Lists that you want to combine
let arg1 = [1; 3]

// Combining function - must be curried for this to work
let add x = fun y -> x + y

let partiallyAppliedAdds = ApplicativeFunctor[add].ap(arg1) // [(y) => 1 + y, (y) => 3 + y]
```

<!-- This is useful if you have two objects and you want to apply a binary function to their contents.

```fs
let arg1 = [1, 3]
let arg2 = [4, 5]

// combining function - must be curried for this to work
let add = (x) => (y) => x + y

let partiallyAppliedAdds = [add].ap(arg1) // [(y) => 1 + y, (y) => 3 + y]
```

This gives you an array of functions that you can call `ap` on to get the result:

```fs
partiallyAppliedAdds.ap(arg2) // [5, 6, 7, 8]
``` -->

## Morphism

A transformation function.

### Endomorphism

A function where the input type is the same as the output.

```fs
let uppercase (str: string): string = str.ToUpper()

let decrement (x: int): int = x - 1
```

### Isomorphism

A pair of transformations between 2 types of objects that is structural in nature and no data is lost.

For example, 2D coordinates could be stored as a tuple `(2, 3)` or record `{x = 2; y = 3}`.

```fs
type Point = { X: float; Y: float; }
type Pair = float * float

// Providing functions to convert in both directions...
let pairToCoords (pair: Pair): Point = 
    let (x, y) = pair
    { X = x; Y = y }

// ... makes them isomorphic
let coordsToPair (coords: Point): Pair = 
    (coords.X, coords.Y)

pairToCoords (1, 2) |> coordsToPair  // (1, 2)

coordsToPair {X = 1; Y = 2} |> pairToCoords // {X = 1; Y = 2}
```

### Homomorphism

A homomorphism is just a structure preserving map. In fact, a functor is just a homomorphism between categories as it preserves the original category's structure under the mapping.

<!-- ```fs
A.of(f).ap(A.of(x)) == A.of(f(x))

Either.of(_.toUpper).ap(Either.of('oreos')) == Either.of(_.toUpper('oreos'))
``` -->

### Catamorphism

A `reduceRight`-like function that applies a function against an accumulator and each value of the array (from right-to-left) to reduce it to a single value.

<!-- ```fs
let sum = fun xs -> xs |> Seq.reduce (fun acc x -> acc + x) 

sum([1; 2; 3; 4; 5]) // 15
``` -->

### Anamorphism

An `unfold` function. An `unfold` is the opposite of `fold` (`reduce`). It generates a list from a single value.

<!-- ```fs
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
``` -->

### Hylomorphism

The combination of anamorphism and catamorphism.

### Paramorphism

A function just like `reduceRight`. However, there's a difference:

In paramorphism, your reducer's arguments are the current value, the reduction of all previous values, and the list of values that formed that reduction.

<!-- ```fs
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
``` -->

<!-- The third parameter in the reducer (in the above example, `[x, ... xs]`) is kind of like having a history of what got you to your current acc value. -->

### Apomorphism

it's the opposite of paramorphism, just as anamorphism is the opposite of catamorphism. Whereas with paramorphism, you combine with access to the accumulator and what has been accumulated, apomorphism lets you `unfold` with the potential to return early.

## Setoid

An object that has an `equals` function which can be used to compare other objects of the same type.

Make array a setoid:

```fs
"Foo".Equals("Foo") // true
[1; 2] = [1; 2; 3] // false
```

## Semigroup

An object that has a `concat` function that combines it with another object of the same type.

```fs
[1] @ [2] // [1, 2]
```

## Foldable

An object that has a `reduce` function that applies a function against an accumulator and each element in the array (from left to right) to reduce it to a single value.

```fs
let accumulate total currentElement = total + currentElement
[1; 2; 3] |> List.fold accumulate 0 // 6
```

## Lens ##
A lens is a structure (often an object or function) that pairs a getter and a non-mutating setter for some other data
structure.


The example below was sourced from: https://fsprojects.github.io/FSharpPlus/lens.html
```fs
open System
open FSharpPlus
open FSharpPlus.Lens

// From Mauricio Scheffer: https://gist.github.com/mausch/4260932
type Person = 
    { Name: string
      DateOfBirth: DateTime }

module Person =
    let inline _name f p =
        f p.Name <&> fun x -> { p with Name = x }

type Page =
    { Contents: string }

module Page =
    let inline _contents f p =
        f p.Contents <&> fun x -> {p with Contents = x}

type Book = 
    { Title: string
      Author: Person 
      Pages: Page list }

module Book =
    let inline _author f b =
        f b.Author <&> fun a -> { b with Author = a }

    let inline _authorName b = _author << Person._name <| b

    let inline _pages f b =
        f b.Pages <&> fun p -> { b with Pages = p }

    let inline _pageNumber i b =
        _pages << List._item i << _Some <| b

let rayuela =
    { Book.Title = "Rayuela"
      Author = { Person.Name = "Julio Cortázar"
                 DateOfBirth = DateTime(1914, 8, 26) } 
      Pages = [
        { Contents = "Once upon a time" }
        { Contents = "The End"} ] }
    
// read book author name:
let authorName1 = view Book._authorName rayuela
//  you can also write the read operation as:
let authorName2 = rayuela ^. Book._authorName

// write value through a lens
let book1 = setl Book._authorName "William Shakespear" rayuela
// update value
let book2 = over Book._authorName String.toUpper rayuela
```


## Type Signatures

Oftentimes, manually adding type signatures in F# is unnecessary, as [the F# compiler uses type inference](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/type-inference#type-inference-in-general).

However, we can explicitly specify type signatures like so:

```fs
let add: int -> int -> int = fun x -> fun y -> x + y

let increment: int -> int = fun x -> x + 1
```

If a function accepts another function as an argument, it is wrapped in parentheses.

```fs
let call: ('a -> 'b) -> 'a -> 'b = fun f -> 
  fun x -> 
    f x
```

In the example above, the annotations `'a`, and `'b` are used to signify that the argument can be of any type. 

In the example below, the following version of `map` takes a function that transforms a value of some type `a` into another type `b`, an list of values of type `a`, and returns an list of values of type `b`.

```fs
let map: ('a -> 'b) -> list<'a> -> list<'b> = fun f -> 
  fun list -> 
    list |> List.map f
```

__Further reading__
* [Function signatures in F#](https://fsharpforfunandprofit.com/posts/function-signatures/)

* [What is Hindley-Milner?](http://stackoverflow.com/a/399392/22425) on Stack Overflow

## Algebraic data type
A composite type made from putting other types together. Two common classes of algebraic types are [sum](#sum-type) and [product](#product-type).

### Sum type
A Sum type is the combination of two types together into another one. It is called sum because the number of possible values in the result type is the sum of the input types.

```fs
// Define sum type (discriminated union)
type WeakLogicValue =
  | True of bool
  | False of bool
  | HalfTrue of string

// We have to return a single type from a match expression, so we wrap the value in a Choice<'a, 'b>
let getChoice (weakLogicValue: WeakLogicValue): Choice<bool, string> = 
  match weakLogicValue with
  | True t -> Choice1Of2 t
  | False f -> Choice1Of2 f
  | HalfTrue s -> Choice2Of2 s

// Create instances of sum type
let weakLogicTrue: WeakLogicValue = True true
let weakLogicFalse : WeakLogicValue = False false
let weakLogicHalfTrue : WeakLogicValue = HalfTrue "half-true"


let unwrappedTrue = 
  getChoice weakLogicTrue |> fun (Choice1Of2 choice) -> choice // True

let unwrappedHalfTrue = 
  getChoice weakLogicHalfTrue |> fun (Choice2Of2 choice) -> choice // "half-true"
```

Sum types are sometimes called union types, discriminated unions, or tagged unions. [Further reading](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/discriminated-unions)

### Product type

A **product** type combines types together in a way you're probably more familiar with:

```fs
let point (x: 'a) (y: 'a): 'a * 'a = (x, y)
```
It's called a product because the total possible values of the data structure is the product of the different values. F# has a tuple type, which is the simplest formulation of a product type.

See also [Set theory](https://en.wikipedia.org/wiki/Set_theory).

## Option
Option is a [sum type](#sum-type) with two cases often called `Some` and `None`.

Option is useful for composing functions that might not return a value.

```fs
// Normally, we'd use use the Option<'a> that is defined in Microsoft.FSharp.Core.OptionModule
type Option<'a> = 
  | Some of 'a
  | None

let isSomeA result = 
  match result with 
  | Some x -> true
  | None -> false

let result1: Option<int> = Some 5
let result2: Option<int> = None

isSomeA result1 // true
isSomeA result2 // false
```

`Option` is also sometimes known as `Maybe`. `Some` may sometimes be called `Just`. `None` is sometimes called `Nothing`.

## Function
A **function** `f :: A => B` is an expression - often called arrow or lambda expression - with **exactly one (immutable)** parameter of type `A` and **exactly one** return value of type `B`. That value depends entirely on the argument, making functions context-independent, or [referentially transparent](#referential-transparency). What is implied here is that a function must not produce any hidden [side effects](#side-effects) - a function is always [pure](#purity), by definition. These properties make functions pleasant to work with: they are entirely deterministic and therefore predictable. Functions enable working with code as data, abstracting over behaviour:

```fs
let times2 n = n * 2

[1; 2; 3] |> List.map times2 // [2, 4, 6]
```

## Partial function
A partial function is a [function](#function) which is not defined for all arguments - it might return an unexpected result or may never terminate. Partial functions add cognitive overhead, they are harder to reason about and can lead to runtime errors. Some examples:

```fs
let first (arr: int array) = arr[0]
first [|42|] // 42
first [||] // System.IndexOutOfRangeException: Index was outside the bounds of the array.
```

### Dealing with partial functions
Partial functions are dangerous as they need to be treated with great caution. You might get an unexpected (wrong) result or run into runtime errors. Sometimes a partial function might not return at all. Being aware of and treating all these edge cases accordingly can become very tedious.
Fortunately a partial function can be converted to a regular (or total) one. We can provide default values or use guards to deal with inputs for which the (previously) partial function is undefined. Utilizing the [`Option`](#Option) type, we can yield either `Some(value)` or `None` where we would otherwise have behaved unexpectedly:

```fs
// Example 1: Handle the empty list so the function will always return a result
let sum (values: int list) = 
    match values with
    | [] -> 0
    | _ -> values |> List.reduce (fun a  b -> a + b)

sum [1; 2; 3] // 6
sum [] // 0


// Example 2: Get the first item in a list, wrapped in an int option.
let first = fun (a: int list) -> if a.Length > 0 then Some a.Head else None
first [42; 43]  // Some 42
first [] // None
```

By converting our partial functions total function, we can avoid certain types of runtime errors. Always returning a value will also make for code that is both easier to maintain as well as to reason about.

---
