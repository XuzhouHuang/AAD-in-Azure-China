# AAD Connect V1 Retirement and V2 Upgrade

On August 31, 2022, all 1.x versions of Azure AD Connect will be retired because they include SQL Server 2012 components that will no longer be supported. Our customers need upgrade to the most recent version of Azure AD Connect (2.x version) by that date.

You can upgrade your Azure AD Connect server from all supported versions with the latest versions:

`Reference link` 

https://docs.microsoft.com/en-us/azure/active-directory/hybrid/whatis-azure-ad-connect-v2

https://docs.microsoft.com/en-us/azure/active-directory/hybrid/reference-connect-version-history#20280

Please also be noted that AAD Connect v2 are *NOT* supported to install on Windows server 2008R2/2012/2012R2. This means you must upgrade Windows server before upgrading AAD Connect in place or build new Windows server directly.
 
![image](https://user-images.githubusercontent.com/96280581/159448457-95c1803b-20dc-42e7-8be6-a4c4c49e3a54.png)

If you choose to install a new AAD Connect, you may refer to the following steps:

## Step 1

Export existing AAD Connect settings, referring to https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-import-export-config.

   ![image](https://user-images.githubusercontent.com/96280581/159448873-4cc3f080-eb64-49ae-9208-972d55bc7f32.png)

## Step 2

Download the latest AAD Connect from https://www.microsoft.com/en-us/download/confirmation.aspx?id=47594, and import the synchronization settings during installation. Enable staging mode for this new installed AADC.

   ![image](https://user-images.githubusercontent.com/96280581/159448945-6b99fc20-d22d-478e-881e-c81def79c100.png)

## Step 3

Leverage [AAD Connect Documenter](https://github.com/Microsoft/AADConnectConfigDocumenter) to compare the two AAD Connect configuration.

1. Export sever configuration both servers:

   Run the following cmd on the old AAD Connect server:

   ```
   Import-Module ADSync 
   Get-ADSyncServerConfiguration -Path "C:\oldaadc\"
   ```

   Run the following cmd on the new AAD Connect server:
   
   ```
   Import-Module ADSync 
   Get-ADSyncServerConfiguration -Path "C:\newaadc\"
   ```

2. Download Documenter to any client and extract the folder. Create a new subfolder under “Data” and copy the “oldaadc” and “newaadc” folder to it.

   ![image](https://user-images.githubusercontent.com/96280581/159450370-6003a583-e987-4b40-890b-59fdcab63c6b.png)

3. Make a copy of AzureADConnectSyncDocumenter-Contoso.cmd with a name appropriate for your environment (say AzureADConnectSyncDocumenter-AADUpgrade.cmd) and edit your copy for the values of "Pilot" and "Production" directories.
  
   ```
   AzureADConnectSyncDocumenterCmd.exe "AADCUpgrade\oldaadc" "AADCUpgrade\newaadc"
   ```

4. Run .cmd file and you’ll get the comparison report under folder “Report”. Check the html file and correct the difference on the new AAD Connect if there’s any.

   ![image](https://user-images.githubusercontent.com/96280581/159451187-807a2ea8-def1-41fc-bac4-258454fd75a3.png)

   ![image](https://user-images.githubusercontent.com/96280581/159451209-d2426441-8f3a-42ed-8b6b-7f14b957424d.png)


## Step 4

After the new AAD Connect is confirmed by customer that the installation and configuration is completed, switch the staging (new) server to active server and switch the active (old) server to staging mode. 
