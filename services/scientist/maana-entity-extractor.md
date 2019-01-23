# Entity Extractor

## Entity Extractor

The Maana Entity Extractor bot reads data from sources passes that data through tagger services and stores the data inside Maana. It recognizes several different types of entities, including:

- Person, location and organization names
- Currency, percentages, and numeric values
- Phone and social security numbers
- Urls and email addresses
- Geospatial coordinates and physical quantities

For each entity mention discovered, the extractor creates links between the originating text and the instance of the entity kind. These links can be used for graph walking and downstream analytics.

## Assumptions

1. Both Maana NER Service and Maana Physical Quantity are available.
2. Kind being processed contain only text.

## Schema

   <details>
   <summary>
   Click to view
   </summary>

```ruby
type Info {
  id: ID!
  name: String!
  description: String
  # service readiness level: what functionality you can expect from this current version. Link to further info: https://github.com/maana-io/ServiceReadinessLevels
  srl: Int
}

type Query {
  # information about the service
  info: Info!
}

type Mutation {
  # Extract the entities from a text field.
  extractAndLink(
    kindId: ID
    fieldId: ID
    kindName: String
    fieldName: String
    modelIds: [ID]
  ): BotAction
}

enum FieldType {
  BOOL
  INT
  FLOAT
  MONEY
  EMAIL
  IP
  PHONE
  URL
  TIME
  DATE
  SSN
  GEOCOORD
  NAME
  TEXT
}

type FieldClassification {
  id: ID!
  fieldId: ID!
  name: FieldType!
  score: Float!

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
scalar DateTime

scalar JSON

enum BotActionStatus {
  PENDING
  IN_PROGRESS
  STOPPING
  STOPPED
  ERROR
  COMPLETE
}

type BotAction {
  # io.maana.kind
  id: ID!
  # bookkeeping
  created: DateTime!
  lastUpdated: DateTime!
  # disposition
  info: String
  status: BotActionStatus!
  progress: Float
  errors: [JSON!]!
  # operation
  kind: ID
  service: ID!
  eventName: String
  mutation: ID
  query: ID
  parent: ID
}
```

</details>

### Entity Extraction

The entity extractor service is invoked manually to extract entities. In the services tab select Maana Entity Extractor and invoke the extractAndLink mutation on the file/CSV kind. Because this mutation returns a Bot Action, query relevent BotAction fields:

```ruby
mutation
{
  extractAndLink(kindName:"MY_FILE_KIND.CSV",fieldName:"myTextField"){
    id
    created
    status
    lastUpdated
    errors
  }
}
```

The following example CSV of comments would be passed into `extractAndLink` with `fieldName = "comment"`:

| id  | comment                                                                  |
| :-- | :----------------------------------------------------------------------- |
| 0   | "Barry Carlson worked on the Well 12345."                                |
| 1   | "The Well 12345 was drilled by John Davidson."                           |
| 2   | "The cost to drill this well was \$5,345,345."                           |
| 3   | "The MD was 19,000 ft."                                                  |
| 4   | "External casing diameter was 8.625 inches."                             |
| 5   | "Well 789 was drilled on June 4th, 2019 by Barry Carlson."               |
| 6   | "Well 12345 was used as an offset."                                      |
| 7   | "The depth of Well 12345 was 23,400 feet TVD, with casing strings of 5." |
| 8   | "Drilling was by WellCo, Inc."                                           |

The service will extract the following entity mentions, creating the instances of the kinds if they don't already exist:

| surface form   | kind              |
| :------------- | :---------------- |
| Barry Carlson  | Person            |
| 12345          | Number            |
| \$5,345,345    | Currency          |
| 5,345,345      | Number            |
| 19,000 ft      | Physical Quantity |
| 19,000         | Number            |
| 8.625 inches   | Physical Quantity |
| 8.625          | Number            |
| 789            | Numbrer           |
| June 4th, 2019 | Date              |
| 2019           | Date              |
| 23,400 feet    | Physical Quantity |
| 23,400         | Number            |
| 5              | Number            |
| WellCo, Inc    | Organization      |

it also creates a link between each of the entities and their originating text. The links can be inspected either via graphQL, e.g.:
