A graph is a set of triples, which contain a subject node, a predicate (`UriRef`) and an RDF object (`Node`):
```
RdfTriple( john, height, TypedLiteral( "167", XSD.int ) )
```

Assigned objects may be RDF nodes, but also some general Scala objects: Strings, Ints, Booleans...
```
john -height-> 167
```

This expression actually evaluates to a "Branch", not a triple. Apply method of object `Graph` is used to construct graphs from branches.
```
Graph( john -height-> 167 )
```

You can assign multiple properties to a single subject, with multiple values of the same property:
```
john -( isMale -> true, likes -> swimming, likes -> science )
```

Assigning multiple values can be shortened using `ObjSet`:
```
john -likes-> ObjSet( swimming, science )
```

You can nest other RDF nodes with their own statements, including branches to create anonymous (blank) nodes:
```
john -(
  name -> Branch( given -> "John", family -> "Doe" ),
  spouse -> ( jane -spouse-> john )
)
```

A more deliberate example of graph building can be found at the [Example](Example.md) page.