# Knowledge Microservices

## Knowledge Microservices

Knowledge microservices are a class of GraphQL services developed for the Maana platform. Unlike pure client-server or n-tier architectures, Maana's microservices act as peers in an asynchronous and loosely-coupled arrangement that promotes independent scaling and extensibility. They provide identity and access controls, graph persistence, search, machine learning, and natural language processing.

As a consequence, these peer microservices provide reasoning capabilities to Knowledge Applications, which help solve domain-specific problems and support optimal decision-making that is capable of learning over time. Taken together, these services allow the solution developer to focus on designing GraphQL schemas and implementing computational resolvers only where needed.

The Maana platform manages these services - providing authentication, reliable messaging, \(automatic\) graph persistence \(with search and querying\), scaling, monitoring, and a rich UX. These GraphQL services include:

* Authenticated Access
* Client/Server Boilerplate
* Reliable Messaging using RabbitMQ
* Lifecycle Management \(info, register, deregister\)
* Docker Containerization and Automatic Scaling/Load Balancing

Maana Knowledge Services form a network of GraphQL endpoints, exposing their types, queries, and mutations for direct access, as well as publishing and subscribing to network events. A GraphQL service \(or endpoint\) consists of:

* Types
* Queries
* Mutations
* Events \(pub/sub\)

Examples of Knowledge Microservices include:

| Indexers | Miners | Classifiers |
| :---: | :---: | :---: |
| Text | Statistics | Entity |
| Number | Probabilities | Field |
| Time | NER/NLP | Document |
| Geospatial |  | Image |
| Geometric |  |  |

### Bots and Bot Actions

A **Bot** is a Knowledge Microservice that “listens” for specific events on a system bus, and acts automatically when such events happen to mine and enrich the graph. They provide specialized queries and mutations that perform **BotActions**, allowing the bot to provide asynchronous status updates. 

Knowledge Services can become Knowledge Bots by offering and using these Bot Actions, which allows the service to interact more directly with users.

{% hint style="info" %}
**Example**:  When a raw data file is loaded into MAANA, a bot automatically analyzes it to identify mentions of entities like persons, phone numbers, values, and facts.
{% endhint %}

Users can configure, start, stop, and schedule bot actions. This enables user interface components to immediately return the latest status: 

* Throughout the duration of long-running, asynchronous operations.
* By events that act as automatic triggers \(such as entity recognition, new concept creation and classification\).
* Report any errors or messages.

There are two primary scenarios to consider here: 

1. Event Handling
2. Direct Query/Mutations 

#### Event Handling 

When a Knowledge Service subscribes to and handles an event, such as "fileAdded," it can \(optionally\) create an instance of a BotAction Kind by mutating the Computational Knowledge Graph.

{% hint style="info" %}
**Note**:  As the service performs its operation, it can periodically update the progress \(if it is deterministic\), and update the status and report errors.
{% endhint %}

#### Queries and Mutations 

A user can invoke a query or mutation either explicitly \(e.g., train a classifier on labeled data\) or implicitly \(i.e., as part of query graph\). In such cases, the service exposes such queries and mutations as returning a BotAction.

### Kinds

All Kinds \(concepts, types\) are associated with a service. This service is said to provide the \(entities of\) the Kind. Many Kinds are purely extensional \(i.e., data\) and do not have custom CRUD behavior. These Kinds are automatically managed by the Computational Knowledge Graph \(CKG\) and stored in the KindDB, where they are indexed for efficient search and querying \(including sophisticated entity concurrences\).

Services also depend on existing Kinds and queries, mutations, and events. The services that provide these Kinds \(which may be fully managed by CKG/KindDB\) can be imported into a new service purely through GraphQL and specified in a manifest that is used to create and register a new service. This process will result in a merged schema on a service-specific endpoint that the newly developed service uses for all Maana GraphQL communication \(non-pub/sub\).

### The User Experience

The UI \(Explorer, KGE, Context\) will query for active BotActions and indicate progress and status on relevant Kinds and instances. Using stock functionality, service queries and mutations can be connected to inputs and outputs, and static parameters can be set. Additionally, users can stop/cancel long-running operations, and possibly resume, if the service supports it.

### Asynchronous Results 

One of the primary use cases for BotActions is to support potentially long-running, asynchronous operations. The underlying GraphQL model for queries and mutations is synchronous. 

For example, the operation:

| ![](https://confluence.corp.maana.io/s/en_US/7109/9af699845080e5027a80c674ed9e6d30a44ff7e8/_/plugins/servlet/confluence/placeholder/macro-icon?name=code) Code Block |
| :--- |
| language    js |
| search\(term: String!\): \[Document!\]! |

can be queried as:

<table>
  <thead>
    <tr>
      <th style="text-align:left">
        <img src="https://confluence.corp.maana.io/s/en_US/7109/9af699845080e5027a80c674ed9e6d30a44ff7e8/_/plugins/servlet/confluence/placeholder/macro-icon?name=code"
        alt/>Code Block</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">languagejs</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>const SEARCH_QUERY = gql`query search($term: String!) {</p>
        <p>search(term: $term) {</p>
        <p>id</p>
        <p>name</p>
        <p>...</p>
        <p>}</p>
        <p>}`</p>
        <p></p>
        <p>...</p>
        <p>const documents = await client.query({
          <br />query: SEARCH_QUERY,</p>
        <p>variables: {term: &quot;Kratos&quot;}</p>
        <p>})</p>
      </td>
    </tr>
  </tbody>
</table>IIt is thus expected that the set of documents found by the search operation will be returned as part of a single call, despite it being _Invoked_ asynchronously. 

This is fine for short-lived operations \(i.e., &lt; 1 minute\), but, in practice, some queries \(or mutations\) can take several minutes, hours, or even days \(e.g., complex Spark or TensorFlow jobs\). In such cases, we'd like a [future-like](https://en.wikipedia.org/wiki/Futures_and_promises) mechanism in which we are given some sort of token that can be used to coordinate access to progress and results.

To accomplish this, we propose the following pattern:

| ![](https://confluence.corp.maana.io/s/en_US/7109/9af699845080e5027a80c674ed9e6d30a44ff7e8/_/plugins/servlet/confluence/placeholder/macro-icon?name=code) Code Block |
| :--- |
| language    js |
| searchAction\(term: String!, resultKey: String!\): BotAction!searchResult\(resultKey: String!\): \[Document!\]!  |

In this case, the original operation has been extended to accept an additional argument, **resultKey**, which is used to retrieve the results for a particular invocation. A BotAction instance \(generic\) is returned \(if the operation is initiated successfully\) and can be used to track the progress of the operation \(polling or event-based, errors, etc.\). It also records the resultkey value that was used for convenience. 

The service must also provide a separate query that returns the actual results that have been generated asynchronously. \(Paging, skips, etc. can be added here as appropriate, but are not part of the BotAction protocol.\)

### Schema





