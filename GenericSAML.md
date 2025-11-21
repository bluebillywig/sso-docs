# Generic SAML Setup Guide

This guide provides instructions for setting up Single Sign-On (SSO) with Blue Billywig Online Video Platform using SAML
2.0 for Identity Providers (IdP) that are not specifically documented in dedicated guides.

## Overview

Blue Billywig supports SAML 2.0 integration with any compliant Identity Provider. This guide covers the essential
configuration parameters and attribute mappings required for a successful integration.

## Required IdP Configuration

### Service Provider (SP) Settings

Configure your Identity Provider with the following Blue Billywig service provider details:

- **Audience URI (Entity ID)**: `urn:amazon:cognito:sp:eu-west-1_2Gn0U5Y6I`
- **IdP response URL** or **Assertion Consumer Service URL (ACS URL)**: `https://auth.bluebillywig.com/saml2/idpresponse`
- **Single Logout URL**: `https://auth.bluebillywig.com/saml2/logout`

> [!NOTE]
> The above settings are for connecting to the Blue Billywig production environment. When you need to connect to the Blue
> Billywig test environment, use the following settings instead:
>
> - **Audience URI (Entity ID)**: `urn:amazon:cognito:sp:eu-west-1_nPGHGV8P3`
> - **IdP response URL** or **Assertion Consumer Service URL (ACS URL)**: `https://test.auth.bluebillywig.com/saml2/idpresponse`
> - **Single Logout URL**: `https://test.auth.bluebillywig.com/saml2/logout`

### Basic SAML Configuration

1. **SAML Version**: 2.0
2. **Binding**: HTTP-POST for SSO and SLO
3. **NameID Format**: Persistent

> [!NOTE]
> Some Identity Providers only support `HTTP-REDIRECT` binding for the Single Logout URL (most notably Microsoft Entra 
> ID). In that case use the following **Single Logout URL**: `https://bbvms.com/saml2/logout`. Or when connecting to the
> Blue Billywig test environment: `https://test.bbvms.com/saml2/logout`.

## Attribute Mapping

Configure your IdP to send the following user attributes in the SAML assertion. You can choose between **OID (Object
Identifier)** format, or **Claims Schema** format:

### Option 1: OID Format (Recommended)
| OID URI                              | Purpose                |
|:-------------------------------------|:-----------------------|
| `urn:oid:2.5.4.3`                    | User ID                |
| `urn:oid:2.5.4.42`                   | First Name             |
| `urn:oid:2.5.4.4`                    | Last Name              |
| `urn:oid:0.9.2342.19200300.100.1.3`  | Email Address          |
| `urn:oid:1.3.6.1.4.1.5923.1.1.1.1`   | Group Membership/Roles |

### Option 2: Claims Schema Format
| Claim URI                                                             | Purpose                |
|:----------------------------------------------------------------------|:-----------------------|
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`          | User ID                |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`     | First Name             |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`       | Last Name              |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`  | Email Address          |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`        | Group Membership/Roles |

### Required Attributes

| Attribute Purpose | Required | Description                              |
|-------------------|----------|------------------------------------------|
| Username/Login    | Yes      | User's login identifier                  |
| First Name        | Yes      | User's first name                        |
| Last Name         | Yes      | User's surname                           |
| Email Address     | Yes      | User's email address                     |
| Group Membership  | No       | User's groups or roles for authorization |

## Group/Role Mapping

The **Group Membership** attribute is required when the Identity Provider is used to authorize users in Blue Billywig.
Ideally, group identifiers are used. Each group can the be mapped to a publication and/or role in Blue Billywig.

**Format**: Send groups as a multi-value attribute with group identifiers or names that will be mapped to Blue Billywig
publications and roles during the integration setup phase.

## Federation Metadata

After configuring your IdP, you must provide the federation metadata to Blue Billywig for the integration to be completed.

### Metadata Delivery Options

**Option 1: Metadata URL (Preferred)**
- Provide the publicly accessible metadata endpoint URL from your IdP.
- Example: `https://yourcompany.idprovider.com/app/metadata`

**Option 2: Metadata Document**
- Export the federation metadata XML document from your IdP.
- Send the complete XML file to your Blue Billywig account manager.

### What to Send to Blue Billywig

Contact your Blue Billywig account manager with:

1. **Environment**: Specify if this is for Production or Test environment
2. **Federation Metadata**: Either the metadata URL or XML document
3. **Attribute Format**: Confirm which attribute format you're using (Claims Schema or OID)
4. **Group Mapping**: Provide a list of groups/roles that will be sent and their intended Blue Billywig permission
   mappings (publications and/or roles)
5. **Contact Information**: Technical contact for troubleshooting

## SSL Certificates

Your IdP will need to trust Blue Billywig's SSL certificates for secure communication. The certificates are available
in this repository:

- **Production**: `crt/auth.bluebillywig.com.crt`
- **Test Environment**: `crt/test.auth.bluebillywig.com.crt`

## Testing and Validation

1. **Attribute Validation**: Verify all required attributes are being sent correctly.
2. **Group Mapping**: Confirm group/role attributes contain expected values.
3. **Single Logout**: Test both SSO login and single logout functionality.

## Troubleshooting

### Common Issues

- **Missing Attributes**: Ensure all required attributes are configured and have values.
- **Incorrect URLs**: Double-check EntityID, ACS URL, and Logout URL configuration.
- **Certificate Issues**: Verify SSL certificate trust and validity.
- **Group Mapping**: Confirm group attribute is multi-valued and contains expected group identifiers or names.

### Support

For technical support during integration:
- Contact your Blue Billywig account manager.
- Provide SAML response samples (with sensitive data redacted).
- Include IdP configuration screenshots if helpful.

## Security Considerations

- Use signed SAML assertions and responses when possible.
- Implement proper certificate validation.
- Regularly rotate signing certificates.
- Monitor authentication logs for suspicious activity.
- Follow your organization's security policies for IdP configuration.
