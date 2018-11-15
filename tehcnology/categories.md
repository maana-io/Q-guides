# Category Theory

> A category defines objects by their _contexts_ and **not** their _contents_.

We can think about **sets** as abstract containers of **elements**.

The generic concept of element serves as a placeholder, a variable, that stands for actual element types, such as numbers, strings, objects, other sets, ...

This allows us to reason more abstractly about set properties and operations on and between them, without regard to specific element types. For instance, we can count them, perform unions, intersections, and differences, compare them for similarity, ordering, ... \(polymorphism\)

Elements may be required to meet some preconditions for the operations to be valid \(e.g., ordering, equality\). \(type constraints\)

A **category**, $$C$$, is also a container \(technically, a [class](https://en.wikipedia.org/wiki/Class_%28set_theory%29)\) of polymorphic elements, called **objects**, denoted:

$$
Ob(C) : T
$$

In addition, categories have a set of directed **arrows** \(morphisms\) between the objects of the category.

$$
Hom(C) : (T \times T)\ \text{where}\ \forall t \in T, t \in Ob(C)
$$

An arrow maps a **domain** to a **codomain**:

$$
\forall a \in Hom(C), dom(a) \in Ob(C) \land cod(a) \in Ob(C)
$$

There are also a few laws that categories must obey, such as the existence of an **identity** arrow for every object.

$$
id : T \to T \\
id  (x) = x
$$

where

$$
\forall x \in Ob(C),\ \exists id \in Hom(C) : id = x \mapsto x
$$

Arrows must also **compose**. Given two arrows, $$f: a \to b$$ and $$g: b \to c$$, the composite arrow is defined as:

$$
g \circ f: a \to c \\
(g \circ f)(x) = g(f(x))
$$

where

$$
\forall g, f \in Hom(C),\ cod(f) = dom(g) \implies (g \circ f) \in Hom(C)
$$

Composition is **associative**. Given three arrows, $$f : a \to b$$, $$g: b \to c$$, and $$h : c \to d$$, then:

$$
h \circ (g \circ f) = (h \circ g) \circ f
$$

In the **Simply Typed Lambda Calculus**, identity and composition are expressed as:

$$
id := \lambda x.x\\
g \circ f := \lambda x.g (f x)
$$
