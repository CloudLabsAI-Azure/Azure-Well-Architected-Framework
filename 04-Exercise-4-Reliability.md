# Exercise 4: Reliability

## Overview

The term "reliable workload" refers to a task that is both resilient and available. The ability of a system to recover from failures and continue to function is known as resiliency. After a failure, the goal of resiliency is to restore the application to its original condition. The availability of your workload refers to whether or not your users can access it when they need it.

Building for reliability includes:
* Ensuring a highly available architecture
* Recovering from failures such as data loss, major downtime, or ransomware incidents

Azure has many resiliency features already built into the platform, such as:

* Azure Storage, SQL Database, and Cosmos DB all provide built-in data replication across availability zones and regions.
* Azure managed disks are automatically placed in different storage scale units to limit the effects of hardware failures.
* Virtual machines (VMs) in an availability set are spread across several fault domains. This limits the impact of physical hardware failures, network outages, or power interruptions.
* Availability Zones are physically separate locations within each Azure region. Each zone is composed of one or more datacenters equipped with independent power, cooling, and networking infrastructure. With availability zones, you can design and operate applications, and databases that automatically transition between zones without interruption, which ensures resiliency if one zone is affected.


### **Task 1: Define requirements** 

### **Task 2: Test with simulations and forced failovers** 

In this task, you will learn how to enable replication for virtual machines, run a test failover to validate your replication and disaster recovery strategy, without any data loss or downtime.

### **a. Replicate a Virtual machine**

1. In the Azure portal, click on **Show portal menu (1)** and select **Resource groups(2)**.

   ![](./media/costopt-01.png)

2. Open **wafprod** resource group and select a virtual machine.

   ![](./media/costopt-02.png)

3. From the left pane, select **Disaster recovery (1)** present under _Operations_. Then select a **Target region (2)** from the drop down and click on **Review + Start Replication (3)**.

   ![](./media/reliability-01.png)

4. Review the details and click on **Start replication**.

   ![](./media/reliability-02.png)

5. Once the deployment starts, a new resource group will get created in which a recovery service vault will get deployed.

6. In the search bar, search for Recovery service vaults and select **Recovery services vaults** from the suggestions.

   ![](./media/reliability-09.png)

7. Observe the vaults here, select the **Site-recovery-vault-eastus** vault, that falls under **site-recovery-vault-rg** resource group.

   ![](./media/reliability-06.png)   

8. From the left pane, select **Site Recovery jobs** present under _Monitoring_. You will see all the jobs here with there status.

   ![](./media/reliability-03.png)

9. Now from the left pane, select **Replicated items** present under _Protected items_. You will have the the VM here that you just replicated, select that virtual machine **wafproxxxxxx**.

   ![](./media/reliability-04.png)

10. On the Overview page of the replicated virtual machine, you will have details such as _Health and status, Failover readiness, Errors_ and so on.

   ![](./media/reliability-07.png)

### **b. Create a Recovery plan**

1. In the Recovery Services vault, select **Recovery Plans (Site Recovery)** present under _Manage_ and click on **+ Recovery plan**.

   ![](./media/reliability-10.png)

2. Provide following details for the recovery plan:

* Name: Specify a name for the plan **(1)**.
* Source: Choose a source location from the drop down. The source location must have machines that are enabled for failover and recovery. Here we are using **WestUS (2)** as it's the same location where we have our virtual machine.
* Target: Choose a target location from the drop down **(3)**.
* Allow items with deployment model: Select **Resource Manager (4)** from the drop down.
* Click on **Select items**.

   ![](./media/reliability-11.png)

3. In Select items, select the machine that you want to add to the plan and then click on **OK**.

   ![](./media/reliability-12.png)

   > **Note:** You can only select machines are in the source and target locations that you specified.
   
4. At last, click on **Create**. Once the plan is created successfully, move to next task.

   ![](./media/reliability-13.png)


### **c. Run a test failover to Azure**

1. In the recovery services vault, select **Recovery Plans (Site Recovery)** and click on **failover-recovery-plan**. This is the Recovery plan that you just created.

   ![](./media/reliability-14.png)

