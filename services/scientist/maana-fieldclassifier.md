# Field Classifier

## Field Classifier

The service runs automatically whenever a kind is added to Maana. It can also be run manually. It creates field classification tags which are visible in the ui. The calculations are stored in the FieldClassification kind inside Maana.

#### Types of Fields:

Field classifier relies on the NER service to do classification so anything the NER service can classify, the field classifier can classify. In addition field classification adds the following flassifications classifications

1.  NULL
2.  TEXT (if field value contains more than 20 (default) words)
3.  CATEGORICAL (if Misc types only and unique values less than 10% (default))

## Assumptions

1. The Maana NER Service is up and running.

### Schema

```ruby
type Info {
  id: ID!
  name: String
  description: String
  srl: Int
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
  classifyFields(kindId: ID!): BotAction
  copyFieldAsKind(
    kindId: ID!
    fieldName: String!
    newFieldName: String!
    newFieldKind: String
    newFieldKindId: ID
    forceAll: Boolean!
  ): Boolean
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

schema {
  query: Query
  mutation: Mutation
  subscription: Subscription
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

### GraphQL interface

The following are currently implemented.

**fieldClassifications**

Return the field classification for a given kind with kind id given by id. The return ids are the field ids while name is the name of the field and score is the confidence score.

```ruby
query {
  fieldClassifications(id: "295ac75b-54d2-4b51-9562-8ecd92808a42") {
    name
    id
    score
  }
}
```

**classifyFields**

Classify the fields of a kind and kickoff and publish a 'fieldClassified' event. Again, id in the classifyFields function is the kindId. This mutation returns a Bot Action, which can be used to update the UI regarding the status of the long-running classifyFields mutation.

```ruby
mutation {
  classifyFields(kindId: "295ac75b-54d2-4b51-9562-8ecd92808a42") {
    id
    created
    lastUpdated
    status
    errors
    service
  }
}
```

**copyFieldAsKind**

This query copies a field and creates a new column with the specific Kind chosen - basically we are typcasting and creating a new column with that type. The instances of that field are stored in the actual kind.

copyFieldAsKind takes the following parameters

- kindId - the id of the kind containing the columns of interest
- fieldName - the field name of the field we will be copying
- newFieldName - the name of the field we want to copy this to (cannot exist yet since it is being created)
- newFieldKind - the kind of the new field. Must be a kind from field classifier, Person, Email, Location, Organization ...
- newFieldKindId - alternatively, the id of the above kind
- forceAll - must be true or false. If true it copies all instance to the new field, if false, it only copies those instances which are actually classified as "newFieldKind"

To test the query upload the [test_data.csv](testdata/test_data.csv) to Maana and create a kind out of it. Using the kind of that kind as "testDataKindId" below. Each column
in this file is a different type. In this case the the column "C3" is of type PhoneNumber, so the query below will create a new column called "C3awesome" filled with ids that refer to the instances in the "PhoneNumber".

```graphql
mutation {
  copyFieldAsKind(
    kindId: testDataKindId
    fieldName: "C3"
    newFieldName: "C3awesome"
    newFieldKind: "PhoneNumber"
    forceAll: true
  )
}
```

After this query is performed you will expect

1.  A new column called "C3awesome" will be in the testDataKind
2.  The contents of "C3awesome" will be ids
3.  The contents of the kind "PhoneNumber" will be the contents of the column "C3" in the testDataKind
4.  The instance ids of PhoneNumber will be the iids found in "C3awesome"
