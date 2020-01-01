## Polymorphism
- poly(many) + morphism(types)
  - subtyping: subtyping a subclass's instance can be passed to a base class
  - generics: by type parameterization, instances of a function or class are created
- Liscov Substitution Principle: _"if S is a subtype of T, then objects of type T may be replaced with objects of type S"_

#### Type Bounds
```scala
def assertAllPos[S >: IntSet](r: S): S = ...
```
- `S <: T` : `S` is a subtype of `T`
- `S >: T` : `T` is s supertype of `T` or `T` is a subtype of `S`
: S can be instantiated only to type that conform to IntSet

#### Lower Bounds
`[S >: NonEmpty]`
- NonEmpty, IntSet, AnyRef, Any

#### Mixed Bounds
`[S >: NonEmpty <: IntSet]`
- Restrict S any type on the interval between `NonEmpty` and `IntSet`

#### Covariance
- `NonEmpty <: IntSet` <=> `List[NonEmpty] <: List[IntSet]` 
