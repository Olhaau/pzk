---
title: User Provisioning – Administration Guide
keywords: source
---

# User Provisioning – Administration Guide

url:: https://docs.posit.co/ide/server-pro/user_provisioning/user_provisioning.html
up: [[sources]]

[Workbench](https://docs.posit.co/ide/server-pro/user_provisioning/../#intended-audience "This feature is only available with Posit Workbench.")

## Overview

Workbench can be configured to automatically manage local accounts for users that authenticate via an external identity provider (IdP) that supports the System for Cross-domain Identity Management (SCIM) protocol. Reference the [SCIM: System for Cross-domain Identity Management](https://scim.cloud/) documentation for additional information on the SCIM specification.

## Supported IdPs

Workbench currently supports the following IdPs for user provisioning (support for other IdPs may be added in the future):

* Okta: Reference [Employee and Customer Identity Solutions | Okta](https://www.okta.com/) for additional information.
* Microsoft Entra ID: Reference [Microsoft Entra ID (formerly Azure Active Directory) | Microsoft Security](https://www.microsoft.com/en-us/security/business/identity-access/microsoft-entra-id) for additional information.

## Prerequisites

* Single Sign-On (SSO) must be configured in Workbench. Reference the [OpenID Connect Authentication](https://docs.posit.co/ide/server-pro/user_provisioning/../authenticating_users/openid_connect_authentication.html) or the [SAML Single Sign-On (SSO) Authentication](https://docs.posit.co/ide/server-pro/user_provisioning/../authenticating_users/saml_sso.html) sections of this guide for more information.
* A mechanism for provisioning user home directories must be configured in the system. See the [Home directories](https://docs.posit.co/ide/server-pro/user_provisioning/../user_provisioning/configuration.html#home-directories) section of this guide for more information.
* Workbench, or an external proxy in front of Workbench, must be configured to use HTTPS with a Certificate Authority (CA) signed certificate. See the [Running with a Proxy](https://docs.posit.co/ide/server-pro/user_provisioning/../access_and_security/running_with_a_proxy.html) section of this guide for more information on how to configure Workbench to use a proxy, and the [Set Up SSL](https://docs.posit.co/ide/server-pro/user_provisioning/../hardening/set_up_ssl.html) section of this guide for more information on how to configure Workbench to use HTTPS.

## How it works

### Provisioning

When a user is assigned to the Workbench application in the IdP, the IdP sends a request to Workbench to create a new user. Workbench assigns a unique user ID (UID), home directory, and POSIX username to the user.

Workbench stores the following information for each user derived from the IdP:

* `user_id`: The user ID (UID) of the user, used as the POSIX/Operating-System-Level ID of the user.
* `user_name`: The user’s username as provided by the IdP.
* `posix_name`: The user’s POSIX username is used to authenticate the user via PAM and to create the user’s home directory. By default, this is the same as the user’s `user_name` with any domain suffix removed.
* `homedir`: The user’s home directory.
* `is_admin`: A boolean value indicating whether or not the user is an Administrator.
* `shadow`: The user’s shadow password. This is the password that is used to authenticate the user via PAM.
* `display_name`: The user’s display name as provided by the IdP.
* `email`: The user’s email address as provided by the IdP.

This information is stored in the `licensed_users` table in the Workbench database. See the [Data Dictionary](https://docs.posit.co/ide/server-pro/user_provisioning/../reference/data_dictionary.html) for more information.

#### User ID assignment

When a user is created, Workbench assigns a UID to the user. The UID is used as the POSIX/Operating-System-Level ID of the user. By default, Workbench begins provisioning users with a minimum UID of 1000, but this is configurable. See the [Configuring the minimum starting UID for provisioned users](https://docs.posit.co/ide/server-pro/user_provisioning/../user_provisioning/configuration.html#configuring-the-minimum-starting-uid-for-provisioned-users) section for more information.

Workbench checks for UID and GID collisions when creating a new user. When an available UID is found, Workbench verifies that it is also available as a GID.

It is possible for the IdP to provide a UID in the provisioning request to Workbench, and Workbench uses the provided UID if it is available. If the provided UID is already in use by another user on the system, Workbench returns an error. See the section on [Adding Workbench attributes to identity providers](https://docs.posit.co/ide/server-pro/user_provisioning/../user_provisioning/configuration.html#custom-workbench-attributes-in-idp) for more information.

Existing users on the system who were provisioned by another mechanism can be transferred to Workbench user provisioning. When existing users are transferred to Workbench user provisioning, they maintain their existing UID. Workbench derives the POSIX username from the username provided by the IdP and performs a lookup to find the existing UID for the user. See the [Transferring Existing Users](https://docs.posit.co/ide/server-pro/user_provisioning/../user_provisioning/transferring_existing_users.html) section for more information.

### SCIM API

When user provisioning is enabled, Workbench hosts an endpoint that implements the SCIM protocol. The IdP uses this endpoint to create, update, and delete users in Workbench. Workbench uses this endpoint to retrieve user information.

The endpoint is hosted at the following URL:

    https://<workbench-hostname>/scim/v2

Workbench uses a bearer token to authenticate requests to the SCIM API. See the [Managing tokens](https://docs.posit.co/ide/server-pro/user_provisioning/../user_provisioning/managing_tokens.html) section below for more information on managing Workbench user service tokens.

### NSS module

Workbench provides a Name Service Switch (NSS) module that surfaces users from a Posit Workbench instance to a local Linux system by querying Workbench’s SCIM API. The system uses this module to retrieve necessary user information. The NSS module is installed in the system when Workbench is installed, and provides functionality for the following NSS databases:

* `passwd`
* `group`
* `shadow`

This NSS module is a required component for Workbench user provisioning, as it is used by Workbench, sessions, and other components to retrieve user information.

Reference [Name Service Switch - Wikipedia](https://en.wikipedia.org/wiki/Name_Service_Switch) for additional information on NSS.
Back to top
