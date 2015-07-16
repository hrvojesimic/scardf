<blockquote><b>Note</b>: _This page describes the old <code>net.croz.scardf</code> API. Check out <b><a href='ApiOverview.md'>the new version</a></b>.</blockquote>_

Reading a property from a RDF resource is simple:
```
Given( Name( john ) ) == Lit( "John" )
```

Another way is with a slash operator, like in XPath. You can connect these operations to form a **path expression**:
```
john/Name/Given
```

This actually gives you a bag of RDF node objects, which may be empty, or a single node, or multiple nodes. To convert a node bag to a string, you use the same slash operator, but now with a **converter object**:
```
john/Name/Given/asString == "John"
```

There are other converters for other types of literals:
```
john/IsMale/asBoolean == true
john/Height/asInt == 167
john/Birthday/asLocalDate == new LocalDate( 1977, 7, 27 )
```

Single-node convertors work only on single-item bags, those that contain exactly one node (in this case a literal convertible to string), and throw exceptions in any other case. But you can construct converters that return options or default values from any single-node converter:
```
john/Spouse/asRes.option == None
john/Weight/asInt.default( 100 ) == 100
```

If you expect results of different cardinalities, you can manipulate a slash-operator result as a collection of RDF nodes.
```
( anna/Spouse ).isEmpty
for ( r <- john/Likes ) println( r )
```

There are also converter modifiers that return collections of specific Scala objects from contained nodes. Adding a `.set` modifier returns a set of nodes instead of a single node:
```
john/Likes/asRes.set == Set( Swimming, Science )
```

**Boolean expressions** in Scardf can have in different forms, to appear as natural as possible. Following expressions all return `true`:
```
john/IsMale?
john has Height -> 167
john( Likes -> Science )?
```

If the slash operator returns an empty node list at any point, the whole expression will yield an empty bag - no exception is thrown:
```
( anna/Spouse/Name/Family ).isEmpty
```

Another type you can use with a slash operator is a **language extractor**, useful for multilingual literals:
```
john/Title/Lang.en
```

Special type of node bag converter is a **filter**. Place them in a path expression by using the factory object `where`:
```
val familyMembers = NodeBag( john, jane, anna, bob )
familyMembers/where( _/IsMale? ) == NodeBag( john, bob )
familyMembers/where( _/Spouse/IsMale? ) == NodeBag( jane )
familyMembers/where( _/Likes contains Science ) == NodeBag( john )
```