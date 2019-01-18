# Fact Recogntion

## Fact Recognition

This service is used for extracting relations, or facts or triples, from text. The service is based on Stanford CoreNLP's Information Extraction library \([https://nlp.stanford.edu/software/openie.html](https://nlp.stanford.edu/software/openie.html)\).

This service could extract information from the phrase "Alex bought a bicycle for 50$." And store a series of triples "Alex", "bought", "bicycle" and "Alex", "bought a bicycle for", "50$". The extracted information can later be used for filling in tables or reasoning. The fact recognition service takes in a kind, a field name, and then set of patterns to perform Information Extraction.

## Assumptions

1. Unstructured text that obeys reasonable rules of grammar to enable the parsing of noun and verb phrases. 
2. Facts are repeated in text to overcome the generally low recall and precision when working with dependency parsing due to challenges with natural language. 

## Warnings

1. The extractByExample and extractByExampleKind are mutations still in research and development, and as such, with certain data, may become long-running.  The use of these mutations should be done with caution.  Future feature releases may address making them more performant and robust.

## Schema

```ruby
type Info {
  id: ID!
  name: String!
  description: String
}

type Query {
  # information about the service
  info: Info!
  # Given a string, extract the sentences
  #extract( kindId: ID, fieldId: ID, instanceIds:[ID], sources: [String]!, modelURL:String): [EntityMention]
  # Takes a string as input and returns the output of Stanford OpenIE
  extractTriples(text: String!): [Triple]
  # Takes a string and pattern as input. Extracts Triples that match the Pattern
  extractByPattern(
    text: String!
    patterns: [TriplePatternInput]!
  ): [PatternMatch]
  #extract Facts by example
  extractByExample(example: String!, text: String!): [Gmap]
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

### HTTP Method: POST

Content-Type: application/json

#### Input to extractTriples

```ruby
query {
    extractTriples(text: "Alex bought a bike") {
        predicatePhrase {
            value
        }
        subjectPhrase {
            value
        }
        objectPhrase{
            value
        }
    }
}
```

#### Output from extractTriples

```ruby
{
  "data": {
    "extractTriples": [
      {
        "predicatePhrase": {
          "value": "bought"
        },
        "subjectPhrase": {
          "value": "Alex"
        },
        "objectPhrase": {
          "value": "bike"
        }
      }
    ]
  }
}
```

#### Input to extractByPattern

```ruby
query {
    extractByPattern(text : "John went to Lynwood and bought a pizza", patterns : [{predicateLemmas : ["purchase"], subjectEntityPattern : ["ANY"], objectEntityPattern : ["ANY"]}]) {
    predicateMatch {
      snippet {
        value
      }
    }
    subjectMatch {
        snippet {
        value
      }

    }
    objectMatch {
        snippet {
        value
      }
    }
    }
}
```

#### Output from extractByPattern

```ruby
{
  "data": {
    "extractByPattern": [
      {
        "predicateMatch": {
          "snippet": {
            "value": "bought"
          }
        },
        "subjectMatch": {
          "snippet": {
            "value": "John"
          }
        },
        "objectMatch": {
          "snippet": {
            "value": "pizza"
          }
        }
      }
    ]
  }
}
```

