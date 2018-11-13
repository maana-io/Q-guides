# Business Analyst Guide

## Introduction

### Welcome to Maana

Maana was founded with the vision of using technology to systematize the world’s industrial expertise and data into digital knowledge that could significantly advance the global economy. Our mission is to facilitate the tens of millions of experts working in industrial companies around the world, and give them the ability to make better decisions, faster. We are devoted to putting the power of artificial intelligence \(AI\) into the hands of millions of industry experts, offering enhanced decision-making tools in a rapid response environment.

### Better and Faster

In 2013 Maana invented a new way to represent industrial knowledge mathematically, using the patented Maana Computational Knowledge Graph™. This unique technology enables industrial companies to take human expertise and data from across silos and encode it into digital knowledge, eliminating the need to move data and enabling the creation of thousands of models at scale, through the re-usability of models across the enterprise.  

### Scope

This guide is intended for the use of End Users, and describes how to use our services and applications, and operationalize them in a production setting.

### Glossary

Prior to working with the Maana Platform, it is suggested that you familiarize yourself with some basic terms and concepts that will help the make most out of your Maana portal experience. For a complete Glossary of Terms, please refer to the [Glossary](https://confluence.corp.maana.io/display/MAANA/Maana+Glossary#app-switcher) section of the Maana Corporate site.

## Maana 101

This section is meant to provide a high-level introduction to the Maana Platform.  It introduces key concepts and language, and provides a brief overview of the training topics. 

In a nutshell, Maana is a platform for building an enterprise-wide **knowledge layer** for search, exploration, and solution development and delivery.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/Maana%20Gear%20human%20plus%20data%20final%20copy.png)

### Knowledge and Computational Models

**Knowledge** is an answer to a question. A knowledge layer is _not just a relational database or data warehouse view_ of a complex business.  It is about things - their properties, and complex, dynamic relationships.  We represent such knowledge through _structural_ and _computational_ models.

## Maana Application and Users

#### Who uses Maana?

The Maana platform is used by **Solutions Teams** to deliver **knowledge applications** to you, their End Users \(i.e., Business Users, SMEs, Managers\). 

The Maana **Solution Team approach** involves the collaboration of: 

* Analysts
* Engineers
* Data scientists

The Maana platform supports this collaboration using a simple process of **problem decomposition**. The Maana **Knowledge Portal** provides a shared environment for visual and programmatic development of a solution.

### The Maana Solution

Maana solutions take a knowledge-centric approach to modeling and reasoning about various interconnected problem domains.  

Maana solutions generally follow a simple pattern:

* Observe the problem domain.
* Reason over the problem domain to make \(explicable\) recommendations.
* Decide \(assist the user\) with what action to take.
* Learn from the outcome of the decision in order to make better recommendations

#### Real World Examples

1. Given a set of invoices, what is the optimal collection plan?
2. Given a set of malware victims, who are the most vulnerable likely victims?
3. Given a patient history and a patient condition, what is the most relevant history?
4. Given a set of Bitcoin wallets, what are the most likely fraudulent transactions?
5. Given a customer equipment failure, what is the recommended repair action?
6. Given a set of insurance claims, which are most likely fraudulent?
7. Given a job activity, what are the relevant Health & Safety considerations?

**Composing** and **Reusing** Models and Solutions offers you, the Maana End User:

* **Predictive Maintenance:**  Planned Maintenance, Supply Chain Optimization
* **Trade Finance:**  Shipping and Port Operations
* **Upstream**:  Planning, Production and Capital Allocations

## Logging Into and Out of Maana

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image%20%283%29.png)

### To Log In

1. Go to the web address provided by IT admin.
2. Log in using one of the 2 methods described below:

* Type your user email address \(if single sign-on \(SSO\) is enabled by IT\) and select **Log in**.
*  Log in with a Google identity. 

### Additional Actions

Once logged in, the user can go to the menu provided in the upper right of their screen to:

* Add/change information on their profile \(i.e name, add a picture, email address, etc.\)
* Choose between a Light or Dark portal theme \(see screenshot provided below\).

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image%20%288%29.png)

#### Light or Dark Portal

To change from a Light to a Dark Theme Portal, simply select the **Dark Theme** icon.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/DARK%20PORTAL%20ICON.png)

### To Log Out

Go to the top right section of the Maana screen, select the the **Profile** icon, then the **Log out** option.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image%20%288%29.png)

### The Maana Personal Dashboard

