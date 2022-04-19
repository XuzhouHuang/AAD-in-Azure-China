# Configure access token lifetime

There are two parties involved in an access token request: the client, who requests the token, and the resource (the API) that accepts the token when the API is called, referring to https://docs.microsoft.com/en-us/azure/active-directory/develop/access-tokens#what-app-is-a-token-for.

Create token lifetime policy and assign it to the resource app/sp, referring to https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-configurable-token-lifetimes#configurable-token-lifetime-properties.

## License

Using this feature requires an **Azure AD Premium P1** license.

## Configurable token lifetime properties

You _can_ specify the lifetime of a **access, ID, or SAML token** issued by the Microsoft identity platform. 

You *can not* set token lifetime policies for **refresh tokens and session tokens**. 

* Property: Access Token Lifetime
* Policy property string: AccessTokenLifetime
* Affects: Access tokens, ID tokens, SAML2 tokens
* Default:
  * Access tokens: varies, depending on the client application requesting the token. For example, continuous access evaluation (CAE) capable clients that negotiate CAE-aware sessions will see a long lived token lifetime (up to 28 hours).
  * ID tokens, SAML2 tokens: 1 hour
* **Minimum: 10 minutes**
* **Maximum: 1 day**

# Example

Doc: https://docs.microsoft.com/en-us/azure/active-directory/develop/configure-token-lifetimes

### 1. download the latest [ Azure AD PowerShell Module Public Preview release](https://www.powershellgallery.com/packages/AzureADPreview).

   You _cannot_ install both the preview and the GA version on the same computer at the same time. 

   ``` Powershell
   # Uninstall the AzureAD Module
   UnInstall-Module AzureAD

   # Install the AzureAD Preview Module adding the -AllowClobber
   Install-Module AzureADPreview -AllowClobber
   ```

### 2. Logon to Azure China

   ``` Powershell
   Connect-AzureAD -AzureEnvironmentName AzureChinaCloud
   ```

### 3. Create a token lifetime Policy

   ``` Powershell
   $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"03:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
   Get-AzureADPolicy -Id $policy.Id | fl *
   ```

   Output:
   ```
   Id                    : b5bf1c7c-7fde-474d-839f-11018dd07fbe
   OdataType             : 
   AlternativeIdentifier : 
   Definition            : {{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"03:00:00"}}}
   DisplayName           : WebPolicyScenario
   IsOrganizationDefault : False
   KeyCredentials        : {}
   Type                  : TokenLifetimePolicy
   ```

### 4. Change a token lifetime Policy

   ``` Powershell
   Set-AzureADPolicy -Id $policy.Id -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"23:59:59"}}')
   Get-AzureADPolicy -Id $policy.Id | fl *
   ```
   
   Output:
   ```
   Id                    : b5bf1c7c-7fde-474d-839f-11018dd07fbe
   OdataType             : 
   AlternativeIdentifier : 
   Definition            : {{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"23:59:59"}}}
   DisplayName           : WebPolicyScenario
   IsOrganizationDefault : False
   KeyCredentials        : {}
   Type                  : TokenLifetimePolicy
   ```

### 5. Assign the policy to ARM (https://management.chinacloudapi.cn/)

   Microsoft first party app:

   * Service Principal Name: Windows Azure Service Management API
   * App Id: 797f4846-ba00-4fd7-ba43-dac1f8f63013

   ``` Powershell
   # Get ID of the service principal   
   $ARMsp = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Windows Azure Service Management API'"
   # Assign policy to a service principal
   Add-AzureADServicePrincipalPolicy -Id $ARMsp.ObjectId -RefObjectId $policy.Id
   ```


