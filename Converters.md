Converters are functions that take objects like literal nodes, graph nodes
or [NodeBags](NodeBags.md), and return object of another type.
There are three types of converters in Scardf:
  * **node-to-value converters** take a single `NodeFromGraph` and return some type T
  * **node-to-bag converters** take a single `NodeFromGraph` and return a node bag
  * **bag converters** take a `NodeBag` and return some type T


## Node-to-value converters ##

A `NodeFromGraph` can be converted to some specific Scala type using node-to-value converters.
This example extracts an integer from a typed literal:
```
TypedLiteral( "10", XSD.integer )/asInt == 10
```

Simple values can be extracted from non-typed literals as well,
if the lexical value can be parsed:
```
PlainLiteral( "10" )/asInt == 10
```

There are other converters for other types of literals - you can find them in the `org.scardf.NodeConverter` object.
```
asString   asStringIn    asLexic
asInt      asFloat       asDouble
asBoolean  asLocalDate   asBigDecimal
```

There are also converters to non-literal types, e.g.: `asNode`, `asSubjectNode`, `asGraphNode` etc.
Of course, you can always create custom converters as needed.


## Node-to-bag converters ##

A node-to-bag converter takes a node from graph and returns a bag of nodes from the same graph.

A typical node-to-bag converter is a URI reference (`UriRef`).
When an `UriRef` _P_ it is applied to a graph node _S_(_G_), it constructs
a node bag of all nodes _O_ from the same graph _G_ such that a triple (_S_, _P_, _O_) is in _G_:

```
g/john/height == g.bagOf( 167 )
g/john/likes  == g.bagOf( science, swimming )
g/bob/likes   == g.bagOf()
```


## Single-node converters used on bags ##

**Node-to-value** converters work only on **single-item bags** (those that contain exactly one node)
and only when that node is convertible to its target type. They throw exceptions in any other case.

```
g/john/spouse/height/asInt  == 150
g/john/birthday/asLocalDate == new LocalDate( 1977, 07, 27 )
g/john/birthday/asString    == "1977-07-27"
```

**Node-to-bag** converters behave in a different manner when applied to entire bags of nodes.
The converter is applied to each node in bag, and then all resulting bags are joined together
to form a resulting bag. For example:

```
g/-/likes == g.bagOf( science, swimming, swimming, swimming )
```


## Node bag converters ##

While node-to-value and node-to-bag converters operate on a single node from graph,
bag converters operate on entire bags, returning another object as a result
(which, in turn, may also be a node bag).

For example, multiple nodes from the previous section can be filtered out using the `distinct` bag converter:
```
g/-/likes/distinct == g.bagOf( science, swimming )
```

But, bag converters are usually derived from node converters.


## Derived converters ##

You can construct converters that return options or default values from any node-to-value converter.
These bag converters are derived from the given node-to-value converter by calling modifier methods
such as `option` and `default`:
```
g/anna/spouse/asNode.option == None
g/anna/weight/asInt.default( 100 ) == 100
```

There are also converter modifiers that return collections of specific Scala objects from contained nodes.
A `set` modifier returns a bag converter yielding a set of objects instead of a single object:
```
g/john/likes/asNode.set == Set( swimming, science )
g/-/height/asInt.set must_== Set( 99, 107, 150, 167 )
```


## Properties ##

Property is a subclass of `UriRef` which associates value type and a node-to-value converter of the same type
to the URI reference.

Standard node-to-value converters are implicit vals so usually they are implied in the constructor call:
```
import NodeConverter._
val height = Property[Int]( prefix + "height" )
```

Properties have additional methods that create some useful converters. So instead of using a separate
node-to-value converter to extract a single value from bag:
```
g/john/height/asInt
```

you can use Property method `v` which constructs a node-to-value converter of associated type.
This expression is shorter and it isn't redundant:
```
g/john/height.v
```

Property classes have all methods for deriving converters as node-to-value converters, e.g.:
```
g/-/height.set == Set( 99, 107, 150, 167 )
```


## Filters ##

Special type of node bag converter is a **filter**.

Custom filter functions can be placed in a path expression by using the factory objects `where` and `having`:

```
val familyMembers = g/-/having( RDF.Type -> person )
familyMembers == g.bagOf( john, jane, anna, bob )
familyMembers/where( _/isMale? ) == g.bagOf( john, bob )
familyMembers/where( _/spouse/isMale? )/asNode.set == Set( jane )
familyMembers/where( _/likes contains science ) == g.bagOf( john )
```