After you sign in, your **Home**, or landing screen will be the first window that opens on your Personal Dashboard.  It has been designed to get you started with the Maana Platform faster, based on relevant items identified by Type.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/PERSONAL%20DASHBOARD.png)

The **Home** screen of the Dashboard \(see example provided above\) features the following sections:

* **Recent Items**
* **Favorite Items**
* **Activity Feed**
* **Quick Start**
* **Help and Learning**

After you have had an opportunity to navigate around the **HOME** screen and investigate some of the areas and **Tutorials** made available for your use, your first step in utilizing the power of the Knowledge Graph is opening a Workspace.  

* You can either go to the **Quick Starts** area of your screen and select **Create a New Workspace** \(if you want to set up a new one\).

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/QUICK%20STARTS.png)

* Or access existing, shared or templates for Workspaces by simply selecting the **WORKSPACES** tab in the ribbon at the top of your screen.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/WORKSPACES%20TAB.png)

When the **Workspaces** Tab is selected, your desktop will shift to display the **Workspaces** screen \(as shown below\).

### The Workspace Screen

Workspaces are dedicated areas within the Maana Portal where Users such as yourself can build, visualize and explore the refined data generated within Computational Knowledge Graphs \(CKG\).

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/WORKSPACE%20SCREEN.png)

In the upper left of your display, is the **My** **Workspaces** area of the screen.  

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/Shared%20Workspaces.gif)

Any Workspaces that have been created previously, either by yourself \(or people in Groups within your organization that have included you\), will be visible here.  You may simply select one of the displayed Workspaces to open it and view the CKG.

Directly beneath **My Workspaces** is the **Shared Workspaces** area.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/Shared%20Workspaces.png)

This area displays Workspaces that multiple users share within your organization, which you are permitted to access.

Beneath **Shared Workspaces** is the **Workspaces Templates** area.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/Workspace%20Templates.gif)

Use the templates found here as a basis to quickly build and customize new Workspaces for your use.

{% hint style="info" %}
**Note**:  Each of these areas include a **Search** functionality for your use, as needed.
{% endhint %}

###  Creating a Workspace

As we mentioned earlier, your Workspace is the key to utilizing the Computational Knowledge Graph \(CKG\) and unlocking the power of the Artificial Intelligence \(AI\) data stream.  If you are not using an existing Workspace, you will want to create one of your own.  This can be done in a few rapid steps.

* First, select the **Workspaces** **Tab** at the top of your **Maana Portal** screen.
* Select the blue circle with the "**+**" icon on it located in the upper left of your screen \(see example below\).  

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/BLIUE%20PLUS.png)

* Your screen should now display a new Workspace that you have created, named **Untitled**, similar to the one shown in the following example.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/Untitled%20with%20Arrow.png)

* Selecting the icon for the **Untitled** Workspace will open a secondary menu bar, where you can name your new Workspace, and decide if you wish to make it Public using the **Context** Panel on the right of your screen \(see example below\).

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image007.png)

{% hint style="info" %}
**Note**:  To close your Workspace, select the "-" button in the red circle located in the upper right corner of your screen.  You will be returned to the original **Workspaces** screen, where you may select other Workspaces from **My Workspaces** or **Shared Workspaces** entries.
{% endhint %}

The various areas in the **Workspaces** tab screen are:

#### The Explorer Panel

This area contains inventories of Knowledge Graphs \(KGs\), Raw Data Kinds, and Interpreted Kinds \(refer to example provided below\).

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/Explorer%20Panel.png)

**Explorer Panel** activities include:

* Creating a new Knowledge Graph.
* Querying a Knowledge Graph.
* * Uploading a file or groups of files into the Workspace.
* Removing a Knowledge Graph from your Workspace.

#### The Inventory Panel

**Inventory Panel** activities include:

* Creating a New Knowledge Graph using the listed **Services**.
* Querying a Knowledge Graph using the listed **Services** \(refer to example provided below with the **Services** menu displayed\).

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/INVENTORY%20WITH%20SERVICES.png)

* Uploading a file or group of files into the Workspace from the listed **Services**.
* Removing a Knowledge Graph from the Workspace.

#### The Visualization Panel

Lists within the Visualization Panel include:

* **Instances**, **Entities** and **Values** for:
  * **Raw Data Kinds**
  * **Interpreted Kinds**.

{% hint style="info" %}
**Note**:  A search-as-you-type filter is available for each Entity.
{% endhint %}

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/VISUALIZATION%20PANEL.png)

