
```
implicit val data = new Model() withPrefix "http://family.eg#"
import PeopleVocabulary._

// some shortcuts for describing gender
val aMale = IsMale -> true
val aFemale = IsMale -> false

val anna = Res( "anna" ) a Person state(
  Name -> Anon( Given -> "Anna" ),
  aFemale, Birthday -> "2004-04-14", Height -> 107,
  Likes -> Swimming
)
val bob = Res( "bob" ) a Person state(
  Name -> Anon( Given -> "Bob" ),
  aMale, Birthday -> "2007-05-18", Height -> 87
)
val john = Res( "john" ) a Person state(
  Name -> Anon( Given -> "John" ),
  aMale, Birthday -> "1977-07-27", Height -> 167,
  Likes -> ( Swimming, Science ),
  Children -> RdfList( anna, bob ), Spouse -> Res( "jane" )
)
val jane = Res( "jane" ) a Person state(
  Name -> Anon( Given -> "Jane" ),
  aFemale, Birthday -> "1976-06-26", Height -> 150,
  Likes -> Swimming,
  Children -> RdfList( anna, bob ), Spouse -> john
)

// set last name "Doe" to each family member
List( anna, bob, jane, john ) foreach { (Name~Family)( _ ) = "Doe" }
```

Equivalent description of this graph in [Turtle](http://www.w3.org/2007/02/turtle/primer/) is:
```
@prefix :        <http://person.eg#> .
@prefix family:  <http://family.eg#> .
@prefix rdf:     <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .

family:anna
      rdf:type  :Person;
      :Birthday "2004-04-14";
      :Height   107;
      :IsMale   false;
      :Likes    :Swimming;
      :Name     [ :Family "Doe"; :Given "Anna" ] .

family:bob
      rdf:type  :Person;
      :Birthday "2007-05-18";
      :Height   87;
      :IsMale   true;
      :Name     [ :Family "Doe"; :Given "Bob" ] .

family:john
      rdf:type  :Person;
      :Birthday "1977-07-27";
      :Children ( family:anna family:bob );
      :Height   167;
      :IsMale   true;
      :Likes    :Swimming, :Science;
      :Name     [ :Family "Doe"; :Given "John" ];
      :Spouse   family:jane .

family:jane
      rdf:type  :Person;
      :Birthday "1976-06-26";
      :Children ( family:anna family:bob );
      :Height   150;
      :IsMale   false;
      :Likes    :Swimming;
      :Name     [ :Family "Doe"; :Given "Jane" ];
      :Spouse   family:john .
```