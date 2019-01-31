# Authentication Overview

<!-- TOC -->

- [Overview](#overview)
- [Further Reading](#further-reading)

<!-- /TOC -->

## Overview

This document and those linked below introduce the process of authenticating your application for operation with the Loop platform.

Whether you are a system administrator or an application developer, the following pages describe the required steps for authentication of your application to work with Loop.

Your server-side application uses Microsoft identity services to obtain permission from Loop to exchange data securely with the platform, without requiring access to users' data.

The Microsoft identity platform (formerly Azure AD) allows applications to get tokens to call a Microsoft-tenanted API such as Loop and supports industry standard authentication protocols OAuth 2.0 and OpenID Connect.

The OAuth 2.0 flow allows your app to access the Loop API regardless of whether users are interacting with the app.

OpenID Connect authentication protocol (built on OAuth 2.0) permits secure user sign-in to a web application, while the v2.0 endpoint's implementation of OpenID Connect enables both sign-in and API access to your web-based apps by using a single integration.

For step instructions to authenticate your app with Loop, please follow [Authentication for Administrators](../Authentication-admin.md) or [Authentication for Developers](../Authentication-authorize-session-endpoint.md).

---

## Further Reading

1. [Application and service principal objects in Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)
2. [Microsoft: What is authentication?](https://docs.microsoft.com/en-us/azure/active-directory/develop/authentication-scenarios)

 