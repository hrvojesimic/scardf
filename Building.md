<blockquote><b>Note</b>: _This page describes the old <code>net.croz.scardf</code> API. Check out <b><a href='ApiOverview.md'>the new version</a></b>.</blockquote>_

Model is constructed as a Scala object in Scardf. If you give it a prefix, all resources will prepend this string in their URIs.
```
new Model withPrefix "http://family.eg#"
```

Resources are created from the model, using alternative expressions like these:
```
model.getRes( "john" )
Res( "john" )( model )
```

You can avoid repeating the reference to the model with implicit values:
```
implicit val model = new Model withPrefix "http://family.eg#"
val john = Res( "john" )
```

Adding statements to the model is easy:
```
Height( john ) = 167
```

Another way is calling method `state` on the subject.
Assigned objects may be RDF nodes, but also some Scala objects: Strings, Ints, Booleans...
```
john state Height -> 167
```

You can assign multiple properties in one `state` method, and also multiple values for the same property:
```
john state( IsMale -> true, Likes -> Swimming, Likes -> Science )
```

Assigning multiple values can be shortened using tuples:
```
Likes( john ) = ( Swimming, Science )
```

You can nest other RDF nodes with their own statements, including anonymous (blank) nodes:
```
john state(
  Name -> Anon(
    Given -> "John",
    Family -> "Doe"
  ),
  Spouse -> Res( "jane" ).state( Spouse -> john )
)
```

A more deliberate example of graph building can be found at the [ExampleData](ExampleData.md) page.