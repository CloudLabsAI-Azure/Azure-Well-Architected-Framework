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
* Availability Zones are physically separate locations within each Azure region. Each zone is composed of one or more data centers equipped with independent power, cooling, and networking infrastructure. With availability zones, you can design and operate applications, and databases that automatically transition between zones without interruption, which ensures resiliency if one zone is affected.


### **Task 1: Define requirements** 

Gathering requirements is the first step in incorporating resiliency (recovering from failures) and availability (operating in a healthy condition without significant downtime) into your applications. 

For example, how much downtime is acceptable? What is the cost of potential downtime to your company? What are the requirements for your customer's availability? How much money do you put into making your app widely available? What is the expense versus the risk?

The following are some of the considerations you should make in Azure to improve reliability and satisfy business goals.

 #### 1. Availability targets
 
  - A **Service Level Agreement (SLA)**, is an availability target that represents a commitment to the performance and availability of the application.
  
  - Understanding the SLA of individual components within the system is essential to define reliability targets. Knowing the SLA of dependencies will also provide a justification for additional spend when making the dependencies highly available and with proper support contracts.
  
  - Monitoring and measuring application availability is vital to qualifying overall application health and progress towards defined targets.
 
 #### 2. Recovery targets
 
 - **Recovery targets** identify how long the workload can be unavailable and how much data is acceptable to lose during a disaster. 
 
 - Recovery targets are non-functional requirements of a system and should be dictated by business requirements. They should be defined in accordance with the required Recovery Time Objective (RTO) and Recovery Point Objective (RPO) targets for the workloads.

 - **Recovery Time Objective (RTO)** is the maximum acceptable time an application is unavailable after a disaster, and **Recovery Point Objective (RPO)** is the maximum duration of data loss that is acceptable during a disaster.


 #### 3. Meet application platform requirements
 
  - Azure application platform services offer resiliency features to support application reliability, they will only be applicable at a certain SKU and configuration/deployment.

   * **Multiple and paired regions**: The ability to respond to disaster scenarios for overall compute platform availability and application resiliency depends on the use of multiple regions or other deployment locations.
   
   * **Availability Zones and sets**: An Availability Set (AS) is a logical construct to inform Azure that it should distribute contained virtual machine instances across multiple faults and update domains within an Azure region. Availability Zones (AZ) elevate the fault level for virtual machines to a physical datacenter by allowing replica instances to be deployed across multiple data centers within an Azure region. 
   
 #### 4. Meet data platform requirements
 
  - Data and storage services should be running in a highly available configuration/SKU. Azure data platform services offer resiliency features to support application reliability.
 
   
#### 5. Replication and Redundancy
   
   - To maintain data availability and durability, Azure Storage creates and stores copies of data across multiple locations. This process is called storage replication. The goal is to provide redundancy to protect data against hardware failures, and power or network outages.

   - Replicating data across zones or paired regions supports application availability objectives to limit the impact of failure scenarios. 

   - The ability to restore data from a backup is essential when recovering from data corruption situations as well as failure scenarios. To ensure sufficient redundancy and availability for zonal and regional failure scenarios, backups should be stored across zones and/or regions.


### **Task 2: Test with simulations and forced failovers** 

In this task, you will learn how to enable replication for virtual machines, run a test failover to validate your replication and disaster recovery strategy, without any data loss or downtime.

### **a. Replicate a Virtual machine**

1. In the Azure portal, click on **Show portal menu (1)** and select **Resource groups(2)**.

   ![](./media/costopt-01.png)

2. Open **wafprod** resource group and select a virtual machine.

   ![](./media/costopt-02.png)

3. From the left pane, select **Disaster recovery (1)** present under _Operations_. Then select a **Target region (2)** from the drop-down and click on **Review + Start Replication (3)**.

   ![](./media/reliability-01.png)
   
   > **Note:** Make sure that the VM is in running state before starting the replication. If not, please start the VM.

4. Review the details and click on **Start replication**.

   ![](./media/reliability-02.png)

5. Once the deployment starts, it will create a new resource group and a recovery service vault in it. The recovery service vault will take up to 5 minutes to reflect in the resource group.

6. In the search bar, search for Recovery service vaults and select **Recovery services vaults** from the suggestions.

   ![](./media/reliability-09.png)

7. Observe the vaults here, select the **Site-recovery-vault-eastus** vault, that falls under **site-recovery-vault-rg** resource group.

   ![](./media/reliability-06.png)   

   > **Note:** Use **Refresh** button in case the recovery service vault is not reflecting.

8. From the left pane, select **Site Recovery jobs** present under _Monitoring_. You will see all the jobs here with their status.

   ![](./media/reliability-03.png)

   > **Note:** It will take upto 15 minutes for all jobs to succeed.

