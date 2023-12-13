# azure-database-migration224

This README provides a brief overview of the Cloud Engineering Bootcamp project and the completed steps.

Final Architecture:
![Azure Project Diagram](https://github.com/dedalus94/azure-database-migration224/assets/49538048/c262ec1c-bbcb-4a99-8530-b912ce0d9a74)

## Table of content 
1. [Production DB setup, migration to an Azure SQL DB & initial backup](#production-db-setup-migration-to-an-azure-sql-db--initial-backup)
2. [Dev Environment Setup and Data Loss Simulation](#dev-environment-setup-and-data-loss-simulation)
   - [Set Up a Dev Environment](#set-up-a-dev-environment)
   - [Automated Backup of the Dev DB](#automated-backup-of-the-dev-db)
   - [Data Loss Simulation in the Azure Prod Environment & Disaster Recovery](#data-loss-simulation-in-the-azure-prod-environment--disaster-recovery)
3. [Geo-replication and Failover](#geo-replication-and-failover)
4. [Microsoft Entra ID for Azure SQL DB](#microsoft-entra-id-for-azure-sql-db)

## Production DB setup, migration to an Azure SQL DB & initial backup backup 

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


### Data loss simulation in the *Azure* prod environment & disaster recovery

  * Run the following statement to mimic data loss:
    
`` DELETE FROM [AdventureWorks2022].[HumanResources].[EmployeePayHistory]
WHERE Rate < 10.0;``  - 54 rows affected


`` UPDATE [AdventureWorks2022].[Sales].[CurrencyRate]
SET AverageRate = 0.0;`` - 13532 rows affected 

  * Using the 'restore' option in the the Azure portal, is possible to restore an Azure SQL Database to a point in time, thanks to Azure's frequent automated backups. The restored db is visible in Data Studio (once the deployment is complete) and all the deleted data is back too:

![image](https://github.com/dedalus94/azure-database-migration224/assets/49538048/beb82d98-3be1-4722-89bf-f9760f2817de)


## Geo-replication and failover

* Geo-replication allows to create a copy of a server in a different region so that if anything happens at the physical location of the primary server the data will still be available in the secondary location.
  
* After a server has been geo-replicated it is necessary to set up a *failover group*. This will be accessible from the primary server page and a failover can also be simulated here (with the primary and the secondary server swapping roles).
  
* Before the failover:
  
![image](https://github.com/dedalus94/azure-database-migration224/assets/49538048/36a5e5f2-4214-4019-9144-e4db817c5439)

* After the failover:
  
![image](https://github.com/dedalus94/azure-database-migration224/assets/49538048/aab4128f-fd3b-44cb-8fe8-9442b9b0bf69)

* hitting failover again will switch back to the original configuration. Failovers can also be planned so that maintenance tasks can be carried out without affecting data availability or to test the secondary region server's ability to cope with the workload. 

## Microsoft Entra ID for Azure SQL DB

* Set up my own Azure account as the Microsoft Entra ID Admin for the DB and tested the connection in Data Studio.
* Created a new user in the Microsoft Entra ID page
* Used the following query template to assign a DB reader role to the new user: ``CREATE USER [DB_Reader@yourdomain.com] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [DB_Reader@yourdomain.com];``
* Added a new account into Data Studio that prompted the first login of the Entra ID account (so the user can select a new password and set the multi-factor authentication)
* Established a new connection with the reader account to the DB (visible on the left in the following screenshot) and then tested that the right permissions were in place:
  
![image](https://github.com/dedalus94/azure-database-migration224/assets/49538048/c0e28b0f-1c00-4381-bb14-1fa65f3bd344)


    


