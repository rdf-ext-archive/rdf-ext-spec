# RDF-Ext

RDF-Ext started as an extension to the [RDF Interfaces](http://www.w3.org/TR/rdf-interfaces/) specification.
This API documentation describes all interfaces, so it's no longer just an extension. 

The RDF-Ext API documentation defines a set of low-level interfaces for working with RDF data in JavaScript.
RDF-Ext defines interface for the RDF Data Model, parsers, serializers and triplestores.


# Interfaces

All asynchronous calls are implemented with a combined API of Callbacks and ES6 Promises.
That means either Callbacks or Promises can be used.


## RDFNode()

RDFNode is the base class of NamedNode, BlankNode, and Literal.

### String interfaceName

Provides access to the string name of the current interface, normally one of "NamedNode", "BlankNode" or "Literal".
This method serves to disambiguate instances of RDFNode which are otherwise identical, such as NamedNode and BlankNode.

### String nominalValue

The nominalValue of an RDFNode is refined by each interface which extends RDFNode.

### boolean .equals(other)

If other is an instance of RDFNode then this method returns true if an only if all attributes on the two interfaces are equivalent.
If other is NOT an instance of RDFNode then the it must be compared against the result of calling toValue on this node.

You cannot simply test two RDF Nodes for equivalence using general language constructs such as ==.

### String .toNT()

Returns the N-Triples representation of the RDFNode.

### String .toString()

The stringification of an RDFNode is refined by each interface which extends RDFNode.

### any .valueOf()

This method provides access to the implementations host environment native value for this RDFNode.


## BlankNode() extends RDFNode

A BlankNode is a reference to an unnamed resource (one for which an IRI is not known), and may be used in a Triple as a unique reference to that unnamed resource.
BlankNodes are stringified by prepending "_:" to a unique value, for instance _:b142 or _:me, this stringified form is referred to as a "blank node identifier".

### String interfaceName

Contains the fixed value "BlankNode".

### String nominalValue

The temporary identifier of the BlankNode, the nominalValue may be of any type, so long as it is unique and can be stringified, for instance a number or a string.
The nominalValue must not be relied upon in any way between two separate processing runs of the same document, or two instances of Graph containing "the same" triples.

Developers and authors must not assume that the nominalValue of a BlankNode will remain the same between two processing runs. BlankNode nominalValues are only valid for the most recent processing run on the document. BlankNodes nominalValues will often be generated differently by different processors.

Implementers must ensure that BlankNode nominalValues are unique within the current environment, two BlankNodes are considered equal if, and only if, their nominalValues are strictly equal.

### String .toString()

Returns the N-Triples represenation of the BlankNode.

### String .valueOf()

Return the stringified nominalValue.


## Literal(String value, optional String language, optional String datatype, optional any native) extends RDFNode

Literals represent values such as numbers, dates and strings in RDF data.
A Literal is comprised of three attributes:

* a lexical representation of the nominalValue
* an optional language represented by a strAcknowledgementsing token
* an optional datatype specified by a NamedNode

Literals representing plain text in a natural language may have a language attribute specified by a text string token, as specified in [BCP47], normalized to lowercase (e.g., 'en', 'fr', 'en-gb').
They may also have a datatype attribute such as xsd:string.

    //TODO: The RDF Working Group is currently looking at the handling of "Plain Literals", with regards to datatypes, further guidance may result in changes to this specification.

Literals representing values with a specific datatype, such as the integer 72, may have a datatype attribute specified in the form of a NamedNode (e.g., <http://www.w3.org/2001/XMLSchema#integer>).

Literals often represent values for which the host environment of an RDF Interface implementation has a corresponding native value, this value can be accessed by the valueOf method of the Literal interface.

Implementations must provide native value type conversion, via the valueOf method, for the following XML Schema datatypes:

* xsd:string
* xsd:boolean
* xsd:dateTime
* xsd:date
* xsd:time
* xsd:int
* xsd:double
* xsd:float
* xsd:decimal
* xsd:positiveInteger  xsd:integer
* xsd:nonPositiveInteger
* xsd:negativeInteger
* xsd:long
* xsd:int
* xsd:short
* xsd:byte
* xsd:nonNegativeInteger
* xsd:unsignedLong
* xsd:unsignedInt
* xsd:unsignedShort
* xsd:unsignedByte

    //TODO: When a Literal contains both a datatype and a language, and the serialization or stringification does not support a lexical representation of both attributes, language should take precedence.

    //TODO: RDF specifies that Literal equality is based on the lexical representation of values rather than the value space, for this reason the Literals "100"^^xsd:double and "1e2"^^xsd:double are not considered equal.

### NamedNode datatype

An optional datatype identified by a NamedNode.

### String interfaceName

Contains the fixed value "Literal".

### String language

An optional language string as defined in [BCP47], normalized to lowercase.

### String nominalValue

The lexical representation of the Literals value.

### String .toString()

Returns the stringified nominalValue.

### any .valueOf()

This method provides access to a corresponding host environment specific native value, where one exists.

If the datatype identifier of the Literal is not known by the implementation, or the value is outside of the range handled by the corresponding native type, then valueOf must return the DOMString lexical representation of the nominalValue.

The chart below provides a datatype map for the XSD datatypes which must be supported.
Datatype Map:

* Specified Datatype ECMAScript
* xsd:string string
* xsd:boolean boolean
* xsd:dateTime Date
* xsd:date Date
* xsd:time Date
* xsd:int number
* xsd:double number
* xsd:float number
* xsd:decimal number
* xsd:positiveInteger number
* xsd:integer number
* xsd:nonPositiveInteger	number
* xsd:negativeInteger number
* xsd:long number-
* xsd:int number
* xsd:short number
* xsd:byte number
* xsd:nonNegativeInteger number
* xsd:unsignedLong number
* xsd:unsignedInt number
* xsd:unsignedShortnumber
* xsd:unsignedByte number
* xsd:positiveInteger number


## NamedNode(String iri) extends RDFNode

### String .toString()

### String .valueOf()


## Triple(RDFNode subject, RDFNode predicate, RDFNode object)

### boolean .equals(other)

### String .toNT()

### Quad .toQuad(RDFNode graph)

### String .toString()


## Quad(RDFNode subject, RDFNode predicate, RDFNode object, RDFNode graph)

### boolean .equals(other)

### String .toNT()

### String .toString()

### Triple .toTriple()


## Graph(optional Array|Graph other)

Creates a new Graph object and optional adds all triple from other using the .addAll method.

### Graph .add(Quad|Triple triple)

Adds the specified triple to the graph.
This method returns the graph instance it was called on.

### Graph .addAll(Array|Graph other)

Imports the graph in to this graph.
The method accepts any object that provides an Array compatible .forEach method.
This method returns the graph instance it was called on.

This method differes from Graph.merge in that it adds all triples from graph to the current instance, rather than combining the two graphs to create a new instance.

### Graph .clone()

Creates a new Graph and copies all triples toAcknowledgements the new graph object.

### Graph .difference(Graph other)

Creates a new graph that contains all triples which are not included in other.

### boolean .equals(Graph other)

Returns true if the canonical representation of this graph and other graph is equal.

### boolean .every(TripleFilter callback)

Universal quantification method, tests whether every triple in the graph passes the test implemented by the provided TripleFilter.
This method will return boolean false when the first triple is found that does not pass the test.

Note: This method is aligned with Array.prototype.every() in ECMAScript-262.

### Graph .filter(TripleFilter callback)

Creates a new Graph with all the triples which pass the test implemented by the provided TripleFilter.

Note: This method is aligned with Array.prototype.filter() in ECMAScript-262.

### void .forEach(TripleCallback callback)

Executes the provided TripleCallback once on each triple in the graph.

Note: This method is aligned with Array.prototype.forEach() in ECMAScript-262.

### boolean .includes(Quad|Triple triple)

Returns true if the graph contains the given triple.

### Graph .intersection(Graph other)

Creates a new graph that contains all triples which are included in this graph and other graph.

### Array .map(TripleCallback callback)

Executes the provided TripleCallback once on each triple in the graph and returns an Array that contains all returns values of the callback.

Note: This method is aligned with Array.prototype.map() in ECMAScript-262.

### Graph .match(optional RDFNode|RegExp|String subject, optional RDFNode|RegExp|String predicate, optional RDFNode|RegExp|String object, optional RDFNode|RegExp|String graph)

This method returns a new Graph which is comprised of all those triples in the current instance which match the given arguments, that is, for each triple in this graph, it is included in the output graph, if:
Calling .equals() with the specified subject, predicate, object or graph as an arguments returns true, or the argument is null.
This method implements AND functionality, so only triples matching all of the given non-null arguments will be included in the result.

Note: This method always returns a new Graph, even if that graph contains no triples.

### Graph .merge(Array|Graph other)

Returns a new Graph which is a concatenation of this graph and the graph given as an argument.

### Graph .remove(Quad|Triple triple)

Removes the specified triple from the graph. 
This method returns the graph instance it was called on. 

### Graph .removeMatches(optional RDFNode|RegExp|String subject, optional RDFNode|RegExp|String predicate, optional RDFNode|RegExp|String object, optional RDFNode|RegExp|String graph)

This method removes those triples in the current instance which match the given arguments, that is, for each triple in this graph, it is removed, if:

Calling .equals() with the specified subject, predicate, object or graph as an arguments returns true, or the argument is null.
This method implements AND functionality, so only triples matching all of the given non-null arguments will be included in the result.

### boolean .some(TripleFilter callback)

Existential quantification method, tests whether some triples in the graph passes the test implemented by the provided TripleFilter.

This method will return boolean true when the first triple is found that passes the test.

Note: This method is aligned with Array.prototype.some() in ECMAScript-262.

### Array .toArray()

Returns the set of triples within the graph as an Array defined in ECMAScript-262.

Note: The order of the triples within the returned sequence is arbitrary, since a Graph is an unordered set.

### String .toString()

Returns the N-Triples representation of the graph.


## Parser

The Parser is a generic RDF document parser which can be used to either parse the triples serialized within an RDF document in to a Graph, or to process an RDF document by passing each triple found to a TripleCallback for processing.

### Promise parse (any toparse, GraphCallback callback, optional String base, optional TripleFilter filter, optional Graph graph)

Parses the triples serialized within an input RDF document in to a Graph, then executes a given GraphCallback on the populated Graph.

If a TripleFilter is passed to the parser, then each Triple found in the document will only be added to the output Graph if it passes the test implemented by the TripleFilter.

By default a new Graph is provided, however users may optionally specify a Graph to which the parsed triples will be added.
This allows users to provide a custom or persistent Graph implementation, or to automatically merge the triples from several documents in to a single Graph.

* **toparse** The document to parse, the type of argument required may further be constrained by implementations of this interface, for instance an RDFa parser may require an instance of Document, whilst a Turtle parser may require a String.
* **callback** The GraphCallback to execute once the parse has completed, the ParserCallback will be passed a single argument which is the propulated Graph.
* **base** An optional base to be used by the parser when resolving relative IRI references.
* **filter** An optional TripleFilter to test each Triple against before adding to the output Graph, only those triples successfully passing the test will be added to the output graph.
* **graph** An optional Graph to add the parsed triples to, if no Graph is provided then a new, empty, Graph will be used.

### Promise process (any toparse, ProcessorCallback callback, optional String base, optional TripleFilter filter, optional SuccessCallback done)

Parses the triples serialized within an input RDF document and passes each (non filtered) Triple found to a ProcessorCallback, this interface allows RDF documents to be processed by SAX-like parsers whilst maintaining a minimal memory footprint.

If a TripleFilter is passed to the parser, then each Triple found in the document will only be passed to the ProcessorCallback if it passes the test implmented by the filter.

* **toparse** The document to parse, the type of argument required may further be constrained by implementations of this interface, for instance an RDFa parser may require an instance of Document, whilst a Turtle parser may require a String.
* **callback** The ProcessorCallback to execute on each (non-filtered) Triple found by the parser.
* **base** An optional base to be used by the parser when resolving relative IRI references.
* **filter** An optional TripleFilter to test each Triple against before adding to the output Graph, only those triples successfully passing the test will be added to the output graph.


## Serializer

The Serializer is a generic interface implemented by Graph serializers.

Implementations of this interface may further constrain the return type, for instance an RDFa serializer may return an instance of Document, whilst a Turtle serializer may return a String.

### Promise serialize (Graph graph, optional DataCallback callback)

A method, which when called will serialize the given Graph and return the resulting serialization to the DataCallback and Promise.	


## Store

### Promise graph (RDFNode|String namedGraph, GraphCallback callback)

Calls the callback function passing the requested NamedGraph as Graph object.

### Promise match (RDFNode|RegExp|String subject, RDFNode|RegExp|String predicate, RDFNode|RegExp|String object, RDFNode namedGraph, GraphCallback callback, optional unsigned long limit)

Calls the callback function passing the requested NamedGraph as Graph object using SPO matching and limit filtering.

### Promise add (RDFNode|String namedGraph, Graph graph, GraphCallback callback)

Creates or replaces the NamedGraph with the given Graph object and calls the callback function passing the added triples as Graph object.

### Promise merge (RDFNode|String namedGraph, Graph graph, GraphCallback callback)

Merges triples from the given Graph object to the NamedGraph and calls the callback function passing the merged triples as Graph object.

### Promise remove (RDFNode|String namedGraph, Graph graph, SuccessCallback callback)

Removes all triples given as Graph object from the NamedGraph.

### Promise removeMatches (RDFNode|RegExp|String subject, RDFNode|RegExp|String predicate, RDFNode|RegExp|String object, RDFNode|String namedGraph, SuccessCallback callback)

Removes all triples of the NamedGraph based on SPO matches.

### Promise delete (RDFNode namedGraph, SuccessCallback callback)

Deletes the NamedGraph.


## Parsers Parsers (optional Object parsers)

Adds all given parsers to the Parsers object.
The parsers arguments must be a object with the media type as key and an Parser instance as value.
It's possible to assign multiple parsers to one media type using an Array.

### Object .list ()

Returns all parsers in an media type as key, Parser instance as value object.

### Array .findParsers (String mediaType)

Returns an array of parsers assigned to the given media type. 

### Promise .parse (String mediaType, any data, optional GraphCallback callback, optional String base, optional TripleFilter filter, optional Graph graph)

Forwards the .parse call to the Parser instance assigned to given media type.

### Promise .process (String mediaType, any data, ProcessorCallback callback, optional String base, optional TripleFilter filter, optional SuccessCallback done) {

Forwards the .process call to the Parser instance assigned to given media type.

### Stream .stream (String mediaType, Stream inputStream, optional String base, optional TripleFilter filter) {

Forwards the .stream call to the Parser instance assigned to given media type.


## Serializers (serializers)

Adds all given serializers to the Serializers object.
The serializers arguments must be a object with the media type as key and an Serializer instance as value.

### Object .list ()

Returns all serializers in an media type as key, Serializer instance as value object.

### Serializer .findSerializer (String mediaType) {

Returns the Serializer instance for the given media type.

### Promise .serialize (String mediaType, graph, optional DataCallback callback) {

Forwards the .serialize call to the Serializer instance assigned to given media type.

### Stream .stream (String mediaType, Stream inputStream, optional String base) {

Forwards the .stream call to the Serializer instance assigned to given media type.


## DataCallback

### void run (Error error, any data)

A function to be executed on serialized data.


## GraphCallback

### void run (Error error, Graph graph)

A function to be executed on a Graph.


## ProcessorCallback

### void run (Quad|Triple triple)

A function to be executed on a Triple produced by a process operation on an RDF Document.


## SuccessCallback

### void run (Error error, boolean success)

A function to be executed after asynchronous processing is done.


## TripleCallback

### void run (Quad|Triple triple, Graph graph)

A callable function which can be executed on a Triple with a Graph context.


## TripleFilter

### boolean test (Quad|Triple triple)

A callable function that returns true if the input Triple passes the test this function implements.


# Acknowledgements

Many parts of this documents are based on the RDF Interfaces specification.
Therefore the authors of this document would like to thank all contributors of the RDF Interfaces specification.
