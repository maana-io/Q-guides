---
description: >-
  A Maana Knowledge Service that translates between various semi-structured
  formats, such as JSON, XML, and GraphQL (types).
---

# Semistructured

## Objects and Arrows

We introduce the [category](../technology/categories.md), **SemiStruct**, to represent collections of semi-structured formats \(e.g., JSON, XML, RDF, GraphQL\) and transformations between them \(e.g., JSON to XML, XML to GraphQL\).

$$
Ob(SemiStruct) : Format \\
Hom(SemiStruct) : (Format \times Format)
$$

### Identity

This category obeys the usual categorical laws, such as identity, composition, associativity, etc.

$$
\forall x \in Format, \exists!\ id \in Hom(SemiStruct) : id(x) = x
$$

For example:

$$
id(JSON) = JSON
$$

### Composition

Given two transformations \(arrows\):

$$
xmlToJson : XML \to JSON \\
jsonToGql : JSON \to GQL
$$

their composition is:

$$
(jsonToGql \circ xmlToJson) : XML \to GQL \\
(jsonToGql \circ xmlToJson)(x) = jsonToGql(xmlToJson(x))
$$

### Associativity

Given three transformations \(arrows\):

$$
xmlToXsd : XML \to XSD \\
xsdToJsons: XSD \to JSONS \\
jsonsToGql : JSONS \to GQL
$$

then the group of execution does not matter:

$$
jsonsToGql \circ (xsdToJsons \circ xmlToXsd) = (jsonsToGql \circ xsdToJsons) \circ xmlToXsd
$$

## Native vs Composed Transforms

We will split our implementation between transformations that are provided via hand-written code/libraries written in a programming language \(e.g., JavaScript, Python, Scala\) and transformations which can be composed purely from other transformations.  For example, there are efficient, low-level implementations of XML to JSON and JSON to GraphQL conversions.  These will be supplied/exposed via a GraphQL endpoints, x`mlToJson` and `jsonToGql`, respectively.

However, there is no direct XML to GraphQL conversion.  Instead, the GraphQL endpoint, xmlToGql, will be implemented as a composition of $$jsonToGql \circ xmlToJson$$.

