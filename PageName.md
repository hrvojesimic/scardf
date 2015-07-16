## Defining a vocabulary ##

You can define values in an `object` to define your vocabulary and the basic ontology:
```
object PeopleVocabulary extends Vocabulary( "http://person.eg#" ) {
  val Person = ÷( "Person" )
  val Hobby = ÷( "Hobby" )
  val Swimming = ÷( "Swimming" )
  val Science = ÷( "Science" )
  val Likes = prop( "Likes" )
  val IsMale = prop( "IsMale" )
  val Height = propInt( "Height" )
}
```

## [Building](Building.md) a graph ##

You can add statements to the model using a Scala assignment, or a method named `state`:
```
Height( john ) = 167
john state( IsMale -> true, Likes -> (Swimming, Science) )
```

An RDF graph is constructed using nested expressions:
```
val john = UriRef( "http://doe.eg#john" )
val g = Graph.build( john -( 
  RDF.Type -> Person,
  isMale -> true,
  name -> Branch( given -> "John", family -> "Doe" ),
  height -> 167, 
  likes -> ObjSet( swimming, science )
) )
```

## [Traversing](Traversal.md) a graph ##

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