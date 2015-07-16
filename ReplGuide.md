Let's get our toes wet using the Scala interactive interpreter, aka the REPL (Read-Eval-Print Loop). We'll construct this graph (adapted from the RDF Primer), written here in [Turtle](http://www.w3.org/TeamSubmission/turtle):

```
@prefix dc:      <http://purl.org/dc/elements/1.1/#>.

<http://www.example.org/index.html> 
    dc:date "1999-08-16"^^<http://www.w3.org/2001/XMLSchema#date> ;
    dc:language "en" ;
    dc:creator <http://www.example.org/staffid/85740> .
```

Start the interpreter for Scala 2.9.1. You will need to add Scardf 0.6-SNAPSHOT ([download](http://code.google.com/p/scardf/downloads/list)) and [Joda Time](http://joda-time.sourceforge.net) 1.6 ([download](http://sourceforge.net/projects/joda-time/files/joda-time/1.6/joda-time-1.6.zip/download)) JARs to the classpath (use the `:cp` instruction).

Now, let's import the org.scardf package and the `LocalDate` class, then create our first Scardf object:
```
scala> import org.scardf._
import org.scardf._

scala> import org.joda.time.LocalDate
import org.joda.time.LocalDate

scala> val dc = Vocabulary( "http://purl.org/dc/elements/1.1/#" )
dc: org.scardf.Vocabulary = Vocabulary(http://purl.org/dc/elements/1.1/#)
```

The vocabulary will be handy to use as an URI prefix, writing `dc\"date"` to mimic Turtle's `dc:date`. However, URI references can be constructed without them just fine:
```
scala> val homepage = UriRef( "http://www.example.org/index.html" )
homepage: org.scardf.UriRef = <http://www.example.org/index.html>
```

Expression constructing the graph is similar to the Turtle syntax, thanks to Scala DSL capabilities:
```
scala> val g = Graph( homepage -(
     |   dc\"date" -> new LocalDate( 1999, 8, 16 ),
     |   dc\"language" -> "en",
     |   dc\"creator" -> UriRef( "http://example.org/staffid/85740" )
     | ) )
g: org.scardf.SetGraph = SetGraph[ 3 triples ]
```

Let's make sure the graph contains the correct triples. Its `rend` method will render every triple in graph in the N-TRIPLES format:
```
scala> g.rend
res0: String =
<http://www.example.org/index.html> <http://purl.org/dc/elements/1.1/#date> "1999-08-16"^^<http://www.w3.org/2001/XMLSchema#date> .
<http://www.example.org/index.html> <http://purl.org/dc/elements/1.1/#language> "en" .
<http://www.example.org/index.html> <http://purl.org/dc/elements/1.1/#creator> <http://example.org/staffid/85740> .
```

To query the graph, a concept of [graph nodes](Nodebags#Graph_nodes.md) - a URI reference within a graph - pops into use:
```
scala> g/homepage
res1: org.scardf.GraphNode = GraphNode(<http://www.example.org/index.html>,SetGraph[ 3 triples ])
```

From graph nodes you can construct paths by adding predicates, like this:
```
scala> g/homepage/dc\"date"
res2: org.scardf.NodeBag = NodeBag(TypedLiteral("1999-08-16", <http://www.w3.org/2001/XMLSchema#date>))
```

Predicates result in [node bags](Nodebags#Nodebags.md). You can extract single values using [converters](Converters.md):
```
scala> g/homepage/dc\"date"/NodeConverter.asLocalDate
res3: org.joda.time.LocalDate = 1999-08-16
```

_to be continued..._

Next, take a look at [a quick overview](ApiOverview.md) of features. Use links on the sidebar to discover more. For inspiration, you may also want to take a look at the [Example](Example.md) and [AdvancedExamples](AdvancedExamples.md).