
## Installation

```
Set-ExecutionPolicy Unrestricted

Install-Module Microsoft.Graph -AllowClobber

Install-Module Microsoft.Graph.Beta -Repository PSGallery -Force -Scope CurrentUser
```

## Sign-in with SP & Client Secret

Click Application permission. Search on the permissions you need for the graph API.

1) Client ID (a.k.a. Application ID) 
2) Tenant ID 
3) Client secret.

```
# Replace the <CLIENT_SECRET_VALUE>, <TENANT_ID>, <CLIENT_ID> with your values
$ClientSecret = "<CLIENT_SECRET_VALUE>"
$TenantId = "<TENANT_ID>"
$ClientId = "<CLIENT_ID>"

# Convert the Client Secret to a Secure String
$SecureClientSecret = ConvertTo-SecureString -String $ClientSecret -AsPlainText -Force

# Create a PSCredential Object Using the Client ID and Secure Client Secret
$ClientSecretCredential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $ClientId, $SecureClientSecret

# Connect to Microsoft Graph Using the Tenant ID and Client Secret Credential
Connect-MgGraph -TenantId $TenantId -ClientSecretCredential $ClientSecretCredential -Environment China

# Confirm that you are using the correct app
Get-MgContext

# Review output:
# 	Client ID: (Should match what you entered in the $ClientId variable
#	Scopes: Should match the API permissions set in the App registration
#	AuthType: 'AppOnly' Since we are using Application permissions
#	TokenCredentialType: 'Client Secret' Since we are using a client secret

# Run the MS Graph API call

# Close the connection
Disconnect-MgGraph
```

## Sign-in with SP & Certificate


```
# Import the necessary module for certificate management
Import-Module PKI

# Define the parameters for the certificate
$certSubject = "CN=xuhuanGraphAppCert"  # Change this to the desired certificate name
$certPath = "<path>"  # Path where you want to save the certificate file
$certPassword = ConvertTo-SecureString "Password123!" -AsPlainText -Force  # Password to secure the PFX file

# Generate the self-signed certificate
$cert = New-SelfSignedCertificate -DnsName $certSubject -CertStoreLocation "Cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(10) -KeyUsage DigitalSignature,KeyEncipherment,DataEncipherment

# Export the certificate to a .pfx file
$cert | Export-PfxCertificate -FilePath $certPath -Password $certPassword

# Display the certificate thumbprint for verification
Write-Host "Certificate Thumbprint: $($cert.Thumbprint)"

$certPathCer = "D:\OneDrive - Microsoft\01 - Workspace\AAD-cert\mooncake\xuhuanGraphAppCert.crt"
Export-Certificate -Cert $cert -FilePath $certPathCer -Type CERT
```


`Connect-MgGraph -Environment China -AppId $CN_App_Id -TenantId $CN_Tenant_Id -CertificateThumbprint "CCA6213E8E41A7750F618A03AE68C2FA74138C4B"` 


## Sign-in with Device Code

![[image.png|953x813]]

`Connect-MgGraph -Scopes "Policy.Read.All" -ClientId "b648ff77-f7a7-42c7-b54c-4eb05a3580ba" -Environment China -TenantId “954ddad8-66d7-47a8-8f9f-1316152d9587” -UseDeviceAuthentication`

## Check Graph Context


```
Get-mgcontext
Disconnect-MgGraph
```
