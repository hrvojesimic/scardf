
```
import NodeConverter._

object PeopleVoc extends Vocabulary( "http://person.eg#" ) {
  val person = uriref( "Person" )
  val hobby = uriref( "Hobby" )
  val swimming = uriref( "Swimming" )
  val science = uriref( "Science" )
  val name = prop( "name" )
  val given = propStr( "given" )
  val family = propStr( "family" )
  val isMale = uriref( "isMale" )
  val height = propInt( "height" )
  val weight = propInt( "weight" )
  val likes = prop( "likes" )
  val spouse = prop( "spouse" )
  val children = prop( "children" )
  val birthday = prop( "birthday" )
  val age = propInt( "age" )
}
```

A second vocabulary is about the family Doe. Here we have defined four resources (members of the family), constructed a graph with necessary information, and then [augmented](GraphAugmentation.md) it to create the final graph.

```
import PeopleVoc._

object Doe extends Vocabulary( "http://doe.eg#" ) {
  val List( anna, bob, john, jane ) = 
    List( 'anna, 'bob, 'john, 'jane ) map { this÷_ }

  // shortcut pairs for describing type and gender
  private val aPerson = RDF.Type -> person
  private val aMale = isMale -> true
  private val aFemale = isMale -> false

  private val basegraph = Graph(
    anna -( aPerson, aFemale, 
      name -> Branch( given -> "Anna" ), height -> 107, birthday -> "2004-04-14",
      likes -> swimming
    ),
    bob -( aPerson, aMale, 
      name -> Branch( given -> "Bob" ), height -> 99, birthday -> "2007-05-18"
    ),
    john -( aPerson, aMale, 
      name -> Branch( given -> "John" ), height -> 167, birthday -> "1977-07-27",
      likes -> ObjSet( swimming, science ), 
      children -> List( anna, bob ), spouse -> jane
    ),
    jane -( aPerson, aFemale, 
      name -> Branch( given -> "Jane" ), height -> 150, birthday -> "1976-06-26",
      likes -> swimming, 
      children -> List( anna, bob ), spouse -> john
    )
  )
  
  // adding last name "Doe" to each name in the basegraph yields the complete graph
  val graph = Augment add { _ -family-> "Doe" } forEach { _/-/name } on basegraph
}
```

For more snippets of Scardf in action, see AdvancedExamples.