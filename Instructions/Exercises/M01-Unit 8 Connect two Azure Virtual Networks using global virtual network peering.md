---
Exercise:
    title: 'M01-Unit 8 Connect two Azure Virtual Networks using global virtual network peering'
    module: 'Module - Introduction to Azure Virtual Networks'
---
# M01-Unit 8 Connect two Azure Virtual Networks using global virtual network peering

## Exercise scenario 
In this unit, you will configure connectivity between the CoreServicesVnet and the ManufacturingVnet by adding peerings to allow traffic flow. 

In this unit, you will:

+ Task 1: Create a Virtual Machine to test the configuration
+ Task 2: Connect to the Test VMs using RDP
+ Task 3: Test the connection between the VMs
+ Task 4: Create VNet peerings between CoreServicesVnet and ManufacturingVnet
+ Task 5: Test the connection between the VMs
+ Task 6: Clean up resources

## Task 1: Create a Virtual Machine to test the configuration

In this section, you will create a test VM on the Manufacturing VNet to test if you can access resources inside another Azure virtual network from your ManufacturingVnet.

### Create ManufacturingVM

1. On the Azure home page, using the global search type **Virtual Machines** and select virtual machines under services.

2. In Virtual Machines, select **+ Create; + Virtual machine**.

3. Use the information in the following table to create your VM.

| **Tab**         | **Option**                                                   | **Value**                             |
| --------------- | ------------------------------------------------------------ | ------------------------------------- |
| Basics          | Resource group                                               | ContosoResourceGroup                  |
|                 | Virtual machine name                                         | ManufacturingVM                       |
|                 | Region                                                       | West Europe                           |
|                 | Availability options                                         | No infrastructure redundancy required |
|                 | Image                                                        | Windows Server 2022 Datacenter- Gen1  |
|                 | Azure Spot instance                                          | Not selected                          |
|                 | Size                                                         | Standard_D2s_v3 - 2vcpus, 8GiB memory |
|                 | Username                                                     | TestUser                              |
|                 | Password                                                     | TestPa$$w0rd!                         |
|                 | Public inbound ports                                         | Allow selected ports                  |
|                 | Select inbound ports                                         | RDP (3389)                            |
| Disks           | No changes required                                          |                                       |
| Networking      | Virtual network                                              | ManufacturingVnet                     |
|                 | Subnet                                                       | ManufacturingSystemSubnet (10.30.10.0/24)|
|                 | Public IP                                                    | (new) ManufacturingVM-ip              |
|                 | NIC network security group                                   | Basic                                 |
|                 | Public inbound ports                                         | Allow selected ports                  |
|                 | Select inbound ports                                         | RDP (3389)                            |
|                 | Load balancing                                               | Not selected                          |
| Management      | No changes required                                          |                                       |
| Advanced        | No changes required                                          |                                       |
| Tags            | No changes required                                          |                                       |
| Review + create | Review your settings and select Create                       |                                       |


4. When the deployment is complete, select **Go to resource**.

## Task 2: Connect to the Test VMs using RDP

1. On the Azure Portal home page, select **Virtual Machines**.

2. Select **ManufacturingVM**.

3. In ManufacturingVM, select **Connect &gt; RDP**.

4. In ManufacturingVM | Connect, select **Download RDP file**.

5. Save the RDP file to your desktop.

6. Connect to ManufacturingVM using the RDP file, and the username **TestUser** and the password **TestPa$w0rd!**.

7. On the Azure Portal home page, select **Virtual Machines**.

8. Select **TestVM1**.

9. In TestVM1, select **Connect &gt; RDP**.

10. In TestVM1 | Connect, select **Download RDP file**.

11. Save the RDP file to your desktop.

12. Connect to TestVM1 using the RDP file, and the username **TestUser** and the password **TestPa$w0rd!**.

13. On both VMs, in **Choose privacy settings for your device**, select **Accept**.

