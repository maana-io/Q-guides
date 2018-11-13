# Entity Extractor

## Entity Extractor

The Maana Entity Extractor bot reads data from sources passes that data through tagger services and stores the data inside Maana. It recognizes several different types of entities, including:

* Person, location and organization names
* Currency, percentages, and numeric values
* Phone and social security numbers
* Urls and email addresses
* Geospatial coordinates and physical quantities

For each entity mention discovered, the extractor creates links between the originating text and the instance of the entity kind. These links can be used for graph walking and downstream analytics.

## Assumptions

1. Both Maana NER Service and Maana Physical Quantity are available.
2. Kind being processed contain only text.

## Schema

```graphql
type Info {
  id: ID!
  name: String!
  description: String
}

type EntityMention {
  fromKindId: ID
  fromFieldId: ID
  fromInstanceId: ID
  fromSpan: String!
  fromOffset: String!
  toKindName: String!
  toInstanceId: ID
  surfaceForm: String!
}

type Query {
  # information about the service
  info: Info!
  # Bootstrap the service's datatypes and relations
  bootstrap: Boolean!
  # extract entities from the provided source but don't persist the results.  You must provide literals or kindRef.
  extract(
    # literals will be deprecated in the future and a single string will be passed instead
    literals: [String]
    kindId: ID
    fieldId: ID
    kindName: String
    fieldName: String
    modelIds: [ID]
  ): [EntityMention]
  # lookup a physical dimension, or return all the values if no id is provided.
  fieldClassification(id: ID!): FieldClassification
}

input CorrespondenceInput {
  name: String!
  value: String!
}

type Mutation {
  # Extract the entities from a text field.
  extractAndLink(
    kindId: ID
    fieldId: ID
    kindName: String
    fieldName: String
    modelIds: [ID]
  ): Boolean
  # Given a pattern, a kind and a field and that kind, extract triples from the data.
  extractFactsByPattern(
    kindId: ID
    fieldId: ID
    patternKindId: ID
    kindName: String
    fieldName: String
    patternKindName: String
  ): [ID]
  extractTriples(
    kindId: ID
    fieldId: ID
    kindName: String
    fieldName: String
  ): [ID]
  extractByExample(
    kindId: ID
    fieldId: ID
    storageKindId: ID
    fieldName: String
    storageKindName: String
    kindName: String
    mapping: [CorrespondenceInput]
    example: String
  ): [ID]
  extractByExampleKind(
    kindId: ID
    fieldId: ID
    exampleKindId: ID
    kindName: String
    fieldName: String
    exampleKindName: String
  ): [ID]
}

type PatternMatchResult {
  subject: String
  predicate: String
  object: String
  score: Float
}

type FieldClassifiedEvent {
  kindId: ID!
  fields: [FieldClassification]
}

type LinkAddedEvent {
  id: ID!
  name: String
  relationId: ID
  relationName: String
  weight: Float
  fromKindId: ID
  fromKindName: String
  fromFieldId: ID
  fromFieldName: String
  fromInstanceId: ID
  fromOffset: String
  fromSpan: String
  toKindId: ID
  toKindName: String
  toFieldId: ID
  toFieldName: String
  toInstanceId: ID
  toOffset: String
  toSpan: String
}

type Subscription {
  fieldClassified: FieldClassifiedEvent!
  linkAdded: LinkAddedEvent!
}
```

### Entity Extraction

The entity extractor service is invoked manually to extract entities. In the services tab select Maana Entity Extractor and invoke the extractAndLink mutation on the sample\_comments.CSV kind:

```text
mutation 
{
  extractAndLink(kindName:"sample_comments.CSV",fieldName:"comment")
}
```

This will extract entities, creating the instances and links as necessary.

| id | comment |
| :--- | :--- |
| 0 | "Barry Carlson worked on the Well 12345." |
| 1 | "The Well 12345 was drilled by John Davidson." |
| 2 | "The cost to drill this well was $5,345,345." |
| 3 | "The MD was 19,000 ft." |
| 4 | "External casing diameter was 8.625 inches." |
| 5 | "Well 789 was drilled on June 4th, 2019 by Barry Carlson." |
| 6 | "Well 12345 was used as an offset." |
| 7 | "The depth of Well 12345 was 23,400 feet TVD, with casing strings of 5." |
| 8 | "Drilling was by WellCo, Inc." |

The service will extract the following entity mentions, creating the instances of the kinds if they don't already exist:

| surface form | kind |
| :--- | :--- |
| Barry Carlson | Person |
| 12345 | Number |
| $5,345,345 | Currency |
| 5,345,345 | Number |
| 19,000 ft | Physical Quantity |
| 19,000 | Number |
| 8.625 inches | Physical Quantity |
| 8.625 | Number |
| 789 | Numbrer |
| June 4th, 2019 | Date |
| 2019 | Date |
| 23,400 feet | Physical Quantity |
| 23,400 | Number |
| 5 | Number |
| WellCo, Inc | Organization |

it also creates a link between each of the entities and their originating text. The links can be inspected either via graphQL, e.g.:

