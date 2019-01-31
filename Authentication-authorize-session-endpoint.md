# Authentication Process: Authorize session endpoint

<!-- TOC -->

- [Overview](#overview)
- [Input](#input)
- [Output](#output)
- [Example of usage](#example-of-usage)
    - [1. Application authenticates user in OAuth 2.0 code grant flow](#1.-application-authenticates-user-in-OAuth-2.0-code-grant-flow)
    - [2. User authentication on Loop](#2.-User-authentication-on-Loop)
- [Further reading](#further-reading)


<!-- /TOC -->


Endpoint name:    `/authorizeSession`

**Example:**  https://devloop.loopsoftware.fr/authorizeSession

## Overview

This document describes how to authenticate an application for access to the Loop platform by retrieving a Session ID for use in consecutive requests either by user self-identification or a fully automated process. 

Once the third party application is validated in AzureAD, the Loop platform verifies these credentials. 

## Input

The Loop endpoint requires a valid token sent in an `Authorization` HTTP header.

**Note:** Loop only supports OpenIDConnect or OAuth2 `JWT access_tokens` from AzureAD for Loop [Further Reading](#further-reading).

## Output

On successful authorization, the Loop endpoint response is an `application/json` as shown where `sessionId` is a unique session identifier and `user` is the user's email address.

```JSON
{
    "sessionId": <uuid>,
    "user": <upn>
}
```

**Note:** On invalid request, a 401 (unauthorised) status code is returned. The response body may contain error description in `json` format.

## Example of usage

### Pre-requisites

Both applications were set up as in [Set up and configuration](Authentication-admin.md#Setup-and-configuration).  

Client `<tenant>` has application with

- `<client_ID>`
- `<redirect_uri>` [aka `ReplyURL` in  AzureAD portal]
- `<secret_key>`

### 1. Application authenticates user in OAuth 2.0 code grant flow

- User is redirected to login.microsoftonline.com with the form_post response mode. 

**Example:**

    https://login.microsoftonline.com/<tenant>/oauth2/authorize?client_id=<client_ID>&response_type=code%20id_token&redirect_uri=<redirect_uri>&response_mode=form_post&scope=openid&state=any_valid_state&nonce=unique_nonce 

- On the Microsoft login page, user logs in with username and password, e.g. alex@loopsoftware.fr and 123456
- On successful login, `<redirect_uri>` receives a request with `<id_token>` and `<code>`.
 
- The  `<id_token>` authenticates user on client application.

- `<code>` is used to retrieve AzureAD `<access_token>` for Loop, by sending POST request to `/oauth2/token` endpoint.

    The `<code>` may look like  

        `AQABAAIAAABH...1200 chars here...7VkGb56UEiAA` 

    and the request should then be as shown:

        curl -X POST \
            https://login.microsoftonline.com/<tenant>/oauth2/token \
            -H 'content-type: application/x-www-form-urlencoded' \
            -d 'client_id=<client_ID>&client_secret=<secret_key>&resource=https://loop.loopsoftware.fr&scope=openid&grant_type=authorization_code&code=<code>&redirect_uri=<redirect_uri>'

- A successful token exchange results in json similar to:

    ```JSON
    {
        "token_type": "Bearer",
        "scope": "user_impersonation",
        "expires_in": "3600",
        "ext_expires_in": "0",
        "expires_on": "1512123526",
        "not_before": "1512119626",
        "resource": "https://loop.loopsoftware.fr",
        "access_token": "eyJ...1600 chars here...ADw",
        "refresh_token": "ABA...1600 chars here...3-g",
        "id_token": "AiO...1500 chars here...gf2"
    }
    ```
     The `access_token` is used to query `/authorizeSession` endpoint.

     The `refresh_token` is used to retrieve new `access_token` as needed.

### 2. User authentication on Loop

- Client application uses `access_token` to get authorized session Id from `/authorizeSession` endpoint.

    **Example:**

      curl -H "Authorization: Bearer eyJ0eXAiOiJK...1600 chars here...sAyBxlWhSADw" https://devloop.loopsoftware.fr/authorizeSession

    will return

    ```XML
    {"sessionId":"<sessionID here>","user":"<user@email.com>"}
    ```

- The sessionId can be used to make requests to the Loop API.

  **Example:**

      curl -H "Cookie:sessionId=1be44e81-1c9c-4852-b4fb-a0dda52d6085" 'https://devloop.loopsoftware.fr/YPND/admin/*/session/read'

## Further Reading

- [Service to service calls using delegated user identity in the On-Behalf-Of flow](https://docs.microsoft.com/en-gb/azure/active-directory/develop/v1-oauth2-on-behalf-of-flow)
