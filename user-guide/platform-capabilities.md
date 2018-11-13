# Platform Capabilities

## Platform Capabilities

### The Computational Knowledge Graph

The Maana Computational Knowledge Graph™ \(CKG\) is a network of models that optimize specific operations and decisions flows, by providing recommendations through AI-Driven Applications. This unique technology eliminates the need to move data - and enables creation of thousands of models at scale, through the re-usability of models across the enterprise.

{% hint style="info" %}
**Example**:  A working, real-world example of a CKG might be a Knowledge Graph that contains a network of models describing the oil wells dug in the Gulf of Mexico during the last 50 years - including companies, people, equipment and activities involved, etc.
{% endhint %}

### CKG Key Components

| **Term** | **Description** | **Example** |
| :--- | :--- | :--- |
| Kinds | Kinds are Concepts. | Examples: People, Ships, Oil Wells, Invoices   |
| Fields/Entities | Entities are properties within a certain Concept. | Such as entities related to the People concept: age, sex, height, weight, etc. |
| Instances |  A particular set of values for Entities within a Concept. | Such as:  Paul, 40yrs old, male, 6', 180 pounds |
| Values | A particular size, measure, number of an Entity. | Example of a value: 40Example of an instance: Paul, 40yrs old, male, 6', 180pounds |
| Relations | Connections/dependencies that can be established between fields belonging to different Kinds. | A Kind describing an oil well may contain a field of a type of a String showing the name of the company operating that well.  That field has a relation with the Kind containing Company Names. |
| Microservice/  Bots | Microservices are processes that communicate with each other over a network to fulfill a goal using technology-agnostic protocols such as HTTP.Organized around capabilities, e.g., user interface front-end, recommendation, logistics, billing, etc.Microservices can emit events \(act as publishers\) which trigger automatic action\(s\) from other services \(acting as subscribers\) that react to those events.Microservices are small in size, independently deployable and easy to replace. | Examples:  **io.maana.nlp** **service category**Named entity recognition \(NER\) microserviceDoc classification  **io.maana**.minerField classifier |
| String | any arbitrary length character sequence and is the most generic representation \(i.e. it has little meaning\)  |   |

{% hint style="info" %}
**Tip**:  Knowledge Accelerator Tip: check out our short Tutorial \(link to Tutorial doc\) with a specific, dataset-driven example on how you can create and explore a CKG. It will help you move much faster when you will create the first CKG from your own data!
{% endhint %}

### 

