---
description: >-
  A Maana Knowledge Service for translating between various semi-structured data
  representations, such as JSON, XML, and GraphQL.
---

# Semistructured

## Objects and Arrows

We introduce the [category](../technology-concepts-and-design/category-theory.md), **SemiStruct**, to represent collections of semi-structured formats \(e.g., JSON, XML, RDF, GraphQL\) and transformations between them \(e.g., JSON to XML, XML to GraphQL\).

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

