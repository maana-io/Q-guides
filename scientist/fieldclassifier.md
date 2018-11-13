Field Classifier
========

The service runs automatically whenever a kind is added to Maana.  It can also be run manually. It creates field classification tags which are visible in the ui.
The calculations are stored in the FieldClassification kind inside Maana.

### Types of Fields:

1.  NULL
2.  TEXT (if field value contains more than 20 (default) words)
3.  MISC (misclassified)
4.  CATEGORICAL (if Misc types only and unique values less than 10% (default))
5.  BOOL
6.  INT
7.  FLOAT
8.  PHONE
9.  FAX
10. IP (IP Address)
11. EMAIL
12. URL
13. SSN (Social Security Number)
14. GEOCOORD (pair: Latitude, Longitude)
15. LATITUDE
16. LONGITUDE
17. CURRENCY
18. DATE
19. TIME
20. DOCFILE
21. IDNUMBER
22. FORM
23. USSTATE
24. COUNTRYCODE

Assumptions
===========

1.  The Maana NER Service is up and running.

## Schema

```graphql

type Info {
  id: String
  name: String
  description: String
}

type FieldClassification {
  id: ID! #id
  fieldId: ID!
  name: String! # class name
  score: Float! # positive/count
}

type Query {
  #Get information about this service
  info: Info!
  #Get the field classifications with kind id given by id.
  fieldClassifications(id: ID!): [[FieldClassification]]
}

type Mutation {
  classifyFields(kindId: ID!): [FieldClassification]
  copyFieldAsKind(kindId: ID!, fieldName: String!, newFieldName: String!, newFieldKind: String, newFieldKindId: ID, forceAll: Boolean!) : Boolean
}

type FieldClassifiedEvent {
  kindId: ID!
  fields: [FieldClassification]
}

type Subscription {
  fieldClassified: FieldClassifiedEvent!
  linkAdded: LinkAddedEvent!
}

schema {
  query: Query
  mutation: Mutation
  subscription: Subscription
}
```

## GraphQL interface

The following are currently implemented.

#### fieldClassifications

Return the field classification for a given kind with kind id given by id. The return ids are the field ids while
name is the name of the field and score is the confidence score.

```graphql
query {
  fieldClassifications(id: "295ac75b-54d2-4b51-9562-8ecd92808a42") {
    name
    id
    score
  }
}
```

#### classifyFields

Classify the fields of a kind and kickoff and publish a 'fieldClassified' event. Again, id in the classifyFields function is the kindId. The return values are the actual classified field names, ids and confidence scores.

```graphql
mutation {
  classifyFields(kindId: "295ac75b-54d2-4b51-9562-8ecd92808a42") {
    id
    score
    name
  }
}
```