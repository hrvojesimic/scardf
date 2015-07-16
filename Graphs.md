# Graphs #


## `Graph` ##

A general trait is called `Graph`.


## `SetGraph` ##

SetGraphs are created with a Graph factory object, or with a SetGraph constructor.


## `MutableSetGraph` ##

If you need a mutable object, use `MutableSetGraph`. Adding operations mutate the original graph and won't create new graphs.


## `JenaGraph` ##

If you wish to use the popular Jena framework, this wrapper implementation of the `Graph` trait is available in the subpackage `jena`.