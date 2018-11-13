# Creating Kinds and Loading Data



### Creating Kinds to Build a Knowledge Graph 

There are two ways to create the Kinds of data used as the building blocks for your Knowledge Graph:

1. The Data-First Approach
2. The Model-First Approach

### The Data-First Approach

#### With the Data-First Approach:

* The User \(you\) uploads the files into the portal.
* The Maana microservices \(bots\) analyze the raw data and generates an _initial_ Knowledge Graph with interpreted Kinds and suggested relations between Kinds. 
* The User then validates these interpretations, and curates these  interpreted Kinds.
* The User can then enrich the initial Knowledge Graph with new Kinds that may be created manually.

### The Model First Approach

With the Model-First Approach:

* The User first manually creates Kinds as a part of a model design.
* The User then connects the manually created Kinds with the data sources \(uploaded as Raw Data Kinds\).

### Loading Data into Maana

{% hint style="info" %}
Note:  Data loading of CSV files is supported by Maana.
{% endhint %}

To load a file into the Maana Portal, simply use our drag-and-drop feature to move the file to the Canvas area of your screen, or you can select the **Upload** button located in the Explorer Panel of the screen and select the file you wish to upload from there.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/FILE%20UPLOAD.png)

The upload results in a Raw Data Kind that appears in the Canvas area of your screen \(refer to above example\).  The Raw Data includes:

* File metadata info
* Data that can be visualized with search-as-you-type filters in the Visualization Panel of your screen.

