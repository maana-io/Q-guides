# Named Entity Recognition

## Named Entity Recognition

Maana Named Entity Recognition service detects entities in text data using:

Stochastic \(CRF-classifier\) and Deterministic \(Token-Regex\) methods. File: /maana-ner-service/src/main/resources/Regex.rules

| n | Entity | CRF | Regex |
| :--- | :--- | :--- | :--- |
| 01 | DateKind | + | + |
| 02 | TimeKind | + | + |
| 03 | Person | + | - |
| 04 | Location | + | - |
| 05 | Organization | + | - |
| 06 | Currency | + | - |
| 07 | Number | + | - |
| 08 | Percentage | + | - |
| 09 | URL | - | + |
| 10 | Email | - | + |
| 11 | PhoneNumber | - | + |
| 12 | SocialSecurityNumber | - | + |
| 13 | IpAddress | - | + |
| 14 | WebLink | - | + |
| 15 | GeoCoordinate | - | + |

## Assumptions

## Schema

```text
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
  """information about the service"""
  info: Info!
  """Bootstrap the service's datatypes and relations"""
  bootstrap: Boolean!
  """extract entities from the provided source but don't persist the results.  You must provide literals or kindRef."""
  extract(
    # literals will be deprecated in the future and a single string will be passed instead
    literals: [String]
    kindId: ID
    fieldId: ID
    kindName: String
    fieldName: String
    modelIds: [ID]
  ): [EntityMention]
  """lookup a physical dimension, or return all the values if no id is provided."""
  fieldClassification(id: ID!): FieldClassification
}

input CorrespondenceInput {
  name: String!
  value: String!
}

type Mutation {
  """Extract the entities from a text field."""
  extractAndLink(
    kindId: ID
    fieldId: ID
    kindName: String
    fieldName: String
    modelIds: [ID]
  ): Boolean
  """Given a pattern, a kind and a field and that kind, extract triples from the data."""
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

type PatternMatchResult {
  subject: String
  predicate: String
  object: String
  score: Float
}

type FieldClassification {
  id: ID!
  fieldId: ID!
  name: FieldType!
  score: Float!
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

## Example

### Query

To extract entities from the following text:

"Reaming down from 6000ft to 8000ft to clear stuck pipe. John, please get that article on www.linkedin.com or [https://google.com](https://google.com) or 192.67.23.222 from file bla123bla.doc and itisme.jpg to me by 5:00PM on Jul 4th 2018 or 4:00 am on 01/09/12 would be ideal, actually. If you have any questions about \"Maana\" or 'Google' office at \"New York\" you can reach my associate at \(012\)-345-6789 or \(230\) 241 2422 or +1\(345\)876-7554 or associative@mail.com or abracadabra123@maana.io&gt;. Send me $5,987.56 or £4,123.14 or € 100 by PayPal. My SSN is 456-23-0965 My coordinates are: 47.617640, -122.191905 or 47°37'03.5\"N 122°11'30.9\"W"

you can go to [http://localhost:9999](http://localhost:9999), which brings up the graphiql interface.

An example query to run with default model would be this:

```text
 {
   extract(sources: ["Reaming down from 6000ft to 8000ft to clear stuck pipe. John, please get that article on www.linkedin.com or https://google.com or 192.67.23.222 from file bla123bla.doc and itisme.jpg to me by 5:00PM on Jul 4th 2018 or 4:00 am on 01/09/12 would be ideal, actually. If you have any questions about \"Maana\" or 'Google' office at \"New York\" you can reach my associate at (012)-345-6789 or (230) 241 2422 or +1(345)876-7554 or associative@mail.com or &lt;abracadabra123@maana.io>. Send me $5,987.56 or £4,123.14 or € 100 by PayPal. My SSN is 456-23-0965 My coordinates are: 47.617640, -122.191905 or 47°37'03.5\"N 122°11'30.9\"W"]) {
     fromSpan
     fromOffset
     toKindName
     surfaceForm
   }
 }
