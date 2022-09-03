# Leverage Azure Active Directory Kerberos with Azure Files for hybrid identities

## Requirements

+ The user identities must be hybrid identities, managed in Active Directory (which means AD DS and AAD Connect is required).
+ Configuring access control lists (ACLs) and permissions might require line-of-sight to the domain controller.
+ The clients connecting to Azure Files need to be Azure AD-joined clients (or hybrid Azure AD-joined).
+ Multi-factor authentication (MFA) on the Azure AD app representing the storage account must be disabled.
+ Client OS must be the following versions:
   * Windows 11 Enterprise single or multi-session.
   * Windows 10 Enterprise single or multi-session, versions 2004 or later with the latest cumulative updates installed, especially the KB5007253 - 2021-11 Cumulative Update Preview for Windows 10.
   * Windows Server, version 2022 with the latest cumulative updates installed, especially the KB5007254 - 2021-11 Cumulative Update Preview for Microsoft server operating system version 21H2.

## Benefits

+ End users can access Azure file shares over the internet without requiring a line-of-sight to domain controllers from hybrid Azure AD-joined and Azure AD-joined VMs

### Note

> Your Azure Storage account can't authenticate with both Azure AD and a second method like AD DS or Azure AD DS. You can only use one authentication method. If you've already chosen another authentication method for your storage account, you must disable it before enabling Azure AD Kerberos.

## Lab Guidence

### Step 1 - Set up AD DS Env 

  ![image](https://user-images.githubusercontent.com/96280581/188254252-9c748911-dd05-4f67-aba3-d05b3135d377.png)

### Step 2 - Register Custom Domain on AAD and Configure AAD Connect Sync

  ![image](https://user-images.githubusercontent.com/96280581/188256204-7ab395da-f944-413d-9491-fcf4ce6a1e02.png)
  
  ![image](https://user-images.githubusercontent.com/96280581/188256244-47a7a747-db34-44e2-8a58-819013fb2695.png)

### Step 3 - Create Storage account for Fslogix and Enable AAD Kerberos Authentication

   ```PowerShell
   $domainInformation = Get-ADDomain
   $domainGuid = $domainInformation.ObjectGUID.ToString()
   $domainName = $domainInformation.DnsRoot
   ```
   ![image](https://user-images.githubusercontent.com/96280581/188254521-577b9d7e-e189-48be-8d4a-c2ad959cdd7b.png)
   
   ![image](https://user-images.githubusercontent.com/96280581/188254639-26e8ee2f-5025-4d20-82df-e8def1d91c6b.png)

### Step
