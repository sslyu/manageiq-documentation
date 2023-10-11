## Fulfilling Requests
{: #fulfilling_requests}

### Fulfilling a Request
{: #fulfilling_a_request}

After a request is approved, {{ site.data.product.title_short }} then goes through the steps that are required to complete the request. The steps to be followed for a regular provision from a virtual machine to a virtual machine (not to a template) are found by navigating to the menu: **Automation** > **Embedded Automate** > **Explorer**, then listed under the menu: *DOMAIN* > **Cloud** (or **Infrastructure**) > **VM** > **Provisioning** > **StateMachines** > **VMProvision_VM** > **Provision VM from Template (template)**. The value for each state shows where the instance resides in the **Datastore** accordion. The default set of execution steps is shown in the following table. For more information on state machines, see [State Machines](#state-machines).

### Default Execution Steps in States Instance
{: #default_execution_steps_in_states_instance}

| Step                | Description                                                                         |
| ------------------- | ----------------------------------------------------------------------------------- |
| Customize Request   | Apply customizations.                                                               |
| Acquire IP Address  | Integrates with IPAM (IP Address Management) to get an IP address.                  |
| Acquire MAC Address | Integrates with IPAM to get a MAC Address.                                          |
| Register DNS        | Integrates with IPAM to register with DNS.                                          |
| Register CMDB       | Integrates with CMDB (Configuration Management Database) to register with the CMDB. |
| Register AD         | Integrates with IPAM to register with active directory.                             |
| Placement           | Determine the placement of a virtual machine or instance.                           |
| PreProvision        | Pre-provisioning steps.                                                             |
| Provision           | Create the virtual machine or instance.                                             |
| CheckProvisioned    | Check that the new virtual machine or instance is in the VMDB.                      |
| PostProvision       | Post-provisioning steps.                                                            |
| Register DHCP       | Integrate with IPAM to register the IP address with DHCP Server.                    |
| Activate CMDB       | Integrate with IPAM to activate the virtual machine or instance in the CMDB.        |
| Email owner         | Send an email to the owner that the virtual machine or instance is provisioned.      |

#### Quotas
{: #quotas}

Quotas allow you to establish maximum usage thresholds for a user, group, or tenant for provisioned virtual machines or instances and are integrated into provisioning profiles. These maximums are checked after the approval but before the actual provision request is started. The quota is set for the tenant or group as a whole.

##### Applying User or Group Quotas
{: #applying_user_or_group_quotas}

1. Log in as a user with administrator or super administrator rights to the {{ site.data.product.title_short }} console.

2. Navigate to the menu: **Automation** > **Embedded Automate** > **Explorer**.

3. Copy the menu: **ManageIQ** > **System** > **CommonMethods** > **QuotaStateMachine** > **quota** instance to a custom DOMAIN.

4. From the accordion menu, click menu: *DOMAIN* > **System** > **CommonMethods** > **QuotaStateMachine** > **quota**.

   **Note:** By default, quotas are applied to tenants and do not require any change in the menu: **Automation** > **Embedded Automate** > **Explorer**.

5. Click ![1847](../images/1847.png)(**Configuration**), ![1851](../images/1851.png)(**Edit this instance**).
   ![6300](../images/6300.png)

   1. Set the value for **Quota Source Type** to *user* or *group*.

      **Important:** A user that is creating a provisioning request must have an email address that is saved in their profile, or provisioning can fail. See [Creating a User](../general_configuration/index.html#creating-a-user) in *General Configuration* for details on configuring users.

   2. Set the values for **VM Warning Count**, **VM Maximum Count**, **Storage Warning Limit**, **Storage Maximum Limit**, **CPU Warning Count**, **CPU Maximum Count**, **Memory Warning Limit**, or **Memory Maximum Limit** to be the maximums for a specific user or group.

6. Click **Save**.

##### Using Tags for Owner and Group Quotas
{: #using_tags_for_owner_and_group_quotas}

{{ site.data.product.title_short }} provides tags for enforcing quotas for the owners of virtual machines or instances. Ownership of a virtual machine or instance can be set either during the provisioning process or by using
the **Configuration Set Ownership** button. If a virtual machine or instance has an owner, the value is displayed in the **Lifecycle** section of the virtual machine or instance summary page.

Quota tags can be assigned directly to **either** a group or owner **not** to a configuration item. The following table shows the tags for use in quotas.

| Category Display Name (Name)            | Use                                                      |
| --------------------------------------- | -------------------------------------------------------- |
| Quota Max CPU (quota_max_cpu)         | Sets the maximum number of CPUs across all virtual machines and instances that are owned by the group or user. Sample Values: 1, 2, 3, 4, 5, 10, 20, 30, 40, 50 |
| Quota Max Memory (quota_max_memory)   | Sets the maximum memory across all virtual machines and instances that are owned by the group or user. Sample Values: 1024, 2048, 4096, 8192, 10240, 20480, 40960, 81920 (in MB) |
| Quota Max Storage (quota_max_storage) | Sets the maximum storage across all virtual machines and instances that are owned by the group or user. Sample Values: 10, 100, 1000, 20, 200, 40, 400 (in GB) |

##### Applying a Tag to a User or User Group
{: #applying_a_tag_to_a_user_or_user_group}

1. Click ![config gear](../images/config-gear.png) (**Settings**) > **Application Settings**.

2. Click the **Access Control** accordion, and select the user or group that you want to tag.

3. Click ![1941](../images/1941.png)(**Policy**), then click ![1851](../images/1851.png)(**Edit Tags**).

4. Select the appropriate customer tag to assign, then the value.

5. Click **Save**.

**Note:** When quotas are applied by both automate instance and tagging, the tagged values have higher precedence.

##### State Machines
{: #state_machines}

The automate state machine processes an ordered list of states. It can ensure the successful completion of a step before the next step is run, permit steps to be retried, allow setting a maximum time to retry the state before exiting, and number of retries before exiting the state. Before each state is executed, the `On_Entry` method is executed and after the state ends the `On_Exit` or `On_Error` method is executed based on how the state ends.

The following components make up a {{ site.data.product.title_short }} automate state machine:

| Component     | Description |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| On_Entry      | Method to run when entering the state. It enables you to execute an automate method to do some pre-processing before the state of the state machine is processed. |
| On_Exit       | Method to run when exiting the state. |
| On_Error      | Method to run if an error is encountered when running the state. It enables you to execute an automate method to do some final processing before the state machine finally exits (MIQ\_ABORT) due to the error. |
| Default Value | Runs after the On_Entry method completes (The actual state that is being processed). |
| Max Retries   | Maximum number of times to retry the state before exiting. |
| Max Time      | Maximum time in seconds to retry the state before exiting. |

In the diagram below, you can see how these components combine to create a state machine workflow.

**Note:** The retry logic `On_Entry` and `On_Error` are distinct cases in the program flow.

![2353](../images/2353.png)

**Code snippet that demonstrates the state machine retry logic:**

    # Get current provisioning status
    task = $evm.root['service_template_provision_task']
    task_status = task['status']
    result = task.status

    Then check the result to see how it should proceed:

    case result
    when 'error'
      $evm.root['ae_result'] = 'error'
    .....
    when 'retry'
      $evm.root['ae_result'] = 'retry'
      $evm.root['ae_retry_interval'] = '1.minute'
    when 'ok'
      $evm.root['ae_result'] = 'ok'
    end

    When the result is "retry", it sets:
      $evm.root['ae_result'] = 'retry'
      $evm.root['ae_retry_interval'] = '1.minute'

The following image shows a simple state machine pertaining to approving a provision request. This instance can be found by going to the menu: **Datastore** > **ManageIQ** > **Infrastructure** > **VM** > **Provisioning** > **StateMachines** > **ProvisioningRequestApproval** > **Default**.
![2354](../images/2354.png)

1. The attribute **max_vms** has a value of 1. State machine processing can use the attributes of the state machine instance to make logic decisions. In this case, the **validate_request** method, which is processed during the **On_Entry** portion of the **ValidateRequest** state, evaluates the **max_vms** attribute. If the number of virtual machines requested is less than the **max_vms** value, the request can be auto-approved.

2. **ValidateRequest** is the first state to be executed.

3. **ApproveRequest** is the next state to be executed.

**Note:** Grayed out items reflect values that are set in the class schema. These values can be overwritten on a per instance basis.

##### Customizing Provisioning States
{: #customizing_provisioning_states}

The steps followed when provisioning a virtual machine or cloud instance are completed based on instances from the menu: *DOMAIN* > **Cloud** > **VM** > **Provisioning** > **StateMachines** > **VMProvision_VM** class.

##### Editing the Default State Instance
{: #editing_the_default_state_instance}

1. Navigate to the menu: **Automation** > **Embedded Automate** > **Explorer**.

2. Click **Datastore** accordion. Select **Configuration** > **Add a New Domain**. You must fill the Name and Description values and click **Add** to create a custom domain.

3. From the accordion menu, navigate to **Cloud** > **VM** > **Provisioning** > **StateMachines** > **VMProvision_VM**.

   1. Click the drop-down menu, **Configuration** > **Copy this class**.

   2. To add the class to the custom domain, specify the custom domain in the **To Domain** list and click **Copy**.

4. From the Datastore accordion, navigate to your custom *DOMAIN* > **Cloud** > **VM** > **Provisioning** > **StateMachines** > **VMProvision_VM**.

   1. Click **Schema** tab.

   2. Select **Configuration** > **Edit selected Schema**.

5. You can remove, update, or insert a line in the Editing Class Schema panel.

**Note**: Depending on your environment, you can leave the values blank in a provisioning process. For example, if you are not integrating with IPAM or a CMDB, then you can leave the values blank. ![6281](../images/6281.png)

6. On the Datastore tree, navigate to your custom *DOMAIN* > **Cloud** > **VM** > **Provisioning** > **StateMachines** > **VMProvision_VM**. Click **Schema** tab and select Configuration \> Edit sequence.

7. Resequence the schema in the Edit of Class Schema Sequence panel.

##### Viewing the Status of a Provisioning Request
{: #viewing_the_status_of_a_provisioning_request}

After a request is approved, the various stages of fulfillment are executed. You can see the progress of the provisioning process by viewing its status.

1. Navigate to the menu: **Services** > **Requests**. The list of requests is shown.

2. Click a specific request for more information. When the provisioning begins, if the request was supposed to create more than one virtual machine or instance, a field appears that is called **Provisioned VMs**. Click the number that appears next to it for information on each of the individual provisions.

##### Viewing a Provisioned Virtual Machine or Instance
{: #viewing_a_provisioned_virtual_machine_or_instance }

When a virtual machine or instance is created as a result of a provisioning request, its summary screen shows when it was provisioned in the **Lifecycle** area of the respective summary.

1. From the menu: **Services** > **Workloads**, click the virtual machine or instance that you want to view. ![2356](../images/2356.png)

##### Viewing a Virtual Machine or Instance Summary
{: #viewing_a_virtual_machine_or_instance_summary}

From the menu: **Services** > **Workloads**, click the virtual machine or instance that you want to view.

You can now request the log table from **Services** > **Request** to display logs related to a particular request.

**Note**: The table appears only if there are any logs to display. This table is sorted by the time, severity, and message.
