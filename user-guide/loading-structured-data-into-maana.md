# Loading Structured Data into Maana

### Loading Structured Data into Maana

To load structured data into Maana:

* Create or select the workspace where you want to load data. Go to the **Workspaces** tab and either create a Workspace or open a Shared Workspace/Template \(refer to the [Opening and Closing Existing Workspaces](workspaces/#opening-and-closing-existing-workspaces) area mentioned previously in the Workspaces section for more details as required\). 
* Select the **Upload File** button.  
* Select the file\(s\) you would like to add. 

{% hint style="info" %}
**Note**:  It is also possible to simply drag and drop files into the **Canvas** area to upload them into the current workspace.  The platform currently supports the CSV file format, with more file types to be supported in the near future. 
{% endhint %}

* The uploading file\(s\) will appear on the canvas while a **Progress** indicator in the Explorer tab shows that the file is being processed. 

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image014.png)

Following the completion of the upload process, the system will generate: 

* **Raw Data Kind**\(s\) – Data Kind representing files that were uploaded into Maana and their corresponding metadata.

![Example of Raw Data Kind](https://gitbooktrainingmaterials.blob.core.windows.net/images/image015.png)

* **Interpreted Kind**\(s\)– Kinds generated from the Raw Data Kind following parsing by Maana  bots/microservices. 

Loading data into a **Raw Data Kind** triggers automatic parsing by Maana bots, resulting in the creation of an **Interpreted Kind**, and possibly other subsequent Kinds \(People Names, Company, etc.\) and connections between them, including: 

* **Field Classifications**:  Interpretations of individual fields and their values against known Kinds and Instances \(e.g., People, Places, Organizations, Categorical\).  

{% hint style="info" %}
**Example**:  The Field Classifier bot can suggest that an Entity called **Company Name** is of the type **Company**, where **Company** is a known Kind describing companies doing business in the US\). 
{% endhint %}

* **Entity Extraction**:  Analysis of comments, descriptions, or whole documents to identify mentions of entities, values, and facts. 

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image016.png)

**Interpreted Kinds** have a schemas including Entities that make up that Kind and their suggested Field Types - which can be detected by a Maana Field Classifier microservice as Generic Field Types \(i.e. String, Integer, etc.\) or as Kind Types.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image017.png)

{% hint style="info" %}
**Example**:  Interpreted Kinds appear in blue, with sample instances showing in the Visualization tab. 
{% endhint %}

* **Relations between Kinds** - Maana suggested Relations and/or Dependencies within the associative network. The Business Analyst can review and validate them as part of the Kind Visualization and Curation process.  

![Relations between Kinds](https://gitbooktrainingmaterials.blob.core.windows.net/images/image018.png)

Maana also offers the option to load multiple files via CLI.  See section “5.9 CLI capabilities” for instructions.  



