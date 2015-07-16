**Note**: Check out the data for the examples at [ExampleData](ExampleData.md) page.

## Select query ##

SELECT allows ORDER BY, LIMIT and OFFSET modifiers to be added.

```
val selectPersonsByHeight = ( Sparql 
  select( 'person, 'height ) 
    where( ('person, RDF.Type, Person), ('person, Height, 'height) )
    orderBy( asc( 'height ) )
    limit 2
    offset 1
)
```

This will create a SPARQL query like this one:
```
SELECT ?person ?height 
  WHERE { 
    ?person rdf:type :Person .
    ?person :Height ?height . 
  }
  ORDER BY ASC( ?height )
  LIMIT 2
  OFFSET 1
```

Select queries get executed by calling method `.from( Model )`.
You can test the solutions of the query as a list of maps:
```
(selectPersonsByHeight from model).solutions == List(
  Map( person -> anna, height -> Lit(107) ), 
  Map( person -> jane, height -> Lit(150) )
)
```


## Query modifiers ##

Enclose variable list in `distinct` or `reduced` to include these modifiers into the query:

```
val q = Sparql select distinct( 'hobby ) where( ('person, Likes, 'hobby) )
```

Object `q` will construct a SPARQL query equivalent to this one:

```
SELECT DISTINCT ?hobby WHERE { ?person :Likes ?hobby . }
```


## Special select expressions ##

When you are selecting a single variable, you can use a predefined one called `X`.
This query variable is already expected in some special select expressions,
that provide a more direct access to objects in the solution(s).

When you expect at most one solution to the query, use `selectX` method with a node converter.
This will give you a appropriately typed `Option`:
```
Sparql selectX asRes where( (X, Likes, Science) ) from model == Some( john )
Sparql selectX asInt where( (jane, Height, X) ) from model == Some( 150 )
Sparql selectX asInt where( (jane, Weight, X) ) from model == None
```

You can use `selectAllX` for multiple-solutions queries.
The following example yields an iterator of Res objects:
```
Sparql selectAllX asRes where( (X, Likes, Swimming) ) from model
```


## ASK queries ##

These queries return boolean values:
```
Sparql ask( (john, Likes, Science) ) in model == true
Sparql ask( (X, IsMale, false), (X, Likes, Science) ) in model == false
```