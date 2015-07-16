> Note: _This page describes the new `org.scardf` API. The old API is described [here](Overview.md)._

## Defining a vocabulary ##

Typically, you define URI references as `val`s in an object extending the `Vocabulary`:
```
object PeopleVocabulary extends Vocabulary( "http://person.eg#" ) {
  val Person = uriref( "Person" )
  val Hobby = uriref( "Hobby" )
  val Swimming = uriref( "Swimming" )
  val Science = uriref( "Science" )
  val likes = prop( "Likes" )
  val isMale = prop( "IsMale" )
  val height = propInt( "Height" )
}
```

Prefix for all URI references is given as a parameter for `Vocabulary`, so:
```
PeopleVocabulary.uriref( "Person" )
```

is the same as:
```
UriRef( "http://person.eg#Person" )
```

`Vocabulary` methods `prop`, `propInt` etc. also construct URI references, but of the subclass `Property`. For more details, see [Converters#Properties](Converters#Properties.md).


## Building a graph ##

An RDF graph is constructed using nested expressions:
```
val john = UriRef( "http://doe.eg#john" )
val g = Graph.build( john -( 
  RDF.Type -> Person,
  isMale -> true,
  name -> Branch( given -> "John", family -> "Doe" ),
  height -> 167, 
  likes -> ObjSet( Swimming, Science )
) )
```

Details are explained on the page about [graph construction](ConstructGraph.md). Also, there is [a more complex example](Example.md).


## Traversing a graph ##

A graph can be traversed with a slash operator forming an XPath-like expressions:
```
john/name/given
```

These yield a bag of RDF node objects. If you expect a single string, you append the `asString` converter object:
```
john/name/given/asString == "John"
```

Converters also have modifiers which yield collections of Scala objects:
```
john/likes/asNode.set == Set( Swimming, Science )
```

For more details, see pages on [Nodebags](Nodebags.md) and [Converters](Converters.md).