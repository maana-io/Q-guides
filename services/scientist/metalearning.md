Metalearning
========

Maana Metalearning service allows one to automatically create machine learning models from data in Maana. It creates knowledge in the knowledge graph by having classifiers, featurizers and their attributes as first class citizens, allowing us to reason over them. This work is presented in the following papers:

F. Yang, D. Lyu, B. Liu, and S. Gustafson, Peorl: Integrating symbolic planning and hierarchical reinforcement learning for robust decision-making, International Joint Conference of Articial Intelligence (IJCAI), 2018.

Fangkai Yang, Steven Gustafson, Alexander Elkholy, Daoming Lyu, and Bo Liu, Program search for machine learning pipelines leveraging symbolic planning and reinforcement learning, Genetic Programming Theory and Practice XVI (Ann Arbor, USA) (Wolfgang Banzhaf, Leigh Sheneman, Lee Spector, and Bill Tozier, eds.), Springer, 19-21 May 2018, Forthcoming.

Assumptions
===========

1.  There is a labeled dataset for use.
2.  The dataset has been cleaned up, features are ready to use.

Warnings
========

Schema
======

```
input ClassifyInstanceInput {
  trainedModelId: ID!
  data: [JSON]!
}

input ClassifyKindInput {
  modelName: ID!
  kindId: ID!
  predictionFieldName: String!
}

type DataSet {
  id: ID!
  name: String
  policies: [MetaPolicy]
  finalModel: MachineLearningModel
  failedModels: [MachineLearningModel]
  targetAccuracy: Float
  timeToLearn: Float
  summary: DecisionSummary
}

type FeatureColumn {
  id: ID
  name: String
  feature: String
}

input FeaturizerSettingInput {
  fieldName: String!
  featurizer: String!
}

type GradientBoostingClassifier {
  id: ID
  features: [FeatureColumn]
  preprocessor: Preprocessor
  saved: Boolean
  accuracy: Float
  labels: [Label]
  trees: Int
  loss: String
  maxDepth: Int
  maxFeatures: String
  minSamplesSplit: Float
  minSamplesLeaf: Float
  minImpurityDecrease: Float
}

type Info {
  id: ID!
  name: String!
  description: String
}

type Label {
  id: ID
  name: String
  precision: Float
  recall: Float
  fBetaScore: Float
  support: Int
}

type LogisticClassifier {
  id: ID
  features: [FeatureColumn]
  preprocessor: Preprocessor
  saved: Boolean
  accuracy: Float
  labels: [Label]
  norm: String
  tolerance: Float
  C: Float
  balance: Boolean
  solver: String
  maxIterations: Int
}

type LSVClassifier {
  id: ID
  features: [FeatureColumn]
  preprocessor: Preprocessor
  saved: Boolean
  accuracy: Float
  labels: [Label]
  loss: String
  norm: String
  dual: Boolean
  tolerance: Float
  C: Float
  multiClass: String
  fitIntercept: Boolean
  interceptScaling: Float
  balance: Boolean
  maxIterations: Float
}

type MachineLearningModel {
  id: ID
  modelId: ID
  kindName: String
  kindId: ID
  features: [FeatureColumn]
  preprocessor: Preprocessor
  saved: Boolean
  timeToLearn: Float
  accuracy: Float
  labels: [Label]
}

type MetaPolicy {
  id: ID
  actions: [MetaPolicyAction]
  failed: Boolean
  quality: Int
  rho: [RhoTable]
  accuracy: Float
}

type MetaPolicyAction {
  id: ID
  name: String
  timestamp: Int
}

type MultiNBClassifier {
  id: ID
  features: [FeatureColumn]
  preprocessor: Preprocessor
  saved: Boolean
  accuracy: Float
  labels: [Label]
  alpha: Float
  fitPrior: Boolean
  classPrior: Boolean
}

type Mutation {
  trainClassifierCsv(input: TrainCSVInput!): DataSet
  trainClassifierKind(input: TrainingKindInput!): Boolean
  classifyInstance(input: ClassifyInstanceInput!): [String]
  classifyKind(input: ClassifyKindInput!): Boolean
  stopMetaLearningAndSaveBest: String
  stopCurrentPhase: String
  addPreprocessor(input: String!): Boolean
  removePreprocessor(input: String!): Boolean
  changeFeaturizerSetting(input: FeaturizerSettingInput!): Boolean
  restoreFeaturizerSetting: String
  stopCurrentTasks: Boolean
}

type Preprocessor {
  id: ID
  name: String
}

type Query {
  info: Info
  AllLogisticClassifiers: [LogisticClassifier]
  LogisticClassifier(id: ID): LogisticClassifier
  AllRandomForestClassifiers: [RandomForestClassifier]
  RandomForestClassifier(id: ID): RandomForestClassifier
  AllGradientBoostingClassifiers: [GradientBoostingClassifier]
  GradientBoostingClassifier(id: ID): GradientBoostingClassifier
  AllSGDClassifiers: [SGDClassifier]
  SGDClassifier(id: ID): SGDClassifier
  AllLSVClassifiers: [LSVClassifier]
  LSVClassifier(id: ID): LSVClassifier
  AllMultiNBClassifiers: [MultiNBClassifier]
  MultiNBClassifier(id: ID): MultiNBClassifier
  AllDataSets: [DataSet]
  DataSet(id: ID): DataSet
}

type RandomForestClassifier {
  id: ID
  features: [FeatureColumn]
  preprocessor: Preprocessor
  saved: Boolean
  accuracy: Float
  labels: [Label]
  trees: Int
  criterion: String
  maxDepth: Int
  maxFeatures: String
  minSamplesSplit: Float
  minSamplesLeaf: Float
  minImpurityDecrease: Float
  bootstrap: Boolean
}

type RhoTable {
  id: ID
  score: Float
  model: MachineLearningModel
  features: [FeatureColumn]
  preprocessor: Preprocessor
}

type SGDClassifier {
  id: ID
  features: [FeatureColumn]
  preprocessor: Preprocessor
  saved: Boolean
  accuracy: Float
  labels: [Label]
  loss: String
  norm: String
  alpha: Float
  L1Ratio: Float
  fitIntercept: Boolean
  maxIterations: Float
  tolerance: Float
  epsilon: Float
  learningRate: String
  etaZero: Float
  balance: Boolean
}

input TrainCSVInput {
  modelName: ID
  accuracy: Float
  dataFolder: String!
  labelField: String!
  featureFields: [String]!
  featureTypes: [String]!
  folds: Int
  candidateModels: [String]
  featurizerOverride: [FeaturizerOverride]
  candidatePreprocessors: [String]
  modelProfilingEpisodes: Int
  modelSearchEpisodes: Int
}

input TrainingKindInput {
  modelName: ID
  accuracy: Float
  kindId: ID!
  labelField: String!
  featureFields: [String]
  excludeFields: [String]
  featureTypes: [String]!
  folds: Int
  candidateModels: [String]
  featurizerOverride: [FeaturizerOverride]
  candidatePreprocessors: [String]
  modelProfilingEpisodes: Int
  modelSearchEpisodes: Int
}
```

