# Additional Configuration on AD FS for Hybrid AADJ in Azure China

## Issue
You may have followed steps in doc https://docs.microsoft.com/en-us/azure/active-directory/devices/howto-hybrid-azure-ad-join for hybrid AADJ in Azure China, but still find device registration doesn't work as expected.

## Analysis
If you capture fiddler trace on client, you should find the WIA(Windows Integration Authentication) against AD FS WSTrust endpoint fails as below.

![image](https://user-images.githubusercontent.com/96280581/163992159-571f9b6b-2741-4c20-b9cb-9713428ef227.png)

Further checking event log **Microsoft-Windows-AAD/Operational** on client, the following indicates the same "invalid scope" in the authencation request:

```
WSTrust response error: InvalidScope

Error description: <s:Text xml:lang="en-US" xmlns:s="http://www.w3.org/2003/05/soap-envelope">ID3082: The request scope is not valid or is unsupported.</s:Text>

Http transport error. Status: Unknown HResult Error code: 0x800484c1 Correlation ID: F9BAACC0-9D7A-4C8C-9207-921C81E80129

Logon failure. Status: 0xC00484C1 Correlation ID: F9BAACC0-9D7A-4C8C-9207-921C81E80129
```

We can find clearer clue why the request is rejected through event log **AD FS/Admin** on AD FS. The identifier "https://login.partner.microsoftonline.cn" was not included in the O365 China relying party. 

```
A token request was received for a relying party identified by the key 'https://login.partner.microsoftonline.cn', but the request could not be fulfilled because the key does not identify any known relying party trust. 
Key: https://login.partner.microsoftonline.cn 

This request failed. 

User Action 
If this key represents a URI for which a token should be issued, verify that its prefix matches the relying party trust that is configured in the AD FS configuration database.
```

## Solution

Manually add identifier "https://login.partner.microsoftonline.cn" to O365 China relying party on AD FS and retry device registration.

![image](https://user-images.githubusercontent.com/96280581/163993922-3b606954-4f36-4546-8633-040043cac982.png)
