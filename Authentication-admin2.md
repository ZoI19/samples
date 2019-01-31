# Authentication Process: Set Up Application on AzureAD

<!-- TOC -->
- [Overview](#overview)
- [1. Create Devspace service principal on the target tenant](#1-create-devspace-service-principal-on-the-target-tenant)
- [2. Set up client application to access DevSpace](#2-set-up-client-application-to-access-devspce)
    - [A. Azure portal UI](#a-azure-portal-ui)
    - [B. Editing the manifest](#b-editing-the-manifest)
- [Further Reading](#further-reading)

<!-- /TOC -->

**Important:** DevSpace itself requires access to the Office365 API, so the target tenancy must have an Office365 subscription. Without an Office365 subscription, an attempt to grant consent to the app will result in an error:

**Example:**
> AADSTS65005: The app needs access to a service ("https://*.dps.mil/") that your organization "06de869e-74e3-4c36-9d35-1cb3e96ddd53" has not subscribed to or enabled

## Overview

This document describes the required steps for configuration of an application with AzureAD for access to the DevSpace platform.

## 1. Create DevSpace service principal on the target tenant

The administrator must first create a DevSpace service principal on their Azure tenant.

In the example URL below:

- Replace **`<Target-Tenant-ID>`** with your Azure Tenant ID
- Request **`<client_id>`** and **`<redirect_uri>`** from your DevSpace representative
- Remove spacing / line breaks from URL
- Copy and paste into browser
- Click 'Accept' when prompted

<code>
https://login.microsoftonline.com/

Target-Tenant-ID

oauth2/authorizeclient_id=ff28182b-b927-4697-9f0e-3d38e40971a8a

&redirect_uri=https://DevSpace.benjaminmawson.com/authorize

&response_type=code

&response_mode=form_post

&prompt=admin_consent
</code>

## 2. Set up client application to access DevSpace

The application must be added on [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) and should request access to DevSpace.

Access may be assigned either by [Azure portal UI](#A.-Azure-portal-UI) or by [editing the manifest](#B.-Editing-manifest):

### A. Azure portal UI

- Select the application which you want to grant access to DevSpace
- Click Settings
- In the Settings Menu's API Access item, select 'Required Permissions'
- Click '+' Add
- Click Select API
- In the Search box, type 'DevSpace'
- Highlight 'DevSpace' and click the 'Select' button
- In the 'Enable Access' tab, select 'Access DevSpace' and click the 'Select' button

**Note:** The DevSpace application does not appear in the search results of the `Select an API` form unless [the service principal has already been created](#1.-create-devspace-service-principal-on-the-target-tenant).

### B. Editing the manifest

Add the following JSON to `requiredResourceAccess` section of the application manifest:

```json
{
    "appId": "<your app ID>",
    "requiredResourceAccess": [
        {
            "resourceAppId": "ff28182b-b927-4697-9f0e-3d38e40971a8a",
            "resourceAccess": [
                {
                    "id": "4291fced-a2b5-42ac-8152-a3c3819d9cb3",
                    "type": "Scope"
                }
            ]
        }
    ]
}
```

**Note:** To ensure the above details remain current, contact your DevSpace representative to request:

- resourceAppId
- resourceAccessid

## Further Reading

- [Application and service principal objects in Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)
