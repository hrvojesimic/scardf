Identifier declaration is simple:

```
on( GET /PT.Element/PT.atomicNumber ) {
  describeSingle( elementDetails )
}
```

It says something like this:

> On every GET request matching the pattern /element/{_atomic number_}, describe a single domain concept with these properties, using the `elementDetails` predicate tree.

First we'll need a **domain graph** in which we describe some properties of these concepts.
```
pt:Element rdf:Type  rdfs:Class ,
           :uriLabel "element" .

pt:atomicNumber rdf:Type   rdf:Property ,
                rdfs:range xsd:integer .
```

Let's test this pattern with URI path `/element/8`, which should describe oxygen. The two path segments in the URI path are mapped to the corresponding pattern segments. Each pattern segment first checks if the path segment matches; if any of the segments do not match the test fails. If they match, then some statements are added to the **identity graph**.

Since the first pattern segment `pt:Element` is described in the domain graph as an RDF resource of type `rdfs:Class`, it is cast as a "set type" segment. This means that the tested path segment should match the URI label property of the pattern ("element"), which it does. This segments adds the statement `_:fc rdf:type pt:Element` to the identity graph.

Second pattern segment `pt:atomicNumber` is an `rdf:Property`, making it a "property value" segment. These check path segments according to their range. In this case, the range is `xsd:integer`, which allows the string "8". This segments adds `_:fc pt:atomicNumber 8` to the identity graph.

And so we end up with this identity graph, expressing exactly what we expected, chemical element with the atomic number 8:
```
_:fc rdf:type  pt:Element ;
     pt:atomicNumber 8 .
```

This pattern graph is used to construct a SPARQL query:
```
SELECT ?fc
WHERE {
  ?fc rdf:type pt:Element;
      pt:atomicNumber 8 .
}
```

Executing this query on the data graph yields a single result, the URI reference `pt:O`, i.e. oxygen.