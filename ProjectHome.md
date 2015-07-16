Scardf is an API in [Scala](http://www.scala-lang.org/) providing a [DSL](http://en.wikipedia.org/wiki/Domain-specific_language) for writing, traversing and querying [RDF](http://www.w3.org/RDF/) graphs.

Instead of writing something like this using [Jena](http://jena.sourceforge.net/):
```
Model model = ModelFactory.createDefaultModel();
model.createResource( "http://somewhere/JohnSmith" )
  .addProperty( N,
    model.createResource()
      .addProperty( Given, "John" )
      .addProperty( Family, "Smith" ) );
```

with Scardf you can write like this:
```
Graph( UriRef( "http://somewhere/JohnSmith" ) -N-> Branch( Given -> "John", Family -> "Smith" ) )
```

For more details, check out these pages first:
  * [Quick intro](ReplGuide.md)
  * [API overview](ApiOverview.md)


## Project changes ##

Current preferred API is in package **`org.scardf`** and it's available since version 0.3. The latest stable version is **0.5** and it works with Scala 2.8.0 **WARNING**: Documentation on the Wiki may include features from the latest development code in the trunk.

Old API is _only_ a facade to [Jena](http://jena.sourceforge.net/). It is still available in the `net.croz.scardf` package. Jena features are available in the new API too, via the `org.scardf.jena` package.

For details, see [Changelog](Changelog.md).

<wiki:gadget url="http://google-code-feed-gadget.googlecode.com/svn/trunk/gadget.xml" up\_feeds="https://groups.google.com/group/scardf/feed/rss\_v2\_0\_msgs.xml" width="500" height="400" border="0"/>