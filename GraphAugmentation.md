Augmentation is construction of additional triples based on existing graph.

Example augmentation rule: Concatenate given name with string and then family name,
and add it as a "fullName" property to each "name" node:

Use the `Augment` object to form `add` _( GraphNode => Branch )_ `forEach` _( Graph => NodeBag )_ statements:
```
Augment add {
  n => n -fullName-> ( n/givenName.v + " " + n/familyName.v ) 
} forEach { _/-/name }
```

Another example: For each triple in the graph, if the triple describes height
(given as a number of centimeters),
construct one additional triple describing the height in meters (rounded down).

This example tests each triple in graph, and concatenates constructed triples. Default case implicitly returns `Nil`, so it doesn't augment anything.
```
Augment triples {
  case Triple( s, `height`, TypedLiteral( hInCm, _ ) ) => List( s -metersHigh-> ( hInCm.toInt/100 ) )
}
```

Another: Add "age" property calculated as years since birth for each subject of type "person":

```
Augment add {
  p => p -age-> yearsSince( p/birthday.v ) 
} forEach {
  _/-/having( RDF.Type -> person ) 
}
```