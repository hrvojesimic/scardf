Use scardf features to publish a RESTfull data retrieval API for a RDF datastore. For this, we will use **[Scalatra](http://scalatra.org)**, a tiny [Sinatra](http://www.sinatrarb.com)-like web framework for Scala.

As data we will use a popular example of a periodic table of elements at http://www.daml.org/2003/01/periodictable/PeriodicTable - we have loaded this graph in a SPARQL-enabled graph ([JenaGraph](ScardfOnJena.md)) through the val `dataGraph`.


## Listing all chemical elements ##

Scalatra rules are in the form _method_(_path_). To list all chemical elements in the graph, we need to follow these three steps:
  1. locate all nodes in graph which have RDF type Element
  1. build a graph consisting of these nodes expanded using a predicate tree
  1. augment this graph to include URIs to related Web resources

```
get("/elements") {
  val elements = findAll( fc -( RDF.Type -> rElement ) )
  val content = listContent( elements, elementSummaryPT )
  uriAugmenter augmented content
}
```

### Step 1: Locating the focus nodes in the data graph ###

```
val fc = Blank()

def findAll( branches: Branch* ) =
  TemplateFactory( fc -> QVar() )( Graph( branches: _* ) ).findAllIn( dataGraph )
```

We can use this method to fetch a list of all elements in the data graph:
```
val elements = findAll( fc -( RDF.Type -> rElement ) )
```

### Step 2: Expanding the selection ###

For every element we expand the content by taking triples from the located nodes for predicates: RDF type, symbol, name and atomic number.
```
val elementSummaryPT = PredicateTree( RDF.Type, symbol, name, atomicNumber )
```

Expanding the selection of a single node using a predicate tree is achieved with the `CONSTRUCT` statement:
```
def content( anchor: Node, pt: PredicateTree ) =
  dataGraph.construct( pt buildPatternBlock anchor construct )
```

Since we have a list of RDF nodes, we'll add all these expansions:
```
def listContent( items: List[Node], pt: PredicateTree ) =
  items.map{ content( _, pt ).triples }.foldLeft( new JenaGraph ){ _++_ }
```


### Step 3: Augmenting the content ###

We need to augment the content graph with references to other REST resources. For details of each chemical element you go to /e/_symbol_, e.g. `/e/He`. So we add a `href` property to each RDF node having `RDF.Type Element` by defining this augmenter.
```
val uriAugmenter = 
  Augment add { element =>
    element/RDF.Type.v match {
      case PeriodicTable.rElement => element -href-> ("/e/" + element/symbol.v)
      case _ => Branch( element )
    }
  } forEach { _/-/having( RDF.Type ) } get
```


### Finally: Response format ###

Of course, the resulting graphs should be serialized into an appropriate format, like Turtle. So we override Scalatra's `renderResponseBody` method:
```
override protected def renderResponseBody( actionResult: Any ) =
  super.renderResponseBody( 
    actionResult match {
      case g: Graph => g renderIn Turtle asString
      case other => other
    }
  )
```


## Single element ##


```
get("/e/:symbol") {
  val element = findOne( fc -( RDF.Type -> rElement, symbol -> params("symbol") ) )
  uriAugmenter augmented content( element, elementDetailedPT )
}
```

### Locating a single element in graph ###

This way, unknown symbols will result in a `404 (Not Found)` response.
```
def findOne( branches: Branch* ) =
  findAll( branches: _* ) match {
    case Nil => halt( 404 )
    case List( one ) => one
    case x => halt( 500, "Not unique, resulted in: " + x )
  }
```

### Extended predicate trees ###

Additional predicates are a part of a _detailed_ element predicate trees:
```
val elementDetailedPT = elementSummaryPT ++ 
  PredicateTree( color?, standardState, classification, atomicWeight, period )
```