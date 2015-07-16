<blockquote><b>Note</b>: _This page describes the old <code>net.croz.scardf</code> API. Check out <b><a href='ApiOverview.md'>the new version</a></b>.</blockquote>_

Vocabularies are easily defined as Scala objects of class Vocabulary. You can specify namespace URI as a prefix in the superclass constructor:

```
object PeopleVocabulary extends Vocabulary( "http://person.eg#" )
```

The Vocabulary class provides factory methods for constructing RDF resources. These have shorthand methods used in defining ontologies, like `.a` for `rdf:type` and `.withRange` for `rdf:range`.

This vocabulary is used in code examples throughout the wiki:
```
object PeopleVocabulary extends Vocabulary( "http://person.eg#" ) {
  val Person = pRes( "Person" )
  val Hobby = pRes( "Hobby" )
  val Swimming = pRes( "Swimming" ) a Hobby
  val Science = pRes( "Science" ) a Hobby
  val Name = pProp( "Name" )
  val Given = pProp( "Given" )
  val Family = pProp( "Family" )
  val IsMale = pProp( "IsMale" ) withRange XSD.boolean
  val Height = pProp( "Height" ) withRange XSD.int
  val Weight = pProp( "Weight" ) withRange XSD.int
  val Likes = pProp( "Likes" ) withRange Hobby
  val Spouse = pProp( "Spouse" ) withRange Person
  val Children = pProp( "Children" )
}
```