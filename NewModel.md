## Design goals ##

The model closely matches concepts and terminology of the RDF specification.

It is a standalone model and not a wrapper for an existing Java library.
It can be expanded to use existing libraries through this API.

Basic constructs are all lightweight immutable objects,
with some mutable companion classes for larger constructs such as Graphs.


## Nodes ##

The hierarchy follows the RDF model. Nodes can be either literals or subject nodes.
Literals are either plain (with optional language tag) or typed.
Subject nodes are either blank nodes or URI references.

![http://yuml.me/diagram/scruffy;/class/%5BNode%5D%5E-%5BSubjectNode%5D,%20%5BSubjectNode%5D%5E-%5BUriRef%5D,%20%5BSubjectNode%5D%5E-%5BBlank%5D,%20%5BNode%5D%5E-%5BLiteral%5D,%20%5BLiteral%5D%5E-%5BPlainLiteral%5D,%20%5BLiteral%5D%5E-%5BTypedLiteral%5D,%20%5BPlainLiteral%5D-.-%3E%5BLangTag%5D.png](http://yuml.me/diagram/scruffy;/class/%5BNode%5D%5E-%5BSubjectNode%5D,%20%5BSubjectNode%5D%5E-%5BUriRef%5D,%20%5BSubjectNode%5D%5E-%5BBlank%5D,%20%5BNode%5D%5E-%5BLiteral%5D,%20%5BLiteral%5D%5E-%5BPlainLiteral%5D,%20%5BLiteral%5D%5E-%5BTypedLiteral%5D,%20%5BPlainLiteral%5D-.-%3E%5BLangTag%5D.png)


## Triples and graphs ##

Graph is a set of triples (subject, predicate, object).
Subjects are instances of SubjectNode, predicates are URI references, and objects are Nodes.

![http://yuml.me/diagram/scruffy%3Bdir%3ALR%3B/class/%5BTriple%5D-object%3E%5BNode%5D%2C%20%5BTriple%5D-predicate%3E%5BUriRef%5D%2C%20%5BTriple%5D-subject%3E%5BSubjectNode%5D%2C%20%5BGraph%5D%3C%3Eset-%5BTriple%5D.png](http://yuml.me/diagram/scruffy%3Bdir%3ALR%3B/class/%5BTriple%5D-object%3E%5BNode%5D%2C%20%5BTriple%5D-predicate%3E%5BUriRef%5D%2C%20%5BTriple%5D-subject%3E%5BSubjectNode%5D%2C%20%5BGraph%5D%3C%3Eset-%5BTriple%5D.png)


## Nodes from graph ##

When traversing graphs, it is useful to have a reference to a node in the context of a wider graph,
`GraphNodes` - pairs of a `SubjectNode` and a `Graph`.
Literals are not associated with any particular any graph, since they cannot be a subject of any triple.
`NodeFromGraph` is a trait that encompasses literals and graph nodes.

![http://yuml.me/diagram/scruffy;/class/%5BNodeFromGraph%5D%5E-.-%5BGraphNode%5D,%20%5BNodeFromGraph%5D%5E-.-%5BLiteral%5D,%20%5BGraphNode%5D-graph%3E%5BGraph%5D,%20%5BGraphNode%5D-node%3E%5BSubjectNode%5D.png](http://yuml.me/diagram/scruffy;/class/%5BNodeFromGraph%5D%5E-.-%5BGraphNode%5D,%20%5BNodeFromGraph%5D%5E-.-%5BLiteral%5D,%20%5BGraphNode%5D-graph%3E%5BGraph%5D,%20%5BGraphNode%5D-node%3E%5BSubjectNode%5D.png)


## Node bags ##

[Node bags](Nodebags.md) are unordered collections of nodes **from the same graph**.

![http://yuml.me/diagram/scruffy/class/[NodeBag]-%3E[Graph],%20[NodeBag]-*%3E[Node].png](http://yuml.me/diagram/scruffy/class/[NodeBag]-%3E[Graph],%20[NodeBag]-*%3E[Node].png)