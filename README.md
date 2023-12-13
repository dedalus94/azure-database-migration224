# azure-database-migration224

This README provides a brief overview of the Cloud Engineering Bootcamp project and the completed steps:

## Production db setup & backup 

* Azure VM and Firewall Setup:
  - Created an Azure Virtual Machine (Windows 11).
  - Added a firewall rule for remote connections so that I could connect from my personal device.
  
* Installed SQL Server and SSMS on the VM.

* Used SSMS to restore a database from a .bak file, simulating an on-premises setup.

* Azure Server and Database Creation:
  - Created an Azure SQL Server.
  - Provisioned an SQL Database.

* Database Migration to Azure:
  - Downloaded Data Studio and the following extensions: **Azure SQL Migration** and **SQL Server schema compare** 
  - Utilized Data Studio to migrate the on-premises database to Azure (completed in roughly 7 minutes).
  - A full backup was generated using SSMS (tasks -> backup) and uploaded into a container that had been previously created within a storage account. 
 
## Dev environment setup and data loss simulation

### Set up a dev environment

* Created a dev VM and restored the same DB to recreate a parallel environment for testing
  
### Automated backup of the dev db:

  * Started SQL Server Agent
  * Run the T-SQL:
    `` CREATE CREDENTIAL [YourCredentialName]
      WITH IDENTITY = '[Your Azure Storage Account Name]',
      SECRET = 'Access Key';``
  * Created an automated maintenance plan to automate backups on a weekly schedule.
The credential and the maintenance plan are visible in the object explorer:
      
![image](https://github.com/dedalus94/azure-database-migration224/assets/49538048/fb81dbab-9a1f-48b1-9389-aacd97dff733)

  * Tested the maintenance plan by executing it: 
    
![image](https://github.com/dedalus94/azure-database-migration224/assets/49538048/ee0de569-fb13-4616-8dc7-d6633fd0ef71)

### Data loss simulation in the **Azure** prod environment 

  * Run the following statement to mimic data loss:
    
`` DELETE FROM [AdventureWorks2022].[HumanResources].[EmployeePayHistory]
WHERE Rate < 10.0;``  - 54 rows affected


`` UPDATE [AdventureWorks2022].[Sales].[CurrencyRate]
SET AverageRate = 0.0;`` - 13532 rows affected 
  
    