**Visualization Panel** activities include:

* Visualization of the **Kinds** and **Data** associated with other Kinds - such as **Instances**, **Entities** and **Values**.

#### The Context Panel

Use the **Context Panel** to create names for Kinds, Property Toggles and to edit Schema.  You may also suggest **Inbound** and **Outbound** Relations \(hasKind\) to Interpreted Kinds in this location.

**Context Panel** activities include:

* **Information Tab** - Modification or deletion of Kinds or their properties.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/Information%20Tab.png)

* **Relations Tab** - Creation of relationships between Kinds or Entities within Kinds.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/Relations%20Tab.png)

* **Text Mining Tab** \(Doc Assist©️\) - Used for document classification.

#### The Canvas Area

The **Canvas Area** offers the user the ability visualize Knowledge Graphs, Kinds and Relations.  In addition, you can manually add, remove, or clone a Kind  in this location - as well as auto-arranging or viewing the entire Graph.  You may also reset the Workspace appearance from this area.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/CANVAS%20AREA.png)

**Canvas Area** activities include:

* Drag-and-Drop files to create or enhance an existing Knowledge Graph.
* Creation or Deletion of Kinds.
* Curate existing Kinds, and the connections between Kinds.
* Visualize and manually explore the Knowledge Graph.

### Workspace Buttons

This is a map of the various options \(as icons\) offered within the Maana KG screen.  The activities mentioned in the following pages utilize or mention a number of these icons, so we would suggest that the User take a moment to familiarize themselves with their function and location on the screen.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/BUTTON%20MAP.png)

{% hint style="info" %}
**Note**:  Sometimes the **Computational Knowledge Graph** \(**CKG**\) may be referred to in its shortened form - **Knowledge Graph** \(**KG**\) - for ease of reference.
{% endhint %}

## Understanding Maana Kinds

Kinds are the term that Maana uses for the data types used to build their Knowledge Graphs.  These "concepts" may have fields containing Properties, Instances with Values \(measurements of size, numbers, etc.\) for Entities within them, and the Relationships \(connections/dependencies\) to other Kinds.

| **Term** | **Definition** | **Example** |
| :--- | :--- | :--- |
| **Kinds** | Kinds are concepts.  | People, ships, Oil wells, Invoices |
| **Fields** | Properties within a concept | Example - Fields related to People:  age, sex, height, weight, etc. |
| **Instances** | Values for entities within a concept | Example - People Instance:  Paul Smith, adult male, blond, right-handed, married. |
| **Values** | A particular size, measure or number within an entity | Example - Values for Paul: 40 yrs old, 180 lbs., 10E shoe size, 6'2". |
| **Relations** | Connections/dependencies that can be established between fields of different Kinds | Married to Linda Smith, related to family name Smith. |

The data generally found in Kinds falls into three categories

1. **Raw Data Kinds**
2. **Interpreted Data Kinds**
3. **Manually Created Kinds**

#### Raw Data Kinds

Raw Data Kinds have data that represents files that have been uploaded into Maana and their corresponding metadata.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/RAW%20DATA%20KINDS%20%281%29.png)

#### Interpreted Data Kinds

Interpreted Data Kinds are generated from Raw Data Kinds after being parsed by Maana Bots/Microservices.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/INTERPRETED%20DATA%20KINDS.png)

#### Manually Created Kinds

Manually Created Kinds are User-defined Kinds and schema.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/MANUALLY%20CREATED%20KINDS.png)

{% hint style="info" %}
**Note**:  Kinds can be displayed in full or compact mode, and the user can toggle between them.
{% endhint %}

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/compact%20size%20KINDS.png)

### The Structure of a Kind

The screen shot displayed below is an example of how a Kind might appear.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/KIND%20STRUCTURES.png)

If you need to edit the properties of the Kind, use the **Context Panel** mentioned earlier \(refer to example presented below\).  

{% hint style="info" %}
**Note**:  You can save your changes to your Kind by using the blue floppy disk icon found in the ribbon at the top of the **Context Panel** 

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/SAVE%20KIND.png)

Conversely, if you wish to delete changes, simply select the red circle with the "**X**" to the right of the save icon.
{% endhint %}

### Creating Kinds to Build a Knowledge Graph 

There are two ways to create the Kinds of data used as the building blocks for your Knowledge Graph:

1. The Data-First Approach
2. The Model-First Approach

### The Data-First Approach

#### With the Data-First Approach:

