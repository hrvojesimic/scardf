## Graph nodes ##

[Graph nodes](GraphNode.md) are objects that pair up URI references or blank nodes with a specific graph.

Take a look at the vocabularies in the [Example](Example.md):
```
import PeopleVoc._
import Doe._
val g = Doe.graph
```

`john` in the context of graph `g` is written like this:
```
g/john
```

Graph nodes also have a **slash operator** that can take URI references as an argument.
You can connect these operations to form a **path expression**, like in XPath:
```
g/john/name/given
```

## Nodebags ##

This actually gives you a **bag of RDF nodes** in a single graph.
This bag may contain any number of nodes, or no nodes at all.
A special expression `g/-` yields a node bag containing all nodes present as subjects in triples of the graph `g`.

To convert a node bag to a string, you use the same slash operator, but now with a **[converter object](Converters.md)**:
```
g/john/name/given/asString == "John"
```

If you expect results of different cardinalities, you can manipulate a slash-operator result as a collection of RDF nodes.
```
( g/anna/spouse ).isEmpty
for ( r <- g/john/likes ) println( r )
```

Unary operator `?` yields `true` if the bag is not empty and contains only boolean values `true`.
```
g/john/isMale? == true
```

If the slash operator returns an empty node list at any point, the whole expression will yield an empty bag
- no exception is thrown:
```
( g/anna/spouse/name/family ).isEmpty
```