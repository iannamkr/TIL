## 1. Category: The Essence of Composition

### 1.1 Arrow as Functions
- g ∘ f
- g after f
- g (f(a))
```haskell
f :: A → B
g :: B → C
g . f
```
### 1.2 Properties of Composition
#### Composition is associative

- f, g, h
- h ∘ (g ∘ f) = (h ∘ g) ∘ f = h ∘ g ∘ f
```haskell
f :: A → B
g :: B → C
h :: C → D
h . (g . f) == (h . g) . f == h . g . f
```
#### For every object A there is an arrow which is a unit of composition
- <img src="https://render.githubusercontent.com/render/math?math=f\cdot id_{A} = f">
- <img src="https://render.githubusercontent.com/render/math?math=id_{B}\cdot f = f">
```haskell
f . id == f
id . f == f
```
- Why would anyone bother with the `identity function`, a function that does nothing?
- Why do we bother with the number `zero`? `Zero` is a symbol for nothing?
- `zero` or `id` are extremely useful

#### Summerize
- A category consists of objects and arrows (morphisms)
- Arrows can be composed and composition is associative
- Every object has an identity arrow that serves as a unit under composition

### 1.3 Composition is the Essence of Programming 
- Programming is about telling the computer what to do.
- Decomposition and Recomposition
  - Decompose bigger problems into smaller problems.
  - Compose those pieces of code to create solutions to larger problems.
- Human limitations
  - "the number of `chunks` an average human can hold in short-term memory is 7 ± 2.", The Magical Number Seven
- **_Elegant code creates chunks that are just the right size and come in just the rigt number for our mental digestive system to assimilate them._**

### 1.4 Challenges
1. Implement, as best as you can, the identity function in your favorite language
```scala
def identity[T](self: T): T = self

println(List(1, 2, 3, 4, 5) map identity)
println(List(List(1), List(2), List(3), List(4)) flatMap identity)

// List(1, 2, 3, 4, 5)
// List(1, 2, 3, 4)
```

2. Implement the composition function in your favorite language. It takes two functions as arguments and returns a function that is
their composition.

- (scala)
```scala
def compose[T](f: T => T, g: T => T) = {
  x: T => f(g(x))
}
def add(x: Int) = x + 1

println(compose(add, add)(3)) // 5
```
- (python)
```python
compose = lambda f, g: lambda x: f(g(x))
print(compose(lambda x: x + 1, lambda y: y + 1)(3)) // 5
```
