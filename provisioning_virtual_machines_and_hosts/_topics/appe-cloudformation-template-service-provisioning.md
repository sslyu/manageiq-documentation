# Example: Using Amazon CloudFormation Template for Service Provisioning
{: #ex_using_cloudformation_template_for_service_provisioning

Cloud orchestration is a service that you can use to create, update, and manage cloud resources and their software components as a single unit and then deploy them in an automated, repeatable way through a template. The deployed instances and associated collection of resources are referred to as stack. {{ site.data.product.title_short }} supports Amazon CloudFormation orchestration templates that make deploying complex services easier in the cloud.

## Creating an Orchestration Template
{: #creating_an_orchestration_template}

Complete the following procedure to add new orchestration templates.

1. Navigate to the menu: **Services** > **Catalogs** and expand the **Orchestration Templates** accordion.

2. Click ![Configuration](../images/1847.png) **Configuration**, then click ![Green Plus Sign](../images/1848.png) **Create a new Orchestration Template**.

3. Enter a **Name** and **Description** for your template.

4. Select **Amazon CloudFormation** from the **Template Type** list.

5. Select **Draft** to create a draft template.

6. Add your template in the area that follows for the selected **Template Type**. You can author your own stack template, or you can copy and paste from an existing text file.

7. Click **Add**.

You can add this template as a catalog item to a service catalog. Stacks can then be created from templates and launched from the service catalog by using a service dialog.

## Creating a Service Dialog from an Orchestration Template
{: #creating_a_service_dialog_from_an_orchestration_template}

Complete the following procedure to create a service dialog based on the input parameters defined in the orchestration template.

1. Navigate to the menu: **Services** > **Catalogs** and expand the **Orchestration Templates** accordion.

2. Expand **All Orchestration Templates**. Then, click the orchestration template that you created by using the preceding procedure, that you want to create a service dialog from.

3. Click ![Configuration](../images/1847.png) **Configuration**, then click ![Green Plus Sign](../images/1848.png) **Create Service Dialog from Orchestration Template**.

4. Enter a name for the service dialog in **Service Dialog Name**.

5. Click **Save**.

## Creating a Catalog
{: #creating_a_catalog}

Complete the following procedure to create a new catalog.

1. Navigate to the menu: **Services** > **Catalogs** and expand the **Catalogs** accordion.

2. Click ![Configuration](../images/1847.png) **Configuration**, then click ![Green Plus Sign](../images/1848.png) **Add a New Catalog**.

3. In **Basic Info**, add values for the **Name** and **Description** for the new catalog.

4. You can assign catalog items in **Assign Catalog Item**.

5. Click **Add**.

## Creating an Orchestration Catalog Item
{: #creating_an_orchestration_catalog_item}

Complete the following procedure to create a new orchestration catalog item.

1. Navigate to the menu: **Services** > **Catalogs** and expand the **Catalog Items** accordion.

2. Click ![Configuration](../images/1847.png) **Configuration**, then click ![Green Plus Sign](../images/1848.png) **Add a New Catalog Item**.

3. Select **Orchestration** from the **Catalog Item Type** list.

4. Enter the basic details in the **Basic Info**:

   1. Enter a **Name** and **Description** for the new service catalog item.

   2. Select **Display in Catalog** box.

   3. Select the appropriate catalog from the **Catalog** list.

   4. Select the appropriate dialog from the **Dialog** list.

   5. Select the orchestration template from the **Orchestration Template** list.

5. Click **Add**.

## Ordering a Service
{: #ordering_a_service}

Complete the following procedure to order your catalog item from the service catalog.

1. Navigate to the menu: **Services** > **Catalogs** and expand the **Service Catalogs** accordion.

2. Expand **All Services**. Then, click the catalog item that you want to order from the service catalog.

3. Click **Order**. The **Order Service** window with **Options** and **Parameters** displays.

4. Enter the name of the stack in **Stack Name**.

5. Select the value of **On Failure** if stack creation fails. The default is **Rollback**.

6. Optionally, enter **Timeout** in minutes.

7. Set the remaining parameters as needed, which can vary depending on the dialog.

8. Click **Submit**.

The provisioning service request is submitted. When a request is approved, the various stages of fulfillment are processed. You can monitor the request status and other details in the menu: **Services** > **Requests**.

## Orchestration Stacks
{: #orchestration_staks}

1. When the status of the provisioning request in the menu: **Services** > **Requests** is **Finished**, click menu: **Compute** > **Clouds** > **Stacks** to see the newly deployed stack.

2. Click the stack to see a summary of its properties, resources, among other details, which includes the running instances that are part of the stack. ![Catalog Item State](../images/7180.png)
   ![Stack Summary](../images/7181.png)

You have now deployed instances and associated collection of resources (referred to as a stack) by using an orchestration template.
