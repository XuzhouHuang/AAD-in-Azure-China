# Configure Azure China MFA as Auth provider on AD FS

## Step 1 - Generate a certificate for Azure MFA on each AD FS server

Run `$certbase64 = New-AdfsAzureMfaTenantCertificate -TenantID <tenantID>` on AD FS server. The tenantID can be the federated tenant name.

Successful response would be like below:

![image](https://user-images.githubusercontent.com/96280581/163996332-68f70c8f-3f8b-465a-a2aa-4d019da6bfeb.png)

You could find the new created certificate as below:

![image](https://user-images.githubusercontent.com/96280581/163996372-d1cad583-1aa6-412a-9885-5c2d14c25dbd.png)

## Step 2 - Add the new credentials to the Azure Multi-Factor Auth Client Service Principal

App "Azure Multi-Factor Auth Client" is Microsoft first-party application. So the application id is identical for each AAD tenant.

```
Connect-MSolService -AzureEnvironment "AzureChinaCloud"

New-MsolServicePrincipalCredential -AppPrincipalId 981f26a1-7f43-403b-a875-f8b09b8cd720 -Type asymmetric -Usage verify -Value $certBase64
```

![image](https://user-images.githubusercontent.com/96280581/163997181-86a66bea-3837-44e1-b0fa-717f1011609a.png)

## Step 3 - Set the Azure tenant information on each AD FS server

a. Set environment to Mooncake by setting registry key values

Registry Key | Value
------------- | -------------
SasUrl  | https://adnotifications.windowsazure.cn/StrongAuthenticationService.svc/Connector
StsUrl  | https://login.chinacloudapi.cn
ResourceUri  |  https://adnotifications.windowsazure.cn/StrongAuthenticationService.svc/Connector

![image](https://user-images.githubusercontent.com/96280581/163997543-e639ea9c-4400-4bd2-85d6-79f5010d1edf.png)

b. Run `Set-AdfsAzureMfaTenant -TenantId <tenant ID> -ClientId 981f26a1-7f43-403b-a875-f8b09b8cd720` on AD FS.

c. Restart AD FS service.

## Step 4 - Choose Azure MFA as additional authentication method

Please note, this step just make Azure MFA optional when application is required for MFA. It doesn’t mean the MFA would be triggered after performing this configuration.
 
![image](https://user-images.githubusercontent.com/96280581/163997856-3d4e834f-c9fe-453b-b21b-8baa767cd660.png)

## Step 5 - Verify ADFS + Azure China MFA works as expected with ClaimsXray

a. Choose 'Permit everyone and require MFA' for test relying party ClaimsXray

![image](https://user-images.githubusercontent.com/96280581/163997965-8eef4ce3-cca0-481f-a9de-43d9cc85195a.png)

b.	Visit "https://<federation name>/adfs/ls/IdpInitiatedSignOn?LoginToRP=urn:microsoft:adfs:claimsxray", you would find the MFA is challenged on the ADFS page.
