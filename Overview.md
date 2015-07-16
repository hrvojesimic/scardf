<blockquote><b>Note</b>: _This page describes the old <code>net.croz.scardf</code> API. Check out <b><a href='ApiOverview.md'>the new version</a></b>.</blockquote>_

Basic use cases of the API are:
  * [Defining RDF vocabularies](Vocabulary.md)
  * [Building RDF graphs](Building.md)
  * [Traversing RDF graphs](Traversal.md)
  * [Querying RDF graphs](Querying.md)

## Defining a [vocabulary](Vocabulary.md) ##

You can define values in an `object` to define your vocabulary and the basic ontology:
```
object PeopleVocabulary extends Vocabulary( "http://person.eg#" ) {
  val Person = pRes( "Person" )
  val Hobby = pRes( "Hobby" )
  val Swimming = pRes( "Swimming" ) a Hobby
  val Science = pRes( "Science" ) a Hobby
  val Likes = pProp( "Likes" ) withRange Hobby
  val IsMale = pProp( "IsMale" ) withRange XSD.boolean
  val Height = pProp( "Height" ) withRange XSD.int
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
implicit val model = new Model withPrefix "http://family.eg#"
val john = Res( "john" ) a Person state(
  Name -> Anon( Given -> "John", Family -> "Doe" ),
  IsMale -> true,
  Height -> 167,
  Likes -> ( Swimming, Science )
)
```

## [Traversing](Traversal.md) a graph ##

A graph can be traversed with a slash operator forming an XPath-like expressions:
```
john/Name/Given
```

These yield a bag of RDF node objects. If you expect a single string, you append the `asString` converter object:
```
john/Name/Given/asString == "John"
```

Converters also have modifiers which yield collections of Scala objects:
```
john/Likes/asRes.set == Set( Swimming, Science )
```

## [Querying](Querying.md) a graph ##

```
val selectHighest = Sparql select 'person where( ('person, Height, 'h) ) orderBy desc( 'h ) limit 1
val results = selectHighest from model
```

The `selectHighest` object forms a Sparql query:
```
SELECT ?person WHERE { ?person :Height ?h . } ORDER BY DESC( ?h ) LIMIT 1
```

Query executed on the example model yields a single result with resource `http://family.eg#john`