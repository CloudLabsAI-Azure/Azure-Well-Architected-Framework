# **Overview**

The Azure Well-Architected Framework is a set of guiding tenets that can be used to improve the quality of a workload. The framework consists of five pillars of architectural excellence:

  * Reliability
  * Security
  * Cost Optimization
  * Operational Excellence
  * Performance Efficiency

   ![](./media/waf-overview01.png)

Incorporating these pillars helps produce a high quality, stable, and efficient cloud architecture:

  * Reliability:	The ability of a system to recover from failures and continue to function.
  * Security:	Protecting applications and data from threats.
  * Cost Optimization:	Managing costs to maximize the value delivered.
  * Operational Excellence:	Operations processes that keep a system running in production.
  * Performance Efficiency:	The ability of a system to adapt to changes in load.

## **Solution Architecture**

From the architectural standpoint, the deployment will consist of the following components:

 * Web servers deployed into an availability set
 * SQL Server backend(single VM)
 * GRS Storage account for object storage
 * Azure Bastion is deployed to manage the VM access.
 * West Europe

   ![](./media/waf-overview03.png)


1. Development Environment uses the same size VMs and settings as Production. 
2. AppDev team only access the environment 9:00-17:00 Monday through Friday. 
3. All services are deployed in pay-as-you-go mode.
4. Platform doesn’t use any WAF but NSG are configured. Claims Apps has not been configured SSL, yet.
5. There is no up/down autoscaling set up.
6. Any new version of the application is deployed manually.  For monitoring they use an agent-based 3rd party tool for the infrastructure metrics, there is no APM. No security or governance solution implemented. They use Azure Backup.  
7. There is a mismatch of the expected consumption for the deployed resources and what they are being charged. They do not know where those charges may be coming from. 
