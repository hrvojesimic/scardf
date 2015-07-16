# Filtering triples #

A simple way of extracting information from a graph is by filtering a set of triples to see which, if any, satisfy a given criteria. You can use three techniques which for a given graph `g` return an `Iterable[Triple]`.


### Scala pattern matching ###

Scala's built-in pattern matching is a big help in filtering triples from a graph. In this example we filter out all triples in graph `g` in the form _:anna :height ?x_

```
g.triples filter {
  case Triple( `anna`, `height`, _ ) => true
  case _ => false
}
```

Back ticks arround `anna` and `height` must be used that the compiler would not interpret them as pattern variables.


### `triplesMatching` ###

You can use Scala pattern matching in a more convenient form, like this:
```
g.triplesMatching{
  case Triple( `anna`, `height`, _ ) => true
}
```

This example using `triplesMatching` method is equivalent to the example above.

For another example, this expression filters all triples stating that someone's height is under 100 cm:

```
g.triplesMatching{
  case Triple( _, `height`, h: Literal ) => asInt(h) < 100
}
```


### `triplesLike` ###

Even more concise is the `triplesLike` method. Its parameters match the subject, predicate and object part of the triple, in that order. If you put in a specific node, it will allow only triples with that node in specified place. You can also use a companion object `Node` to match any node in that place. So, the first example would go like this:

```
g.triplesLike( anna, height, Node )
```

Companion objects `SubjectNode`, `UriRef`, `Literal` and `Blank` are used for matching subclasses of Node. Scala value object may also be used, if they can be successfully converted to a literal. Every argument can also be a function of type `Node => Boolean`, as seen in the second example:

```
g.triplesLike( Node, height, { h: Literal => asInt(h) < 100 } )
```

The `triplesLike` method is the **preferred way** of filtering triples in graph because they are declarative. Implementations can use this information to optimize the filtering process, e.g. by using indices or modifying query strings.