14. On both VMs, in **Networks**, select **Yes**.

15. On TestVM1, open a PowerShell prompt, and run the following command: ipconfig

16. Note the IPv4 address. 

 

## Task 3: Test the connection between the VMs

1. On the ManufacturingVM, open a PowerShell prompt.

2. Use the following command to verify that there is no connection to TestVM1 on CoreServicesVnet. Be sure to use the IPv4 address for TestVM1.

| PowerShell                               |
| ---------------------------------------- |
| Test-NetConnection 10.20.20.4 -port 3389 |


3. The test connection should fail, and you will see a result similar to the following:
   ![PowerShell window with Test-NetConnection 10.20.20.4 -port 3389 showing failed ](../media/test-netconnection-fail.png)

 

## Task 4: Create VNet peerings between CoreServicesVnet and ManufacturingVnet

1. On the Azure home page, select **Virtual Networks**, and then select **CoreServicesVnet**.

2. In CoreServicesVnet, under **Settings**, select **Peerings**.
   ![screen shot of core services VNet Peering settings ](../media/create-peering-on-coreservicesvnet.png)

3. On CoreServicesVnet | Peerings, select **+ Add**.

4. Use the information in the following table to create the peering.

| **Section**                          | **Option**                                    | **Value**                             |
| ------------------------------------ | --------------------------------------------- | ------------------------------------- |
| This virtual network                 |                                               |                                       |
|                                      | Peering link name                             | CoreServicesVnet-to-ManufacturingVnet |
|                                      | Traffic to remote virtual network             | Allow (default)                       |
|                                      | Traffic forwarded from remote virtual network | Allow (default)                       |
|                                      | Virtual network gateway or Route Server       | None (default)                        |
| Remote virtual network               |                                               |                                       |
|                                      | Peering link name                             | ManufacturingVnet-to-CoreServicesVnet |
|                                      | Virtual network deployment model              | Resource manager                      |
|                                      | I know my resource ID                         | Not selected                          |
|                                      | Subscription                                  | MOC Subscription-lodxxxxxxxx          |
|                                      | Virtual network                               | ManufacturingVnet                     |
|                                      | Traffic to remote virtual network             | Allow (default)                       |
|                                      | Traffic forwarded from remote virtual network | Allow (default)                       |
|                                      | Virtual network gateway or Route Server       | None (default)                        |
| Review your settings and select Add. |                                               |                                       |
|                                      |                                               |                                       |


5. In CoreServicesVnet | Peerings, verify that the **CoreServicesVnet-to-ManufacturingVnet** peering is listed.

6. Under Virtual networks, select **ManufacturingVnet**, and verify the **ManufacturingVnet-to-CoreServicesVnet** peering is listed.

 

## Task 5: Test the connection between the VMs

1. On the ManufacturingVM, open a PowerShell prompt.

2. Use the following command to verify that there is now a connection to TestVM1 on CoreServicesVnet. 

| PowerShell                               |
| ---------------------------------------- |
| Test-NetConnection 10.20.20.4 -port 3389 |


3. The test connection should succeed, and you will see a result similar to the following:
   ![Powershell window with Test-NetConnection 10.20.20.4 -port 3389 showing TCP test succeeded: true](../media/test-connection-succeeded.png)

 

Congratulations! You have successful configured connectivity between VNets by adding peerings. 

## Task 6: Clean up resources

   >**Note**: Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not see unexpected charges.

1. In the Azure portal, open the **PowerShell** session within the **Cloud Shell** pane. (Create Cloud Shell storage if needed, using default settings.)

1. Delete all resource groups you created throughout the labs of this module by running the following command:

   ```powershell
   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob
   ```

    >**Note**: The command executes asynchronously (as determined by the -AsJob parameter), so while you will be able to run another PowerShell command immediately afterwards within the same PowerShell session, it will take a few minutes before the resource groups are actually removed.
