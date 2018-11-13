# Computational Knowledge Graph Features

## Computational Knowledge Graph Features <a id="computational-knowledge-graph-features"></a>

#### Key Features: <a id="key-features"></a>

* Upload structured and unstructured content
* Bots will automatically interpret, index, mine, and classify content
* Analysts can curate and extend the graph
* Analysts can train and employ text classifiers
* Bots will automatically generate associative networks
* Analysts can search, explore, query, filter, and visualize the graph
* Developers can create and publish knowledge services
* Scaled, multi-tenant Azure deployments

#### Computational Knowledge Graph Functionality <a id="computational-knowledge-graph-functionality"></a>

Maana uses and builds on the core GraphQL technology to make developing solutions a simpler and more intuitive process:

* The Graph is dynamic - Nodes, which represent concepts in the graph, are not static containers, but rather act as computational vessels, allowing algorithms to be stored and executed.
* Reusable Models - Knowledge Graph flexibility enables groups across the organization to leverage and build-upon models created by other groups, dramatically accelerating the speed at which models are created throughout the organization. These models are dynamic, and once operationalized into applications, they learn and adapt based on the user behaviors and provide continuous intelligence for day-to-day operations.
* The structure of data is separated from its content - This separation enables a fluidity of modeling, and data from any source or format can be seamlessly integrated, modeled, searched, analyzed, operationalized and re-purposed.
* Data remains at the source - Only the most relevant data, within the context of what is being optimized, is indexed and brought into the graph.
* Each resulting Data Model is a unique combination of three key components, which are instrumental in optimizing assets and decision flows:  
  * Subject Matter Expertise
  * Relevant Data From Silos 
  * The Right Algorithm

{% hint style="info" %}
**Note**: Algorithms may range from being as simple as pulling in new external source data, to being as complicated as classification of documents through machine learning.
{% endhint %}

The CKG consists of **concepts** and **properties**, **instances** and **values**, and **relations** and **links**.

Consider the concept of a _ContainerShip_ with the properties of _name_, _length_, _position_, etc. A specific instance \(entity\) for our ContainerShip might have values for each of these properties - such as the vessel Maersk Viking having a length of _400 meters._ Such properties can be "scale-able" \(e.g., numbers, strings, dates\) or might refer to other concepts/instances - such as the _Hold Cargo_ of the vessel.

In some cases, property values for an instance may simply be stored, as they are rarely subject to change. In other cases, they will be dynamically computed due to constantly changing values - such as a ship's _weight_ \(which is dependent upon its cargo\) or variables like the vessel’s _current position_ \(which requires getting a GPS reading\).

Unlike a traditional graph database, Maana incorporates arbitrary computation \(through custom GraphQL resolvers\) and distributes the graph into subgraphs managed by different microservices, optionally with their own dedicated persistence mechanism. This separation enables a fluidity of modeling, permitting data from any source and in any format to be seamlessly integrated, modeled, searched, analyzed, operationalized and re-purposed. It also places more responsibility on the microservices to provide their own storage.

### Data Model Split <a id="data-model-split"></a>

To address this, Maana proposes an explicit split between the data models \(i.e., GraphQL type definitions\) that a service uses and its operations \(i.e., GraphQL resolvers\). Maana will generate the appropriate managed service for such models using KindDB, Prisma, neo4j, etc. As a result, the solution developer only has to provide the logic they require, and then lets the system handle of all the CRUD/ORM-like operations on the data.