2. Now select **Test Failover**.

   ![](./media/reliability-15.png)

3. d
* Choose a Recovery Point: Select **Latest processed (low RTO)** from the drop down. This option fails over all VMs in the plan to the latest recovery point processed by Site Recovery. This option provides a low RTO (Recovery Time Objective), because no time is spent processing unprocessed data.


3. Select an Azure virtual network in which test VMs will be created.

* Site Recovery attempts to create test VMs in a subnet with the same name and same IP address as that provided in the Compute and Network settings of the VM.
* If a subnet with the same name isn't available in the Azure virtual network used for test failover, then the test VM is created in the first subnet alphabetically.
* If same IP address isn't available in the subnet, then the VM receives another available IP address in the subnet. Learn more.
Track failover progress on the Jobs tab. You should be able to see the test replica machine in the Azure portal.

4. To initiate an RDP connection to the Azure VM, you need to add a public IP address on the network interface of the failed over VM. If you don't want to add a public IP address to the virtual machine, check the recommended alternatives here.

5. When everything is working as expected, click Cleanup test failover. This deletes the VMs that were created during test failover.

6. In Notes, record and save any observations associated with the test failover.

7. When a test failover is triggered, the following occurs:

* Prerequisites: A prerequisites check runs to make sure that all conditions required for failover are met.
* Failover: The failover processes and prepared the data, so that an Azure VM can be created from it.
* Latest: If you have chosen the latest recovery point, a recovery point is created from the data that's been sent to the service.
* Start: This step creates an Azure virtual machine using the data processed in the previous step.






### **Task 3: Deploy consistently** 

### **Task 4: Monitor health** 

### **Task 5: Respond to failure and disaster** 

**Site Recovery** helps you keep your applications up and running in the event of planned or unplanned zonal/regional outages. Enabling Site Recovery on your machines at scale through the Azure portal can be challenging. **Azure Policy** can help you enable replication at scale without resorting to any scripting.

In this task, we are going to create a policy assignment for the built-in Azure Site Recovery policy that enables replication for all the VMs in a subscription or resource group.

1. Type **Policy** in the search box located on the top of the Azure Portal page and click on **Policy** to open it.

   ![](./media/ex4-task5-01.png)
   
2. Click on **Assignments** from the left natigation pane under **Authoring**.

   ![](./media/ex4-task5-02.png)
   
3. Select **Assign policy** from the top of the **Policy - Assignments** page.

    ![](./media/ex4-task5-03.png)
    
4. On the **Assign policy**, provide the following details on the **Basic** tab:

   * **Scope**: Select your default subscription.
   * **Exclusions**: Click on ellipses and select **wafprod** resource group. 
   * **Policy definition**: Click on ellipses and search for **Configure disaster recovery on virtual machines by enabling replication via Azure Site Recovery**. Select it.
   * Leave all the other values to default and click **Next**.

    ![](./media/ex4-task5-04.png)

5. On the **parameters** tab of **Assign policy** page, provide the following details:

   * Check only show parameters that need input or review
   * **Source Region**: Central US
   * **Target Region**: East US
   * **Vault Resource Group**: wafprod
   * Leave all the other values to default and select **Next**.

   ![](./media/ex4-task5-05.png)
   
6. On the **Remediation** tab in the **Assign policy** workflow, select the **Create a Remediation Task** checkbox and click on **Next**.

   ![](./media/ex4-task5-06.png)
   
7. Click on **Review and create** to review the selected options, and then select **Create** at the bottom of the page.

   ![](./media/ex4-task5-07.png)
   
   > **Note:** After you assign the policy, wait for up to 1 hour for replication to be enabled.

8. In the Azure portal, click on **Show portal menu (1)** and select **Resource groups(2)**.

   ![](./media/costopt-01.png)
   
9. Open **wafprod** resource group and select the Recovery services vault with the name **wafprodxxxxbackup**.

    ![](./media/ex4-task5-08.png)






