9. Now from the left pane, select **Replicated items** present under _Protected items_. You will have the VM here that you just replicated, select that virtual machine **wafproxxxxxx**.

   ![](./media/reliability-04.png)

10. On the Overview page of the replicated virtual machine, you will have details such as _Health and status, Failover readiness, Errors_ and so on.

    ![](./media/reliability-07.png)

### **b. Create a Recovery plan**

1. In the Recovery Services vault, select **Recovery Plans (Site Recovery)** present under _Manage_ and click on **+ Recovery plan**.

   ![](./media/reliability-10.png)

2. Provide the following details for the recovery plan:

   * **Name:** failover-recovery-plan **(1)**.
   * **Source:** Choose a source location from the drop-down. The source location must have machines that are enabled for failover and recovery. Here we are using **Central US (2)** as it's the same location where we have our virtual machine.
   * **Target:** Choose a target location from the drop-down **(3)**.
   * **Allow items with deployment model:** Select **Resource Manager (4)** from the drop down.
   * Click on **Select items (5)**.

    ![](./media/reliability-38.png)

3. In Select items, select the machine that you want to add to the plan and then click on **OK**.

   ![](./media/reliability-22.png)

   > **Note:** You can only select machines that are in the source and target locations that you specified.
   
4. At last, click on **Create**. Once the plan is created successfully, move to the next task.

   ![](./media/reliability-40.png)


### **c. Run a test failover to Azure**

1. In the recovery services vault, select **Recovery Plans (Site Recovery)** and click on **failover-recovery-plan**. This is the Recovery plan that you just created.

   ![](./media/reliability-14.png)

2. Now select **Test Failover**.

   ![](./media/reliability-15.png)

3. Provide the following details for the failover:

   * **Choose a Recovery Point:** Select **Latest processed (low RTO) (1)** from the drop-down. This option fails over all VMs in the plan to the latest recovery point processed by Site Recovery. This option provides a low RTO (Recovery Time Objective), because no time is spent processing unprocessed data.
   * **Azure virtual network:** Select an Azure virtual network **(2)** from the drop-down, in which a test virtual machine will be created.
   * Click on **OK (3)**.

    ![](./media/reliability-16.png)

4. Go to **Site Recovery Jobs** present under _Monitoring_. Here you can track the failover progress. Click on the failover to view the process.

   ![](./media/reliability-17.png)

5. When a test failover is triggered, the following occurs:

   * **Prerequisites:** A prerequisites check runs to make sure that all conditions required for failover are met.
   * **Failover:** The failover processes and prepared the data, so that an Azure VM can be created from it.
   * **Latest:** If you have chosen the latest recovery point, a recovery point is created from the data that's been sent to the service.
   * **Start:** This step creates an Azure virtual machine using the data processed in the previous step.

    ![](./media/reliability-18.png)

6. When everything is working as expected, click Cleanup test failover. This deletes the VMs that were created during test failover.

7. Return to the Recovery plan you created and click on **Cleanup test failover**.

   ![](./media/reliability-19.png)

8. Add a note for cleanup and check the _'Testing is complete. Delete test failover virtual machine(s)'_ checkbox and click on **OK**.

   ![](./media/reliability-20.png)

9. You can track the cleanup progress in **Site Recovery Jobs** present under _Monitoring_.

   ![](./media/reliability-21.png)
   


### **Task 3: Monitor health** 

**Service Health** provides a personalized view of the health of the Azure services and regions you're using. This is the best place to look for service impacting communications about outages, planned maintenance activities, and other health advisories because the authenticated Service Health experience knows which services and resources you currently use. 

Service Health tracks four types of health events that may impact your resources:

 - **Service issues** - Problems in the Azure services that affect you right now.
 
 - **Planned maintenance** - Upcoming maintenance that can affect the availability of your services in the future.
 
 - **Health advisories** - Changes in Azure services that require your attention. Examples include deprecation of Azure features or upgrade requirements (e.g upgrade to a supported PHP framework).
 
 - **Security advisories** - Security related notifications or violations that may affect the availability of your Azure services.
 
 - **Health history** - Service Health provides you with a customizable dashboard that tracks the health of your Azure services in the regions where you use them. In this dashboard, you can track active events like ongoing service issues, upcoming planned maintenance, or relevant health advisories. When events become inactive, they get placed in your health history for up to 90 days.

In this task, you will set up **Service Health alerts** to notify you via your preferred communication channels when service issues, planned maintenance, or other changes may affect the Azure services and regions you use.


1. Type **service** in the search box located on the top of the Azure Portal page and click on **Service Health** to open it.

   ![](./media/ex4-task4-01.png)
   
2. From the left navigation pane, click on **Service issues**. You will be prompted with the active issues if there are any.

   ![](./media/ex4-task4-08.png)

