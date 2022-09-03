# Leverage Azure Active Directory Kerberos with Azure Files for hybrid identities

## Requirements

+ the clients connecting to Azure Files need to be Azure AD-joined clients (or hybrid Azure AD-joined)
+ the user identities must be hybrid identities, managed in Active Directory
+ configuring access control lists (ACLs) and permissions might require line-of-sight to the domain controller.
+ multi-factor authentication (MFA) on the Azure AD app representing the storage account must be disabled
+ Client OS must be one of the r
   * Windows 11 Enterprise single or multi-session.
   * Windows 10 Enterprise single or multi-session, versions 2004 or later with the latest cumulative updates installed, especially the KB5007253 - 2021-11 Cumulative Update Preview for Windows 10.
   * Windows Server, version 2022 with the latest cumulative updates installed, especially the KB5007254 - 2021-11 Cumulative Update Preview for Microsoft server operating system version 21H2.

## Benefits

+ End users can access Azure file shares over the internet without requiring a line-of-sight to domain controllers from hybrid Azure AD-joined and Azure AD-joined VMs

### Note

> Your Azure Storage account can't authenticate with both Azure AD and a second method like AD DS or Azure AD DS. You can only use one authentication method. If you've already chosen another authentication method for your storage account, you must disable it before enabling Azure AD Kerberos.