The main interface to Maana Metalearning is through the graphIQL endpoint, which allows one to train a classier from kinds which exist in Maana. For the rest of this section, assume a kind exists in Maana with a kind id, a number offields which are features to train on, and a target feature to predict.

* accuracy: The minimum target accuracy to try to achieve.
* kindId: The kind id of the data to train on which exists in Maana.
* excludeFields: (optional) If there are anyelds the user wishes to exclude from the feature set to train on, they are listed here.
* featureFields: (optional) Opposite excludeFields, the user may specify only thoseelds which are to be trained on, rather than only those to exclude.
* candidateModels: This is a list of classication algorithms to try. The models available are generally those in scikit learn[9].
* candidatePreprocessors: This is a list of preprocessors to try, from scikit learn. Additionally, the preprocessor 'noop' is allowed, which species that no preprocessor is to be used.
* folds: (optional) The number of cross-validation folds to use. Defaults to 10.
* modelProfilingEpisode: This is the number of runs to do in phase 1. It is the number of times a pipeline is to be tried before moving on. Recommended is 4
* modelSearchEpisode: This is the number of runs to do in phase 2. It is the number of times to test the chosen pipeline tofind the best parameter set. Recommended is 8.

Additionally, the process can be changed during run time. We allow the following mutations to control the flow of the algorithm:
* StopMetaLearningAndSaveBest: This will stop the entire process and save the best model it has found so far.
* StopCurrentPhase: This will stop the current phase and move onto the next with the best pipeline it has found so far.
* StopCurrentTasks: This will stop the current pipeline it is testing. If, for example, one classier is taking much longer than expected, the user can stop the current process and move onto the next pipeline to test in whatever phase it is in.
* ChangeFeaturizerSetting: This will allow the user to change what featurizers are being used in the process.

Example(s)
==========

#### Input 1
```ruby
    # Input 1
    mutation training
    {
      trainClassifierKind(
        input:
          {
            accuracy: 0.70,
            kindId: "52238b88-01c5-404a-96c9-9cf79ffd1786",
            labelField:"salary", # The field name in the kind to try and learn / predict.
            excludeFields:["id"], # Optionally specificy fields to exclude.
            # featureFields:["age","education","occupation","sex","nativecountry"], # Optionally choose only those fields to include
            featureTypes: ["interger", "categorical", "categorical", "categorical", "categorical", "categorical", "categorical", "categorical", "categorical", "categorical"],
            candidateModels: ["random_forest_classifier", "logistic_classifier"],
            candidateFeaturizers:["bag_of_words"],
            candidatePreprocessors:["noop"],
            folds:10, # This is the number of cross validation folds to use. Default and a good pick is 10
            modelProfilingEpisodes:10,
            modelSearchEpisodes:50
          }
      )
    }
```
#### Output 1
```ruby
# Output 1
{

   "data": {

    "trainClassifierKind": true

    }

}
```

#### Input 2

```ruby
# Input 2
mutation classify_inst {

  classifyInstance(input: {

    trainedModelId: "<modelId>",

    data:["{\r\n\"age\":39,\r\n\"workclass\":\"State-gov\",\r\n\"education\":\"Bachelors\",\r\n\"maritalstatus\":\"Never-married\",\r\n\"race\":\"White\",\r\n\"sex\":\"Male\"\r\n}"]

  })

}


```

#### Output 2
```ruby
# Output 2
{

  "data": {

    "classifyInstance": [

      "<=50K"

    ]

  }

}

```
#### Input 3
```ruby
# Input 3
query {

  extractXHTML(

    file: {

      id:"SOME\_FILE\_ID",

      name: "SOME\_FILE\_NAME",

      url: "http://SOME\_BASE\_URL/SOME_FILE.xls",

      status: 200

    }

  )

}
```