3. Click on **health history** from the left navigation pane and select the time range as **Last 3 months**. You will be able to see the inactive events such as service issues, upcoming planned maintenance, and health advisories.

   ![](./media/ex4-task4-09.png)
   
   > **Note:** Feel free to click on the events and check on the details.
   
4. Click on **Health alerts** from the left navigation pane under **Alerts**.

   ![](./media/ex4-task4-02.png)
   
5. On the **Health alerts** page, click on **Add service health alert**.

   ![](./media/ex4-task4-07.png)
   
6. On the create an alert rule page, leave Condition page as default, click on **Actions** and click on **+ Select action group**.

   ![](/media/Ex4-task3-step6.png)
   
7. You will be prompted to **select action group** page, in this page make sure your subscription is selected and select the action group with the name **waf-actiongroup** which you created in exercise 1, task 4. Click on **Select**.

   ![](./media/ex4-task4-04.png)

8. Click on **Details (1)**, under **Alert rule details**, Provide the following details:

   * **Resource Group**: waf-prod **(2)**
   * **Alert rule name**: waf-alert **(3)**
   * Extend **Advanced options** check **Enable alert rule upon creation (4)**
   * Click on **Review + Create (5)**
 
   ![](/media/Ex4-task3-step8.png)
   
9. On **Review + create** page click on **Create**.

  ![](/media/Ex4-task3-step9.png)
   
10. Once the alert rule is created, go to the **Health alerts** page and observe all the details of the alert.

   ![](./media/ex4-task4-06.png)

11. In the Azure portal, search for Advisor and select **Advisor** from the suggestions.

    ![](./media/costopt-25.png)

12. Click on **All recommendations** from the left navigation pane under **Recommendations**.

    ![](./media/ex4-task4-11.png)
    
13. On the **All recommendations** page, click on the recommendation **Enable Cross Region Restore for your recovery Service Vault**.

    ![](./media/ex4-task4-10.png)
    
14. Click on **Enable CRR** on the **Enable Cross Region Restore for your recovery Service Vault** page to enable cross region restore.

    ![](/media/Ex4-task3-step14.png)
    
15. On the **Backup configuration** page, select **Enable** on the Cross Region Restore option and click on **Save**.

    ![](./media/ex4-task4-13.png)

### **Task 4: Respond to failure and disaster** 

Azure services are always available, thanks to Microsoft's efforts. Unexpected service disruptions, on the other hand, are possible. If you need resiliency for your application, Microsoft advises employing geo-redundant storage, which replicates your data to a second location. Customers should also have a plan in place for dealing with a regional outage. An important part of a disaster recovery plan is preparing to fail over to the secondary endpoint in the event that the primary endpoint becomes unavailable.

In this task, you will learn how to initiate an account failover for your storage account.

1. In the Azure Portal, search for storage accounts and select **Storage accounts** from the suggestions.

   ![](./media/reliability-23.png)

2. Click on **+ Create**.

   ![](./media/reliability-30.png)

3. Provide following details to create the storage account:

   - **Subscription:** Make sure your sunbscription is selected **by default (1)**
   - **Resource group:** Select **wafdev (2)** from the drop down
   - **Storage account name:** Give a unique name for the storage account **(3)**
   - **Region:** Leave to default **(4)**
   - **Performance:** Select **Standard (5)**
   - **Redundancy:** Select **Geo-redundant storage(GRS) (6)** from the drop down
   - At last, click on **Review (7)**

   > **Note:** The storage account should be configured with Geo-redundant storage, as Geo-replication is available for Geo-redundant (GRS) and Read access geo-redundant (RA-GRS) storage accounts.
 
   
   ![](./media/reliability-31.png)

4. Once the validation passes, click on **Create**.

   ![](./media/reliability-32.png)

5. After successful deployment of the storage account, click on **Go to resource**.

   ![](./media/reliability-35.png)

6. Now select **Redundancy (1)** present under _Data management_ and click on **Prepare for failover (2)**

   ![](./media/redundancy.png)
   
7. Enter **YES** for _'Confirm Failover'_ and click on **Failover**. The Last Sync Time property here, indicates how far the secondary is behind from the primary. Last Sync Time provides an estimate of the extent of data loss that you will experience after the failover is completed.

   ![](./media/reliability-26.png)

8. Once the failover begins, you will see a notification saying _Failover in progress_. This will take up to an hour to succeed.

   ![](./media/reliability-27.png)

9. After the failover is performed, go to **Redundancy** present under _Data management_. You will see that location is updated the as primary endpoint.

   ![](./media/locationredundancy.png)

10. Once the failover is complete, clients can begin writing to the new primary endpoint.

11. After the failover, your storage account type is automatically converted to locally redundant storage (LRS) in the new primary region. 

12. Now, click on the **Next** from lower right corner to move on next page.



























