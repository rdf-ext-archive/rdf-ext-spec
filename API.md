# Interfaces

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

## Literal(String value, String language, String datatype, any native) extends RDFNode

Literals represent values such as numbers, dates and strings in RDF data.
A Literal is comprised of three attributes:

* a lexical representation of the nominalValue
* an optional language represented by a string token
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

## Triple(RDFNode, subject, RDFNode, predicate, RDFNode, object)

### boolean .equals(other)

### String .toNT()

### Quad .toQuad(RDFNode graph)

### String .toString()

## Quad(RDFNode subject, RDFNodepredicate, RDFNode object, RDFNode graph)

### boolean .equals(other)

### String .toNT()

### String .toString()

### Triple .toTriple()

## Graph([Array|Graph other])

Creates a new Graph object and optional adds all triple from other using the .addAll method.

### Graph .add(Quad|Triple quad)

### Graph .addAll(Array|Graph other)

other must provide a .forEach function to loop over all triples.
Returns the own object instance.

### Graph .clone()

Creates a new Graph and copies all Triples to it.

### Graph .difference(Graph other)

### boolean .equals(Graph other)

### boolean .every(Function callback)

### Graph .filter(Function callback)

### undefined .forEach(Function callback)

### boolean .includes(Quad|Triple quad)

### Graph .intersection(Graph other)

### Array .map(Function callback)

### Graph .match(RDFNode|RegExp|String subject, RDFNode|RegExp|String predicate, RDFNode|RegExp|String object, [RDFNode|RegExp|String graph])

### Graph .merge(Array|Graph other)

### Graph .remove(Quad|Triple quad)

### Graph .removeMatches(RDFNode|RegExp|String subject, RDFNode|RegExp|String predicate, RDFNode|RegExp|String object, [RDFNode|RegExp|String graph])

### boolean .some(Function callback)

### Array .toArray()

### String .toString()
