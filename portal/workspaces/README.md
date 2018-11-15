# Workspaces

## Workspaces

A workspace is a dedicated place within the MAANA portal where a user can build, visualize and explore their Maana Computational Knowledge Graph, or CKG. The Maana portal home page features a tab called **Workspaces**, where the user can find:

* **My Workspaces** – This area displays the workspaces that the User has previously created.
* **Shared Workspaces** – This area displays the workspaces that multiple users share.
* **Workspace Templates** – This area can be used to create a new workspace from an existing template.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image%20%287%29.png)

### My Workspaces

Once a workspace is created it will automatically be saved, and will appear in the **My Workspaces** area of the **Workspaces Tab**.

### Shared Workspaces

A user can share their workspace _only_ with other users within the same tenant.

#### To Share a Workspace:

* Right-click on the Workspace name and select the **Sharing** option.
* Type the email addresses of the users you would like to add and then select **Share**.

#### To Remove a User from a Shared Workspace:

* Right-click on the workspace name and select the **Sharing...** option.
* Type the email addresses of the users to be removed and select **Remove Sharing**.

### Workspace Templates

Using Workspace templates can speed up building knowledge solutions as they may come with ready-built Knowledge Graphs, Kinds, Micro-sServices, etc.

#### To Create a Workspace Template:

* Move to the **My Workspaces** area in the **Workspaces** tab.
* Right-click on a Workspace, and select **Make Template**.
* Select **Yes** when the confirmation message that appears.

The newly created Workspace Template will appear in the Templates section. You can further share the template with other users within the same tenant by right-clicking on a template, and then selecting the **Sharing…** option. Then select the users you wish. This will add the users you want the template to be shared with.

### Creating, Renaming and Deleting a Workspace

#### To Create a New Workspace:

* Select the **Workspaces** tab at the top of the Maana portal.
* Select the **+** on the top right of the new menu ribbon that appears. 

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image%20%285%29.png)

A new workspace with the name **Untitled** is created.

#### To Rename a Newly created Workspace:

* Change the naming convention of the Workspace  you want to your choice in the **Name** field of the **Context Panel** which appears on the right side of the screen.
* Scroll down to the bottom of the **Context Panel** and select **Save**.

#### To Delete a Workspace:

{% hint style="info" %}
**Note**: The user can only delete workspaces that they have created.
{% endhint %}

* Select the **Workspaces** tab at the top of the Maana portal.
* Right-click on the Workspace you wish to delete and select the **Delete** option.
* The system will display a message asking for confirmation. 
* Select **Yes** to confirm deletion.

{% hint style="warning" %}
**Caution**: Deleting a Workspace may impact applications, services and users connected to it. Contact your Maana platform Administrator or Maana Customer Support to verify the impact of a deletion _prior_ deleting workspaces. The Maana platform Administrator can also delete a workspace following a request from end-user.
{% endhint %}

### Opening and Closing Existing Workspaces

#### Opening an Existing Workspace:

Go to the **Workspaces** tab in the horizontal menu at the top of the screen and select a workspace from **My Workspaces**, a shared workspace from the **Shared Workspaces** group, or a template from the **Workspace Templates** group.

{% hint style="info" %}
**Note**: The secondary ribbon menu under the **Workspaces** tab shows the Workspaces that are currently open. Selecting any of the names in that area will take the user to the respective workspace.
{% endhint %}

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image006.png)

#### Closing an Existing Workspace:

Go to the **Workspaces** tab in the horizontal menu at the top of the screen and select a workspace name in the secondary horizontal menu. Select the minus sign to the right of the list of workspace names.

### Working within a Workspace

The workspace features several areas with specific functionality that enables the user to create, manage, visualize and explore Knowledge Graphs and their components.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image007.png)

#### Explorer Panel Area

The Explorer Panel area of the screen allows the user to:

* Create or clone a new Knowledge Graph.
* Query a Knowledge Graph.
* Upload a file or a group of files to create or enhance a Knowledge Graph.
* Create a new Query.

#### The Canvas Area

The Canvas area of the screen enables the user to:

* Drag and Drop files to create/enhance a Knowledge Graph.
* Create or Delete Kinds.
* Curate existing Kinds and the connections between them.
* Visualize and manually explore the Knowledge Graph.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image008.png)

#### The Context Panel

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image009.png)

There are number of Tabs on the Context Panel:

* The Information Tab
  * Enables the user to
    * Modify Kinds and their properties
    * Delete or completely remove Kinds from the Knowledge Graph.
* Relations Tab
  * Used to create relations between Kinds or entities within Kinds.
* Text Mining Tab
  * Used for document classification.
* Filtering tab - ???
* Other tab- ???

#### The Inventory Panel

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image010.png)

The Inventory Panel contains an inventory of Micro Services, Files and Kinds used in the workspace.

#### The Visualization Panel

The Visualization Panel allows the user to visualize Kinds and data associated with those Kinds like Instances, Entities and Values. Raw Data Kinds can be explored quickly using the visualization panel, as each entity detected in the data file can be filtered using a “search as you type” capability.

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image011.png)

The visualization panel can also be used to query a Knowledge Graph using GraphQL \(refer to example provided below\).

![](https://gitbooktrainingmaterials.blob.core.windows.net/images/image012.png)

