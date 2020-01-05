# Table of contents
1. Who needs Types?
2. Types Are About Composability
3. What are Types?
4. Why Do We Need a Mathematical Model?
5. Pure and Dirty Functions
6. Examples of Types



## 2. Types and Functions

### 2.1 Who needs Types?
- Dynamically typed language vs statically checked language
  - dinamically typed language: type mismatches discovered at runtime 
  - statically checked language: type mismatches discovered at compile time
- Type checking provides yet another barrier against nonsensical programs.

### 2.2 Types Are About Composability
- Category theory is about composing arrows, but not any two arrows can be composed
  - The target object of one arrow must be the same as the source source object of the next arrow
- Strong static type checking is that it might eliminate some programs that are semantically correct
- Dealing with types imposes too much burden on the programmer -> `type inference`
- Unit testing can replace strong typing, consider the common refactoring practice in strongly typed languages
  - it's enough to modify the declaration of that function and then fix all the build breaks

### 2.3 What are Types?
- Sets of values
- Sets can be finite and infinite
  - `Bool`: a set of values True and False
  - `String`: a infinite list of `Char`
- Set is a category of Sets
- In Set, objects are sets and morphisms(arrows) are functions
  - functions: map elements of one set to elements of another set.
    - **can map two elements to one but not one elements to two**
  - identity function: maps each element of aset to itself, and so on

> gradually forget all this information and instead express all those notions in purely categorical terms, 
> that is in terms of objects and arrows.


### 2.4 Why Do We Need a Mathematical Model?
- programmers never perform formal proofs of correctness. We always “think” that we write correct programs
- We think that the code we write will perform certain actions that will produce desired results
- denotational semantics: if you want to prove a property of a program, you just prove a mathematical theorem
- it’s possible to perform formal proofs of correctness of software

### 2.5 Pure and Dirty Functions
- A mathmatical function is just a mapping of values to values
- pure functions 
  - guaranteed to produce the same output every time it’s called with the same input
  - it’s easier to give these languages denotational semantics and model them using category theory

### 2.6 Examples of Types
- 
-

### 2.7 Challenges
1. Define a higher-order function (or a function object) memoize in your favorite language. This function takes a pure function f as
an argument and returns a function that behaves almost exactly like f, except that it only calls the original function once for every
argument, stores the result internally, and subsequently returns this stored result every time it’s called with the same argument.
You can tell the memoized function from the original by watching its performance. For instance, try to memoize a function that takes a long time to evaluate. You’ll have to wait for the result
the first time you call it, but on subsequent calls, with the same argument, you should get the result immediately

2. Try to memoize a function from your standard library that you normally use to produce random numbers. Does it work?
3. Most random number generators can be initialized with a seed. Implement a function that takes a seed, calls the random number
generator with that seed, and returns the result. Memoize that function. Does it work?


```scala
object TypesAndFunctions extends App {
  def memoize[A, B](f: A => B): A => B = {
    new mutable.HashMap[A, B]() {
      override def apply(key: A): B = {
        getOrElseUpdate(key, f(key))
      }
    }
  }

  // #2
  assert(memoize(scala.util.Random.nextInt) == memoize(scala.util.Random.nextInt))

  // #3
  def generator(seed: Int): BigInt = {
    scala.util.Random.nextInt(seed)
  }
  assert(memoize(generator)(100) == memoize(generator)(100))
}
```
