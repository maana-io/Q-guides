# Loading Unstructured Data into MAANA

### Loading Unstructured Data into MAANA  

The user can load unstructured data files into MAANA, following the same process used for Structured Data:  

* Create or select the Workspace where you want to load the data. 
* Go to the **Workspaces** tab and either:  
  * Create a Workspace or...
  * Open a Shared Workspace/Template \(refer to the [Opening  and Closing an Existing Workspace](workspaces/#opening-and-closing-existing-workspaces) area mentioned previously in the **Workspaces** section for more details as required\).

 
* Select the **Upload File** button
* Select the desired file\(s\). 

{% hint style="info" %}
**Note**:  It is also possible to simply drag and drop files into the **Canvas** area to upload them into the current workspace.  The platform currently supports the TXT and PDF text file formats, with additional file types to be supported in the near future. 
{% endhint %}

#### Additional Bots and Microservices that Mine Unstructured Data

When unstructured data files are loaded, there are additional bots/microservices that mine the data.  This Entity Extraction includes:  

* Analysis of Comments
* Descriptions
* Whole documents used to identify mentions of:
  * Entities 
  * Values
  * Facts

The Extracted Entities will appear in the **Inventory** Tab, under the MAANA Entity Extractor service, together with information regarding the number of times instances of such entities were identified. The Business Analyst can drag one or all of the extracted Entities to the Canvas area of their screen, making them Kinds within the Knowledge Graph. 

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image019.png)

* **Text Classification**:  Classification of comments, descriptions, or whole documents into preferred groups \(e.g. Comment Problem types - Stuck pipe, Waiting on weather, etc. or Document types - AFE report or Lease Agreement\) – not available yet in UI. 

### Creating, Deleting or Renaming a Kind

TBA

