# Table of contents
1. No Objects
2. Simple Graphs
3. Orders
4. Monoid as Set
5. Monoid as Category


## 1. No Objects
- zero object & zero morphism
- empty set makes sense, then how about an empty category?

## 2. Simple Graphs
- categories by connecting objects with arrows
  - 1. an identity arrow at each node
  - 2. any two composable arrows
  - ** Every time you add a new arrow, you have to consider its composition 
  with any other arrow (except for the identity arrows) and itsetlf
- `free category`
  - ![image](https://user-images.githubusercontent.com/13671946/71914103-39b50a00-31bc-11ea-9cc2-1b0e68e17246.png)
  - composition: ![image](https://user-images.githubusercontent.com/13671946/71914076-2ace5780-31bc-11ea-9748-f24665140117.png)

  - 1. all object for every node in the graph
  - 2. all possible chains of composable graph edges as morphisms

## 3. Orders
- A category is a relation between object: **the relation of being less than or equal**
- `preorder`: a < b and b < c then a < c (transitivity)
- `partial order`: a < b and b < a then a = b (anti-symmetry)
- `total order` or `linear order`: 두 object 간에 한 방향으로 비교가 가능한 경우
  - If a ≤ b and b ≤ a then a = b (antisymmetry)
  - If a ≤ b and b ≤ c then a ≤ c (transitivity)
  - a ≤ b or b ≤ a (totality)

- home-set `C(a, b)`
  - A set of morphisms(arrow) from object a to object b in a category C
  - Every home-set in a `preorder` is empty or singleton
    - home-set C(a,a), morphism from a to a must be a singleton, containing only the identity, in any preorder
  - ** preorder may have cycles but forbidden in a partial order **
  - ** Sorting algorithms, such as quick sort, bubble sort, merge sort, etc can only work correctly on total orders.
  - ** partial orders can be sorted using topological sort **

## Monoid as Set
- Monoid는 놀랍도록 간단하지만 매우 강력한 개념이다. 덧셈, 곱셈 등의 기본적인 수학의 기초이며 프로그래밍 어디에서도 찾을 수 있다. 
- `strings`, `lists`, `foldable data structure`, `futures in concurrent programming`, `events in functional reactive programming` 등등
- 전통적으로 모노이드는 이항연산을 갖춘 set으로 정의되며 결합법칙을 따르고 항등원을 갖는다.
- 결합법칙: (a + b) + c = a + (b + c) 
- 항등원: 0 + a = a, a + 0 = a 
- !교환법칙: a + b = b + a, 덧셈은 교환법칙이 성립하지만, monoid의 조건은 아니다.

## Monoid as Category
- 범주론에서는 set, element가 아닌 objects, morphisms 에 대해 집중한다.
- 모든 자연수 n에 대해 n을 더하는 morphism `adder`, 모든 문자열 s에 대해 서로 연결하는 morphism `concat`은 모두 category 성질을 충족한다.
    
