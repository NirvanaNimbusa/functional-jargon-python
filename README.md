# Functional Programming Jargon

[![Build Status](https://github.com/dry-python/functional-jargon-python/workflows/test/badge.svg?event=push)](https://github.com/dry-python/functional-jargon-python/actions?query=workflow%3Atest)

Functional programming (FP) provides many advantages, and its popularity has been increasing as a result. However, each programming paradigm comes with its own unique jargon and FP is no exception. By providing a glossary, we hope to make learning FP easier.

This is a fork of [Functional Programming Jargon](https://github.com/jmesyou/functional-programming-jargon).

This document is WIP and pull requests are welcome!

__Table of Contents__
<!-- RM(noparent,notop) -->

* [Side effects](#side-effects)
* [Purity](#purity)
* [Idempotent](#idempotent)
* [Arity](#arity)
* [IO](#io)
* [Higher-Order Functions (HOF)](#higher-order-functions-hof)
* [Closure (TODO)](#closure-todo)
* [Partial Application](#partial-application)
* [Currying](#currying)
* [Function Composition](#function-composition)
* [Continuation (TODO)](#continuation-todo)
* [Point-Free Style](#point-free-style)
* [Predicate](#predicate)
* [Contracts (TODO)](#contracts-todo)
* [Category (TODO)](#category-todo)
* [Value (TODO)](#value-todo)
* [Constant (TODO)](#constant-todo)
* [Lift (TODO)](#lift-todo)
* [Referential Transparency (TODO)](#referential-transparency-todo)
* [Equational Reasoning (TODO)](#equational-reasoning-todo)
* [Lambda (TODO)](#lambda-todo)
* [Lambda Calculus (TODO)](#lambda-calculus-todo)
* [Lazy evaluation (TODO)](#lazy-evaluation-todo)
* [Functor](#functor)
* [Applicative Functor](#applicative-functor)
* [Monoid](#monoid)
* [Monad (TODO)](#monad-todo)
* [Comonad (TODO)](#comonad-todo)
* [Morphism (TODO)](#morphism-todo)
  * [Endomorphism (TODO)](#endomorphism-todo)
  * [Isomorphism (TODO)](#isomorphism-todo)
  * [Homomorphism (TODO)](#homomorphism-todo)
  * [Catamorphism (TODO)](#catamorphism-todo)
  * [Anamorphism (TODO)](#anamorphism-todo)
  * [Hylomorphism (TODO)](#hylomorphism-todo)
  * [Paramorphism (TODO)](#paramorphism-todo)
  * [Apomorphism (TODO)](#apomorphism-todo)
* [Setoid (TODO)](#setoid-todo)
* [Semigroup (TODO)](#semigroup-todo)
* [Foldable (TODO)](#foldable-todo)
* [Lens (TODO)](#lens-todo)
* [Type Signatures (TODO)](#type-signatures-todo)
* [Algebraic data type (TODO)](#algebraic-data-type-todo)
  * [Sum type (TODO)](#sum-type-todo)
  * [Product type (TODO)](#product-type-todo)
* [Option (TODO)](#option-todo)
* [Function (TODO)](#function-todo)
* [Partial function (TODO)](#partial-function-todo)


<!-- /RM -->


## Side effects

A function or expression is said to have a side effect if apart from returning a value, 
it interacts with (reads from or writes to) external mutable state:

```python
>>> print('This is a side effect!')
This is a side effect!
>>>
```

Or:

```python
>>> numbers = []
>>> numbers.append(1)  # mutates the `numbers` array
>>>
```


## Purity

A function is pure if the return value is only determined by its
input values, and does not produce any side effects.

This function is pure:

```python
>>> def add(first: int, second: int) -> int:
...    return first + second
>>>
```

As opposed to each of the following:

```python
>>> def add_and_log(first: int, second: int) -> int:
...    print('Sum is:', first + second)  # print is a side effect
...    return first + second
>>>
```


## Idempotent

A function is idempotent if reapplying it to its result does not produce a different result:

```python
>>> assert sorted([2, 1]) == [1, 2]
>>> assert sorted(sorted([2, 1])) == [1, 2]
>>> assert sorted(sorted(sorted([2, 1]))) == [1, 2]
>>>
```

Or:

```python
>>> assert abs(abs(abs(-1))) == abs(-1)
>>>
```


## Arity

The number of arguments a function takes. From words like unary, binary, ternary, etc. This word has the distinction of being composed of two suffixes, "-ary" and "-ity". Addition, for example, takes two arguments, and so it is defined as a binary function or a function with an arity of two. Such a function may sometimes be called "dyadic" by people who prefer Greek roots to Latin. Likewise, a function that takes a variable number of arguments is called "variadic," whereas a binary function must be given two and only two arguments, currying and partial application notwithstanding.

We can use the `inspect` module to know the arity of a function, see the example below:

```python
>>> from inspect import signature

>>> def multiply(number_one: int, number_two: int) -> int:  # arity 2
...     return number_one * number_two

>>> assert len(signature(multiply).parameters) == 2
>>>
```

### Arity Distinctions

#### Minimum Arity and Maximum Arity

The __minimum arity__ is the smallest number of arguments the function expects to work, the __maximum arity__ is the largest number of arguments function can take. Generally, these numbers are different when our function has default parameter values.

```python
>>> from inspect import getfullargspec
>>> from typing import Any

>>> def example(a: Any, b: Any, c: Any = None) -> None:  # mim arity: 2 | max arity: 3
...     pass

>>> example_args_spec = getfullargspec(example)
>>> max_arity = len(example_args_spec.args)
>>> min_arity = max_arity - len(example_args_spec.defaults)

>>> assert max_arity == 3
>>> assert min_arity == 2
>>>
```

#### Fixed Arity and Variable Arity

A function has __fixed arity__ when you have to call it with the same number of arguments as the number of its parameters and a function has __variable arity__ when you can call it with variable number of arguments, like functions with default parameters values.

```python
>>> from typing import Any

>>> def fixed_arity(a: Any, b: Any) -> None:  # we have to call with 2 arguments
...     pass

>>> def variable_arity(a: Any, b: Any = None) -> None:  # we can call with 1 or 2 arguments
...     pass
>>>
```

#### Definitive Arity and Indefinite Arity

When a function can receive a finite number of arguments it has __definitive arity__, otherwise if the function can receive an undefined number of arguments it has __indefinite arity__. We can reproduce the __indefinite arity__ using Python _*args_ and _**kwargs_, see the example below:

```python
>>> from typing import Any

>>> def definitive_arity(a: Any, b: Any = None) -> None: # we can call just with 1 or 2 arguments
...     pass

>>> def indefinite_arity(*args: Any, **kwargs: Any) -> None: # we can call with how many arguments we want
...     pass
>>>
```

### Arguments vs Parameters

There is a little difference between __arguments__ and __parameters__:

* __arguments__: are the values that are passed to a function
* __parameters__: are the variables in the function definition


## Higher-Order Functions (HOF)

A function that takes a function as an argument and/or returns a function, basically we can treat functions as a value.
In Python every function/method is a Higher-Order Function.

The functions like `reduce`, `map` and `filter` are good examples of __HOF__, they receive a function as their first argument.
```python
>>> from functools import reduce

>>> reduce(lambda accumulator, number: accumulator + number, [1, 2, 3])
6
>>>
```

We can create our own __HOF__, see the example below:

```python
>>> from typing import Callable, TypeVar

>>> _ValueType = TypeVar('_ValueType')
>>> _ReturnType = TypeVar('_ReturnType')

>>> def get_transform_function() -> Callable[[str], int]:
...     return int

>>> def transform(
...     transform_function: Callable[[_ValueType], _ReturnType],
...     value_to_transform: _ValueType,
... ) -> _ReturnType:
...     return transform_function(value_to_transform)

>>> transform_function = get_transform_function()
>>> assert transform(transform_function, '42') == 42
>>>
```


## IO

IO basically means Input/Output, but it is widely used to just tell that a function is impure.

We have a special type (``IO``) and a decorator (``@impure``) to do that in Python:

```python
>>> import random
>>> from returns.io import IO, impure

>>> @impure
... def get_random_number() -> int:
...     return random.randint(0, 100)

>>> assert isinstance(get_random_number(), IO)
>>>
```

__Further reading__:
* [`IO` and `@impure` docs](https://returns.readthedocs.io/en/latest/pages/io.html)


## Closure (TODO)

A closure is a way of accessing a variable outside its scope.
Formally, a closure is a technique for implementing lexically scoped named binding. It is a way of storing a function with an environment.

A closure is a scope which captures local variables of a function for access even after the execution has moved out of the block in which it is defined.
ie. they allow referencing a scope after the block in which the variables were declared has finished executing.


```python
# TODO
```

Lexical scoping is the reason why it is able to find the values of x and add - the private variables of the parent which has finished executing. This value is called a Closure.

The stack along with the lexical scope of the function is stored in form of reference to the parent. This prevents the closure and the underlying variables from being garbage collected(since there is at least one live reference to it).

Lambda Vs Closure: A lambda is essentially a function that is defined inline rather than the standard method of declaring functions. Lambdas can frequently be passed around as objects.

A closure is a function that encloses its surrounding state by referencing fields external to its body. The enclosed state remains across invocations of the closure.

__Further reading/Sources__
* [Lambda Vs Closure](http://stackoverflow.com/questions/220658/what-is-the-difference-between-a-closure-and-a-lambda)
* [JavaScript Closures highly voted discussion](http://stackoverflow.com/questions/111102/how-do-javascript-closures-work)


## Partial Application

Partially applying a function means creating a new function by pre-filling some of the arguments to the original function.
You can also use `functools.partial` or `returns.curry.partial` to partially apply a function in Python:

```python
>>> from returns.curry import partial

>>> def takes_three_arguments(arg1: int, arg2: int, arg3: int) -> int:
...     return arg1 + arg2 + arg3

>>> assert partial(takes_three_arguments, 1, 2)(3) == 6
>>> assert partial(takes_three_arguments, 1)(2, 3) == 6
>>> assert partial(takes_three_arguments, 1, 2, 3)() == 6
>>>
```

The difference between `returns.curry.partial` and `functools.partial` 
is in how types are infered:

```python
import functools

reveal_type(functools.partial(takes_three_arguments, 1))
# Revealed type is 'functools.partial[builtins.int*]'

reveal_type(partial(takes_three_arguments, 1))
# Revealed type is 'def (arg2: builtins.int, arg3: builtins.int) -> builtins.int'
```

Partial application helps create simpler functions from more complex ones by baking in data when you have it. [Curried](#currying) functions are automatically partially applied.

__Further reading__
* [`@curry` docs](https://returns.readthedocs.io/en/latest/pages/curry.html#partial)
* [`functools` docs](https://docs.python.org/3/library/functools.html#functools.partial)


## Currying

The process of converting a function that takes multiple arguments into a function that takes them one at a time.

Each time the function is called it only accepts one argument and returns a function that takes one argument until all arguments are passed.

```python
>>> from returns.curry import curry

>>> @curry
... def takes_three_args(a: int, b: int, c: int) -> int:
...     return a + b + c

>>> assert takes_three_args(1)(2)(3) == 6
>>>
```

Some implementations of curried functions 
can also take several of arguments instead of just a single argument:

```python
>>> assert takes_three_args(1, 2)(3) == 6
>>> assert takes_three_args(1)(2, 3) == 6
>>> assert takes_three_args(1, 2, 3) == 6
>>>
```

Let's see what type `takes_three_args` has to get a better understanding of its features:

```python
reveal_type(takes_three_args)

# Revealed type is:
# Overload(
#   def (a: builtins.int) -> Overload(
#     def (b: builtins.int, c: builtins.int) -> builtins.int, 
#     def (b: builtins.int) -> def (c: builtins.int) -> builtins.int
#   ), 
#   def (a: builtins.int, b: builtins.int) -> def (c: builtins.int) -> builtins.int, 
#   def (a: builtins.int, b: builtins.int, c: builtins.int) -> builtins.int
# )'
```

__Further reading__
* [`@curry` docs](https://returns.readthedocs.io/en/latest/pages/curry.html#id3)
* [Favoring Curry](http://fr.umio.us/favoring-curry/)
* [Hey Underscore, You're Doing It Wrong!](https://www.youtube.com/watch?v=m3svKOdZijA)


## Function Composition

For example, you can compose `abs` and `int` functions like so:

```python
>>> assert abs(int('-1')) == 1
>>>
```

You can also create a third function 
that will have an input of the first one and an output of the second one:

```python
>>> from typing import Callable, TypeVar

>>> _FirstType = TypeVar('_FirstType')
>>> _SecondType = TypeVar('_SecondType')
>>> _ThirdType = TypeVar('_ThirdType')

>>> def compose(
...     first: Callable[[_FirstType], _SecondType],
...     second: Callable[[_SecondType], _ThirdType],
... ) -> Callable[[_FirstType], _ThirdType]:
...     return lambda argument: second(first(argument))

>>> assert compose(int, abs)('-1') == 1
>>>
```

We already have this functions defined as `returns.functions.compose`!

```python
>>> from returns.functions import compose
>>> assert compose(bool, str)([]) == 'False'
>>>
```

__Further reading__
* [`compose` docs](https://returns.readthedocs.io/en/latest/pages/functions.html#compose)


## Continuation (TODO)

At any given point in a program, the part of the code that's yet to be executed is known as a continuation.

```python
# TODO
```

Continuations are often seen in asynchronous programming when the program needs to wait to receive data before it can continue. The response is often passed off to the rest of the program, which is the continuation, once it's been received.

```python
# TODO
```


## Point-Free Style

Point-Free is a style of writting code without using any intermediate variables.

Basically, you will end up with long chains of direct function calls.
This style usually requires [currying](#currying) or other [Higher-Order functions](#higher-order-functions-hof). 
This technique is also sometimes called "Tacit programming".

The most common example of Point-Free programming style is Unix with pipes:

```bash
ps aux | grep [k]de | gawk '{ print $2 }'
```

It also works for Python, let's say you have this function composition:

```python
>>> str(bool(abs(-1)))
'True'
>>>
```

It might be problematic method methods on the first sight, because you need an instance to call a method on.
But, you can always use HOF to fix that and compose normally:

```python
>>> from returns.pipeline import flow
>>> from returns.pointfree import map_
>>> from returns.result import Success

>>> assert flow(
...     Success(-2),
...     map_(abs),
...     map_(range),
...     map_(list),
... ) == Success([0, 1])
>>>
```

__Further reading:__
* [Pointfree docs](https://returns.readthedocs.io/en/latest/pages/pointfree.html)


## Predicate

A predicate is a function that returns true or false for a given value.
So, basically a predicate is an alias for `Callable[[_ValueType], bool]`.

It is very useful when working with `if`, `all`, `any`, etc.

```python
>>> def is_long(item: str) -> bool:
...     return len(item) > 3

>>> assert all(is_long(item) for item in ['1234', 'abcd'])
>>>
```

__Futher reading__
* [Predicate logic](https://en.wikipedia.org/wiki/Predicate_functor_logic)
* [`cond` docs](https://returns.readthedocs.io/en/latest/pages/pointfree.html#cond)


## Contracts (TODO)

A contract specifies the obligations and guarantees of the behavior from a function or expression at runtime. This acts as a set of rules that are expected from the input and output of a function or expression, and errors are generally reported whenever a contract is violated.

```python
# TODO
```

## Category (TODO)

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

__Further reading__

* [Category Theory for Programmers](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)

## Value (TODO)

Anything that can be assigned to a variable.

```python
# TODO
```

## Constant (TODO)

A variable that cannot be reassigned once defined.

```python
# TODO
```

Constants are [referentially transparent](#referential-transparency-todo). That is, they can be replaced with the values that they represent without affecting the result.

```python
# TODO
```

## Lift (TODO)

Lifting is when you take a value and put it into an object like a [Functor](#functor). If you lift a function into an [Applicative Functor](#applicative-functor) then you can make it work on values that are also in that functor.

Some implementations have a function called `lift`, or `liftA2` to make it easier to run functions on functors.

```python
# TODO
```

Lifting a one-argument function and applying it does the same thing as `map`.

```python
# TODO
```


## Referential Transparency (TODO)

An expression that can be replaced with its value without changing the
behavior of the program is said to be referentially transparent.

Say we have function greet:

```python
# TODO
```

## Equational Reasoning (TODO)

When an application is composed of expressions and devoid of side effects, truths about the system can be derived from the parts.

## Lambda (TODO)

An anonymous function that can be treated like a value.

```python 
def f(a):
  return a + 1

lambda a: a + 1
```
Lambdas are often passed as arguments to Higher-Order functions.

```python
List([1, 2]).map(lambda x: x + 1) # [2, 3]
```

You can assign a lambda to a variable.

```python
add1 = lambda a: a + 1
```

## Lambda Calculus (TODO)

A branch of mathematics that uses functions to create a [universal model of computation](https://en.wikipedia.org/wiki/Lambda_calculus).

## Lazy evaluation (TODO)

Lazy evaluation is a call-by-need evaluation mechanism that delays the evaluation of an expression until its value is needed. In functional languages, this allows for structures like infinite lists, which would not normally be available in an imperative language where the sequencing of commands is significant.

```python
# TODO
```

## Functor

An object that implements a `map` method which, while running over each value in the object to produce a new object, adheres to two rules:

__Identity law__

```python
functor.map(lambda x: x) == functor
```

__Associative law__

```python
functor.map(compose(f, g)) == functor.map(g).map(f)
```

Sometimes `Functor` can be called `Mappable` to its `.map` method.
You can have a look at the real-life [`Functor` interface](https://github.com/dry-python/returns/blob/master/returns/interfaces/mappable.py):

```python
>>> from typing import Callable, TypeVar
>>> from returns.interfaces.mappable import Mappable1 as Functor
>>> from returns.primitives.hkt import SupportsKind1

>>> _FirstType = TypeVar('_FirstType')
>>> _NewFirstType = TypeVar('_NewFirstType')

>>> class Box(SupportsKind1['Box', _FirstType], Functor[_FirstType]):
...     def __init__(self, inner_value: _FirstType) -> None:
...         self._inner_value = inner_value
...
...     def map(
...         self,
...         function: Callable[[_FirstType], _NewFirstType],
...     ) -> 'Box[_NewFirstType]':
...         return Box(function(self._inner_value))
...
...     def __eq__(self, other) -> bool:
...         return type(other) == type(self) and self._inner_value == other._inner_value

>>> assert Box(-5).map(abs) == Box(5)
>>>
```

__Further reading:__

- [Functor interface docs](https://returns.readthedocs.io/en/latest/pages/interfaces.html#mappable)


## Applicative Functor

An Applicative Functor is an object with `apply` and `.from_value` methods:
- `.apply` applies a function in the object to a value in another object of the same type. Somethimes this method is also called `ap`
- `.from_value` creates a new Applicative Functor from a pure value. Sometimes this method is also called `pure`

All Applicative Functors must also follow [a bunch of laws](https://returns.readthedocs.io/en/latest/pages/interfaces.html#applicative).

__Further reading:__

- [`Applicative Functor` interface docs](https://github.com/dry-python/returns/blob/master/returns/interfaces/applicative.py)


## Monoid

An object with a function that "combines" that object with another of the same type
and an "empty" value, which can be added with no effect.

One simple monoid is the addition of numbers 
(with `__add__` as an addition function and `0` as an empty element):

```python
>>> assert 1 + 1 + 0 == 2
>>>
```

Tuples, lists, and strings are also monoids:

```python
>>> assert (1,) + (2,) + () == (1, 2)
>>> assert [1] + [2] + [] == [1, 2]
>>> assert 'a' + 'b' + '' == 'ab'
>>>
```


## Monad (TODO)

A monad is an [Applicative Functor](#applicative-functor) with `bind` method. 
`bind` is like [`map`](#functor) except it un-nests the resulting nested object.

```python
# TODO
```

`of` is also known as `return` in other functional languages.
`chain` is also known as `flatmap` and `bind` in other languages.

## Comonad (TODO)

An object that has `extract` and `extend` functions.

```python
# TODO
```

## Morphism (TODO)

A transformation function.

### Endomorphism (TODO)

A function where the input type is the same as the output.

```python
# uppercase :: String -> String
uppercase = lambda s: s.upper() 

# decrement :: Number -> Number
decrement = lambda x: x - 1
```

### Isomorphism (TODO)

A pair of transformations between 2 types of objects that is structural in nature and no data is lost.

```python
# TODO
```

### Homomorphism (TODO)

A homomorphism is just a structure preserving map. In fact, a functor is just a homomorphism between categories as it preserves the original category's structure under the mapping.

```python
# TODO
```

### Catamorphism (TODO)

A `reduce_right` function that applies a function against an accumulator and each value of the array (from right-to-left) to reduce it to a single value.

```python
# TODO
```

### Anamorphism (TODO)

An `unfold` function. An `unfold` is the opposite of `fold` (`reduce`). It generates a list from a single value.

```python
# TODO
```

### Hylomorphism (TODO)

The combination of anamorphism and catamorphism.

### Paramorphism (TODO)

A function just like `reduce_right`. However, there's a difference:

In paramorphism, your reducer's arguments are the current value, the reduction of all previous values, and the list of values that formed that reduction.

```python
# TODO
```

### Apomorphism (TODO)

it's the opposite of paramorphism, just as anamorphism is the opposite of catamorphism. Whereas with paramorphism, you combine with access to the accumulator and what has been accumulated, apomorphism lets you `unfold` with the potential to return early.

## Setoid (TODO)

An object that has an `equals` function which can be used to compare other objects of the same type.

Make array a setoid:

```python 
# TODO
```

## Semigroup (TODO)

An object that has a `concat` function that combines it with another object of the same type.

```python
# TODO
```

## Foldable (TODO)

An object that has a `reduce` function that applies a function against an accumulator and each element in the array (from left to right) to reduce it to a single value.

```python
# TODO
```

## Lens (TODO)

A lens is a structure (often an object or function) that pairs a getter and a non-mutating setter for some other data
structure.

```python
# TODO
```

Lenses are also composable. This allows easy immutable updates to deeply nested data.

```python
# TODO
```

## Type Signatures (TODO)

__Further reading__
* [Ramda's type signatures](https://github.com/ramda/ramda/wiki/Type-Signatures)
* [Mostly Adequate Guide](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch7.html#whats-your-type)
* [What is Hindley-Milner?](http://stackoverflow.com/a/399392/22425) on Stack Overflow

## Algebraic data type (TODO)

A composite type made from putting other types together. Two common classes of algebraic types are [sum](#sum-type-todo) and [product](#product-type-todo).

### Sum type (TODO)

A Sum type is the combination of two types together into another one. It is called sum because the number of possible values in the result type is the sum of the input types.

```python
# TODO
```

Sum types are sometimes called union types, discriminated unions, or tagged unions.

The [sumtypes](https://github.com/radix/sumtypes/) library in Python helps with defining and using union types.

### Product type (TODO)

A __product__ type combines types together in a way you're probably more familiar with:

```python
# TODO
```

See also [Set theory](https://en.wikipedia.org/wiki/Set_theory).

## Option (TODO)

Option is a [sum type](#sum-type-todo) with two cases often called `Some` and `None`.

Option is useful for composing functions that might not return a value.

```python
# TODO
```

`Option` is also known as `Maybe`. `Some` is sometimes called `Just`. `None` is sometimes called `Nothing`.

## Function (TODO)

A __function__ `f :: A => B` is an expression - often called arrow or lambda expression - with __exactly one (immutable)__ parameter of type `A` and __exactly one__ return value of type `B`. That value depends entirely on the argument, making functions context-independent, or [referentially transparent](#referential-transparency-todo). What is implied here is that a function must not produce any hidden [side effects](#side-effects) - a function is always [pure](#purity), by definition. These properties make functions pleasant to work with: they are entirely deterministic and therefore predictable. Functions enable working with code as data, abstracting over behaviour:

```python
# TODO
```

## Partial function (TODO)

A partial function is a [function](#function-todo) which is not defined for all arguments - it might return an unexpected result or may never terminate. Partial functions add cognitive overhead, they are harder to reason about and can lead to runtime errors. Some examples:

```python
# TODO
```

### Dealing with partial functions (TODO)

Partial functions are dangerous as they need to be treated with great caution. You might get an unexpected (wrong) result or run into runtime errors. Sometimes a partial function might not return at all. Being aware of and treating all these edge cases accordingly can become very tedious.
Fortunately a partial function can be converted to a regular (or total) one. We can provide default values or use guards to deal with inputs for which the (previously) partial function is undefined. Utilizing the [`Option`](#option-todo) type, we can yield either `Some(value)` or `None` where we would otherwise have behaved unexpectedly:

```python
# TODO
```
