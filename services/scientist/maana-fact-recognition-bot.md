# Fact Recogntion Bot

## Fact Recognition Bot

The fact recognition bot calls the fact recognition service and reads the input data from kinds and stores the resulting data in kinds

## Assumptions

1. Unstructured text that obeys reasonable rules of grammar to enable the parsing of noun and verb phrases. 
2. Facts are repeated in text to overcome the generally low recall and precision when working with dependency parsing due to challenges with natural language. 

## Warnings

1. The extractByExample and extractByExampleKind are mutations still in research and development, and as such, with certain data, may become long-running.  The use of these mutations should be done with caution.  Future feature releases may address making them more performant and robust.

## Schema

```ruby
type Query {
  # information about the service
  info: Info!
}

type Mutation {
  # Given a pattern, a kind and a field and that kind, extract triples from the data.
  extractByPattern(
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

type Info {
  id: ID!
  name: String!
  description: String
  srl: Int
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

input CorrespondenceInput {
  name: String!
  value: String!
}

# A snippet of characters extracted from a string.
type Snippet {
  # The location of the starting character of the snippet in the original string
  fromOffset: String!
  # The number of characters in the snippet
  fromSpan: String!
  # The string value of the snippet
  value: String!
  # The normalized value of the string
  normalized: String
}

type Triple {
  id: ID
  snippet: Snippet!
  predicatePhrase: Snippet!
  subjectPhrase: Snippet!
  objectPhrase: Snippet!
  confidence: String
}

type ArgMatch {
  snippet: Snippet!
  matchedTags: [String]
  confidence: String
}
type PredMatch {
  snippet: Snippet!
  confidence: String
}

type PatternMatch {
  pattern: TriplePattern!
  triple: Triple!
  predicateMatch: PredMatch!
  subjectMatch: ArgMatch!
  objectMatch: ArgMatch!
  confidence: String
}

type TriplePattern {
  id: ID
  predicateLemmas: [String]!
  subjectEntityPattern: [String]
  objectEntityPattern: [String]
}

input TriplePatternInput {
  id: ID
  predicateLemmas: [String]!
  subjectEntityPattern: [String]
  objectEntityPattern: [String]
}

type EntityCorrespondence {
  baseEntity: String
  targetEntity: String
  score: Float
}

type PredicateCorrespondence {
  basePredicate: String
  targetPredicate: String
  score: Float
}

type MatchHypothesis {
  predicateCorrespondence: PredicateCorrespondence
  entityCorrespondences: [EntityCorrespondence]
  score: Float
}

type Gmap {
  matchHypotheses: [MatchHypothesis]
  predicateCorrespondences: [PredicateCorrespondence]
  entityCorrespondences: [EntityCorrespondence]
  score: Float
}
```

## Example\(s\)

See tutorials for specific examples

