# Semistructured

## Category

> A category defines objects by their _contexts_ and **not** their _contents_.

When we think about sets, we think about a 'container' of 'elements'.  The generic concept of 'element' is a placeholder, variable, that stands for actual element types, such as numbers, strings, objects, other sets, ...

This allows us to reason more abstractly about set properties and operations without regard to specific element types, such as counting them, unions, intersections, differences, similarity, ordering, ...  (polymorphism)

Elements may be required to meet some preconditions for the operations to be valid (e.g., ordering, equality).  (type constraints)

A category is also a container of polymorphic elements, called 'objects'.  Categories also consist of a set of directed arrows between the objects of the category.  There are also a few laws that categories must obey, such as the existence of an identity arrow for every object.  $$\forall x \in Ob(C), \exists f \in Hom(C)$$ such that $$f = x \to x$$ 

```
$ give me super-powers
```

{% hint style="info" %}
 Super-powers are granted randomly so please submit an issue if you're not happy with yours.
{% endhint %}

Once you're strong enough, save the world:

```
// Ain't no code for that yet, sorry
echo 'You got to trust me on this, I saved the world'
```

Inline math: $$\int_{-\infty}^\infty g(x) dx$$


Block math:

$$
\int_{-\infty}^\infty g(x) dx
$$

Or using the templating syntax:

{% math %}\int_{-\infty}^\infty g(x) dx{% endblock %}