* The User \(you\) uploads the files into the portal.
* The Maana microservices analyze the raw data and generates an _initial_ Knowledge Graph, with interpreted Kinds and suggested relations between Kinds. 
* The User then validates these interpretations, and curates these interpreted Kinds.
* The User can then enrich the initial Knowledge Graph with new Kinds that may be created manually.

### The Model First Approach

With the Model-First Approach:

* The User first manually creates Kinds as a part of a model design.
* The User then connects the manually created Kinds with the data sources \(uploaded as Raw Data Kinds\).

### Loading Data into Maana

{% hint style="info" %}
**Note**:  Data loading of CSV files is supported by Maana.
{% endhint %}

To load a file into the Maana Portal, simply use our drag-and-drop feature to move the file to the **Canvas** **Area** of your display, or you can select the **Upload** button  located in the **Explorer** **Panel** of the screen and select the file you wish to upload from there.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/UPLOAD%20BUTTON%20RIBBON.png)

The upload results in a Raw Data Kind that appears in the **Canvas Area** of your screen \(see example below\).  The Raw Data includes:

* File metadata info
* Data that can be visualized with search-as-you-type filters in the Visualization Panel of your screen.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/FILE%20UPLOAD.png)

### Adding Interpreted Kinds to the Knowledge Graph

**Interpreted Kinds** are the net result of Bots/Microservices parsing Raw Data Kinds.

{% hint style="info" %}
**Example**:  A Field classifier analyses the Raw Data Kind, and produces interpretations of individual fields and their values based upon comparisons against Kinds and instances \(e.g., People, Places, Organizations, Categorical\) that already exist within the system.  The Named Entity Recognition \(NER\) Microservice then extracts the values corresponding with the identified entities.
{% endhint %}

### How to Add Interpreted Kinds to the Knowledge Graph

1. The User selects the Raw Data Kind they wish to use.
2. Microservices will then identify any Interpreted Kinds in the **Links** tab located in the **Control Panel** to the right of your screen.
3. The User can then choose the Interpreted Kinds they require, and then select **Add to KG** to add them to the Knowledge Graph.
4. The Interpreted Kinds will appear in blue in the Knowledge Graph after they have been added, together with the inbound/outbound relations to other kinds \(refer to the example provided below\).

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/INTERPRETED%20KINDS.png)

### Manually Creating Kinds

The User:

1. Selects **Create New Kind** located in the **Canvas** **Area** of the screen.
2. Names the Kind, and selects the desired properties located in the **Context Panel**.
3. Adds Fields to the Kind schema, using the **Context Panel**.
4. Connects the Kind they have created with a data source, utilizing the [Command Line Interface](https://www.npmjs.com/package/graphql-cli-maana) \(CLI\) controls.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/MANUALLY%20CREATING%20KINDS%201.png)

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/MANUALLY%20CREATING%20KINDS%202.png)

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/MANUALLY%20CREATING%20KINDS%203.png)

#### Maana CLI Commands

{% hint style="info" %}
For a complete list of Maana CLI commands, see the [**Maana Plugin for the GraphQL Command Line page**](https://www.npmjs.com/package/graphql-cli-maana) on [npmjs.com](https://www.npmjs.com/package/graphql-cli-maana).
{% endhint %}

Generic CLI primarily serves to set up the graphqlconfig file.  Maana CLI adds several functions, which allow for specific interaction with Maana Q.  Sample of content on that page:

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/CLI%20COMMANDS%20EXAMPLE.png)

### Editing Existing Kinds

The user:

1. Selects the Kind they desire from the Knowledge Graph.
2. Goes to the **Context Panel** and selects the **Info** tab.
3. Navigates to the **Schema** section and selects the Field they wish to edit.
4. Selects the **Edit** button located in the red ribbon.
5. Edits the Field properties.
6. Selects **Save Field** to save the changes they have made.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/EDITING%20KINDS%201.png)

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/EDITING%20KINDS%202.png)

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/EDITING%20KINDS%203.png)

## Exploring the Knowledge Graph \(KG\)

### How to Query the Knowledge Graph using GraphQL

1. Select **New Query** in the **Explorer Panel**.
2. Navigate to the **Context Panel** on the right ****and enter a name for your Query, then define its Properties.
3. Write the Query using a dedicated window in the **Visualization** tab.
4. Select the **Play** button in the **GraphQL** window to execute your Query.
5. View the Query results in the right-hand panel of the GraphQL.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/NEW%20QUERY.png)





