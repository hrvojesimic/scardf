A predicate tree is a construct used for extracting a subset of a larger RDF graph from a given root node.

Predicate tree consists of URI references, connected together using the tilde operator:
```
val pt1 = name~( given, family )
```

Subgraph is extracted using the `growNew` and giving it a `GraphNode` from the source graph:
```
val newRoot = pt1.growNew( graph/john )
```

The result is a node `:john` in this new graph:
```
:john p:name [ p:given "John"; p:family "Doe" ] .
```

Note: The new graph is independent copy of the source graph, and not just a restricted view.

Multiple branches can be specified with a companion object `PredicateTree`:
```
val pt2 = PredicateTree( name~( given, family ), height )
```


## Optional predicates ##

Some predicates may not be required in the source graph - these are optional predicates in the tree.
They are specified using the `?` suffix operator or the `?~` binary operator:
```
val pt3 = PredicateTree( name~( given, family ), likes?, spouse?~( name~given ) )
```


## SPARQL CONSTRUCT ##

Constructed predicate trees can be transformed into SPARQL's CONSTRUCT statements.
Use the following expression:
```
pt3.buildPatternBlock( john ).construct
```

which returns a string containing the equivalent expression:
```
CONSTRUCT { 
  :john p:name ?v2 . 
  ?v2 p:given ?v3 . 
  ?v2 p:family ?v4 . 
  :john p:spouse ?v5 . 
  ?v5 p:name ?v6 . 
  ?v6 p:given ?v7 . 
  :john p:likes ?v8 .
}
WHERE {
  :john p:name ?v2 .
  ?v2 p:given ?v3 .
  ?v2 p:family ?v4 .
  OPTIONAL {
    :john p:spouse ?v5 .
    ?v5 p:name ?v6 .
    ?v6 p:given ?v7 .
  }
  OPTIONAL {
    :john p:likes ?v8 .
  }
}
```