```

and produces the output

```text
 {
  "data": {
    "extract": [
      {
        "toKindName": "Number",
        "surfaceForm": "6000ft to 8000ft",
        "fromOffset": "18",
        "fromSpan": "16"
      },
      {
        "toKindName": "Person",
        "surfaceForm": "John",
        "fromOffset": "56",
        "fromSpan": "4"
      },
      {
        "toKindName": "URL",
        "surfaceForm": "www.linkedin.com",
        "fromOffset": "89",
        "fromSpan": "16"
      },
      {
        "toKindName": "URL",
        "surfaceForm": "https://google.com",
        "fromOffset": "109",
        "fromSpan": "18"
      },
      {
        "toKindName": "IpAddress",
        "surfaceForm": "192.67.23.222",
        "fromOffset": "131",
        "fromSpan": "13"
      },
      {
        "toKindName": "URL",
        "surfaceForm": "bla123bla.doc",
        "fromOffset": "155",
        "fromSpan": "13"
      },
      {
        "toKindName": "URL",
        "surfaceForm": "itisme.jpg",
        "fromOffset": "173",
        "fromSpan": "10"
      },
      {
        "toKindName": "TimeKind",
        "surfaceForm": "5:00PM on",
        "fromOffset": "193",
        "fromSpan": "9"
      },
      {
        "toKindName": "DateKind",
        "surfaceForm": "Jul 4th 2018",
        "fromOffset": "203",
        "fromSpan": "12"
      },
      {
        "toKindName": "TimeKind",
        "surfaceForm": "4:00 am on 01/09/12",
        "fromOffset": "219",
        "fromSpan": "19"
      },
      {
        "toKindName": "Organization",
        "surfaceForm": "Google",
        "fromOffset": "309",
        "fromSpan": "6"
      },
      {
        "toKindName": "Location",
        "surfaceForm": "New York",
        "fromOffset": "328",
        "fromSpan": "8"
      },
      {
        "toKindName": "PhoneNumber",
        "surfaceForm": "-LRB-012-RRB--345-6789",
        "fromOffset": "368",
        "fromSpan": "14"
      },
      {
        "toKindName": "PhoneNumber",
        "surfaceForm": "-LRB-230-RRB- 241 2422",
        "fromOffset": "386",
        "fromSpan": "14"
      },
      {
        "toKindName": "PhoneNumber",
        "surfaceForm": "+1-LRB-345-RRB-876-7554",
        "fromOffset": "404",
        "fromSpan": "15"
      },
      {
        "toKindName": "Email",
        "surfaceForm": "associative@mail.com",
        "fromOffset": "423",
        "fromSpan": "20"
      },
      {
        "toKindName": "Currency",
        "surfaceForm": "$5,987.56",
        "fromOffset": "485",
        "fromSpan": "9"
      },
      {
        "toKindName": "Currency",
        "surfaceForm": "#4,123.14",
        "fromOffset": "498",
        "fromSpan": "9"
      },
      {
        "toKindName": "Currency",
        "surfaceForm": "$ 100",
        "fromOffset": "511",
        "fromSpan": "5"
      },
      {
        "toKindName": "Organization",
        "surfaceForm": "PayPal",
        "fromOffset": "520",
        "fromSpan": "6"
      },
      {
        "toKindName": "SocialSecurityNumber",
        "surfaceForm": "456-23-0965",
        "fromOffset": "538",
        "fromSpan": "11"
      },
      {
        "toKindName": "GeoCoordinate",
        "surfaceForm": "47.617640, -122.191905",
        "fromOffset": "570",
        "fromSpan": "22"
      },
      {
        "toKindName": "GeoCoordinate",
        "surfaceForm": "47°37'03.5``N 122°11'30.9``W",
        "fromOffset": "596",
        "fromSpan": "26"
      }
    ]
  }
}
```

