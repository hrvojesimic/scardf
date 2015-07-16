Identifier declaration is simple:

```
on( GET /PT.Element/PT.symbol ) {
  Grow single elementDetails
}
```

```
pt:Element rdf:Type rdfs:Class ,
           :uriLabel "element" .
pt:symbol  rdf:Type rdf:Property ,
           rdfs:range xsd:string .
```

URI path `/element/Si` is tested against the pattern in the declaration. Since the first pattern segment is described in the domain graph as an RDF resource of type `rdfs:Class`, it

```
SELECT ?fc
WHERE {
  ?fc rdf:type pt:Element;
      pt:symbol "Si" .
}
```