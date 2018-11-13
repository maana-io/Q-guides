# The Maana Platform

## 

## The Maana Platform

The Maana platform is used by solutions teams to deliver knowledge applications to their end users \(i.e., business users, SMEs, managers\). This “solution team” approach involves the collaboration of analysts, engineers, and data scientists. The platform itself is designed to build an enterprise-wide knowledge layer, utilizing search and exploration for solution development and delivery.

### The Computational Knowledge Graph

At the core of any Maana solution sits our Computational Knowledge Graph \(CKG\), a network of models built using machine learning techniques and artificial intelligence that powers the AI-driven applications used to digitize decision support and operations. It allows for the knowledge of any business to be incrementally captured and grown, coupling that with ongoing evolution and ever increasing sophistication as more projects are developed.

### Utilizing GraphQL

Maana uses GraphQL to represent and expose its Computational Knowledge Graph. GraphQL is a data query language created by Facebook and open-sourced in 2015 as an alternative to REST interfaces.

GraphQL provides a complete and understandable description of the data in the API, gives clients the power to ask for exactly what they need and nothing more, makes it easier to evolve APIs over time, and enables powerful developer tools. With the GraphQL-type system, developers can access the full capabilities of your data from a single endpoint.

GraphQL creates a uniform API across your entire application without being limited by a specific storage engine. The developer provides functions for each field in the type system, and GraphQL calls them with optimal concurrency.

### GraphQL Features <a id="graphql-features"></a>

GraphQl query language is maintainable, with the ability to evolve an application program interface \(API\) without multiple versioning. With GraphQL, old Clients remain unaffected by new features and deprecating fields discourage new uses. It is an open specification language offering a lean core, multiple implementations and encourages ongoing innovation. Utilizing GraphQL:

* The server is decoupled from the Client
* The server knows almost nothing about Clients
* Apps request exactly the data they need
* It is mush easier to pull data into the Client models.

####  GraphQL models data in an intuitive way: <a id="graphql-models-data-in-an-intuitive-way"></a>

* Nodes have Attributes
* Nodes have links to other Nodes
* It suits our model of the world
* Meshes well with object-oriented programming

#### GraphQL is Composable: <a id="graphql-is-composable"></a>

* It is capable of fetching everything need with only one request.
* It fetches only the data that is needed.
* It has the ability to specify data requirements locally.

GraphQL is strongly typed and introspective, featuring documentation that is built-in, powerful tooling, and the ability to can catch errors easily.

#### GraphQL acts as a single source of truth: <a id="graphql-acts-as-a-single-source-of-truth"></a>

* It supports multiple back ends.
* It can pull data from anything.
* It is resilient.
* It uses existing application logic.
* It is _not_ a storage engine.

#### Maana Platform Architecture and GraphQL

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image%20%289%29.png)

For more information on using on Graph QL, please refer to the link provided below:

* [How to GraphQL](https://www.howtographql.com/)

