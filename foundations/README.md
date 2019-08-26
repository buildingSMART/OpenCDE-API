[![Official repository by buildingSMART International](https://img.shields.io/badge/buildingSMART-Official%20Repository-orange.svg)](https://www.buildingsmart.org/)
[![This repo is managed by the CDE API Implementers Group](https://img.shields.io/badge/-BCF%20Implementers%20Group-blue.svg)](https://img.shields.io/badge/-BCF%20Implementers%20Group-blue.svg)

# BSI API Foundations
![](https://raw.githubusercontent.com/BuildingSMART/BCF/master/Icons/BCFicon128.png)

**Version 0.1** based on BCF API v2.1.
[BCF API GitHub repository](https://github.com/buildingSMART/BCF-API/tree/release_2_1)

**Table of Contents**

<!-- toc -->

- [1. Introduction](#1-introduction)
  * [1.1 Paging, Sorting and Filtering](#11-paging-sorting-and-filtering)
  * [1.2 Caching](#12-caching)
  * [1.3 Updating Resources via HTTP PUT](#13-updating-resources-via-http-put)
  * [1.4 Cross Origin Resource Sharing (CORS)](#14-cross-origin-resource-sharing-cors)
  * [1.5 Http Status Codes](#15-http-status-codes)
  * [1.6 Error Response Body Format](#16-error-response-body-format)
  * [1.7 DateTime Format](#17-datetime-format)
  * [1.8 Additional Response and Request Object Properties](#18-additional-response-and-request-object-properties)
  * [1.9 Binary File Uploads](#19-binary-file-uploads)
- [2. Public Services](#2-public-services)
  * [2.1 Versions Service](#21-versions-service)
  * [2.2 Authentication Services](#22-authentication-services)
    + [2.2.1 Obtaining Authentication Information](#221-obtaining-authentication-information)
    + [2.2.2 OAuth2 Example](#222-oauth2-example)
    + [2.2.3 OAuth2 Protocol Flow - Dynamic Client Registration](#223-oauth2-protocol-flow---dynamic-client-registration)
  * [2.3 User Services](#23-user-services)
    + [2.3.1 Get current user](#231-get-current-user)


<!-- tocstop -->

# 1. Introduction

This is 

## 1.1 Paging, Sorting and Filtering

When requesting collections of items, a BSI API Server should offer URL parameters according to the OData v4 specification. It can be found at [http://www.odata.org/documentation/](http://www.odata.org/documentation/).

## 1.2 Caching

ETags, or entity-tags, are an important part of HTTP, being a critical part of caching, and also used in "conditional" requests.

The ETag response-header field value, an entity tag, provides for an "opaque" cache validator.
The easiest way to think of an etag is as an MD5 or SHA1 hash of all the bytes in a representation. If just one byte in the representation changes, the etag will change.

ETags are returned in a response to a GET:

    joe@joe-laptop:~$ curl --include http://bitworking.org/news/
    HTTP/1.1 200 Ok
    Date: Wed, 21 Mar 2007 15:06:15 GMT
    Server: Apache
    ETag: "078de59b16c27119c670e63fa53e5b51"
    Content-Length: 23081
    …

The client may send an "If-None-Match" HTTP Header containing the last retrieved etag. If the content has not changed the server returns a status code 304 (not modified) and no response body.

## 1.3 Updating Resources via HTTP PUT

Whenever a resource offers the HTTP PUT method to be updated as a whole.

This means that there is no partial update mechanism for objects but every PUT request is sending the whole object representation. PUT schemas may exclude server generated values that cannot be edited, such as creation dates or authors.

## 1.4 Cross Origin Resource Sharing (CORS)

To work with browser based API clients using [Cross Origin Resource Sharing (Cors)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing), servers will put the `Access-Control-Allow'` headers in the response headers and specifiy who can access the servers Json resources. The client can look for these values and proceed with accessing the resources.

In a CORS scenario, web clients expect the following headers:
* `Access-Control-Allow-Headers: Authorization, Content-Type, Accept` to allow the `Authorization`, `Content-Type` and `Accept` headers to be used via [XHR requests](https://en.wikipedia.org/wiki/XMLHttpRequest)
* `Access-Control-Allow-Methods: GET, POST, PUT, OPTIONS` to allow the Http methods the API needs
* `Access-Control-Allow-Origin: example.com` to allow XHR requests from the `example.com` domain to the BSI API server

For example, Asp.Net applications in IIS need the following entries in their `web.config` file. `*` means the server allows any values.

    <httpProtocol>
        <customHeaders>
            <add name="Access-Control-Allow-Headers" value="Content-Type, Accept, X-Requested-With,  Authorization" />
            <add name="Access-Control-Allow-Methods" value="GET, POST, PUT, DELETE, OPTIONS" />
            <add name="Access-Control-Allow-Origin" value="*" />
        </customHeaders>
    </httpProtocol>

## 1.5 Http Status Codes

The BSI APIs rely on the regular Http Status Code definitions. Good sources are [Wikipedia](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) or the [HTTP/1.1 Specification](https://tools.ietf.org/html/rfc7231).

Generally, these response codes shall be used in the API:
* `200 - OK` for `GET` requests that return data or `PUT` requests that update data
* `201 - Created` for `POST` requests that create data

`POST` and `PUT` requests do usually include the created resource in the response body. Exceptions to this rule are described in the specific section for the resource.

## 1.6 Error Response Body Format

BCI APIs have a specified error response body format [error.json](schemas/error.json).

## 1.7 DateTime Format

DateTime values in this API are supposed to be in ISO 8601 compliant `YYYY-MM-DDThh:mm:ss` format with optional time zone indicators. This is the same format as defined in the Xml `xs:dateTime` type as well as the result of JavaScripts Date.toJson() output.

For example, `2016-04-28T16:31:12.270+02:00` would represent _Thursday, April 28th, 2016, 16:31:12 (270ms) with a time zone offset of +2 hours relative to UTC._
Please note that the colon in the timezone offset is optional, so `+02:00` is equivalent to `+0200`.

## 1.8 Additional Response and Request Object Properties

All API response and request Json objects may contain additional properties that are not covered by this specification.
This is to allow server and client implementations freedom to add additional functionality. Servers and clients shall ignore those properties and must not produces errors on additional properties. Servers and clients are not required to preserve these properties.

## 1.9 Binary File Uploads

Some endpoints in the BSI APIs may expect binary file uploads, such as the BCF Document Service and the BCF BIM Snippet Service.

In such cases, files should be sent with the following Http headers:

    Headers:
    Content-Type: application/octet-stream;
    Content-Length: {Size of file in bytes};
    Content-Disposition: attachment; filename="{Filename.extension}";

----------

# 2. Public Services

## 2.1 Versions Service

[versions_GET.json](schemas/versions_GET.json)

**Resource URL (public resource)**

    GET /bsi/versions

**Parameters**

|Parameter|Type|Description|Required|
|---------|----|-----------|--------|
|api_id|string|Identifier of the API|true|
|version_id|string|Identifier of the version|true|
|detailed_version|string|Url to specification on GitHub|false|

Returns a list of all supported BSI API versions of the server.

**Example Request**

    GET /bsi/versions

**Example Response**

    Response Code: 200 - OK
    Body:
    {
        "versions": [{
            "api_id": "bsi-foundations",
            "version_id": "0.1",
            "detailed_version": "https://github.com/BuildingSMART/BSI-FOUNDATIONS"
        }, {
            "api_id": "bcf",
            "version_id": "2.1",
            "detailed_version": "https://github.com/BuildingSMART/BCF-API"
        }]
    }

----------

## 2.2 Authentication Services

### 2.2.1 Obtaining Authentication Information

[auth_GET.json](schemas/auth_GET.json)

Authentication is based on the [OAuth 2.0 Protocol](http://tools.ietf.org/html/draft-ietf-oauth-v2-22).

**Resource URL (public resource)**

    GET /bsi/{version}/auth

**Parameters**

|Parameter|Type|Description|Required|
|---------|----|-----------|--------|
|oauth2_auth_url|string|URL to authorization page (used for Authorization Code Grant and Implicit Grant OAuth2 flows)|false|
|oauth2_token_url|string|URL for token requests|false|
|oauth2_dynamic_client_reg_url|string|URL for automated client registration|false|
|http_basic_supported|boolean|Indicates if Http Basic Authentication is supported|false|
|supported_oauth2_flows|string[]|array of supported OAuth2 flows|

If `oauth2_auth_url` is present, then `oauth2_token_url` must also be present and vice versa. If properties are not present in the response, clients should assume that the functionality is not supported by the server, e.g. a missing `http_basic_supported` property would indicate that Http basic authentication is not available on the server.

OAuth2 flows are described in detail in the [OAuth2 specification](https://tools.ietf.org/html/rfc6749). BSI API servers may support the following workflows:
* `authorization_code_grant` - [4.1 - Authorization Code Grant](https://tools.ietf.org/html/rfc6749#section-4.1)
* `implicit_grant` - [4.2 - Implicit Grant](https://tools.ietf.org/html/rfc6749#section-4.2)
* `resource_owner_password_credentials_grant` - [4.3 - Resource Owner Password Credentials Grant](https://tools.ietf.org/html/rfc6749#section-4.3)

The [OAuth2 Client Credentials Grant (section 4.4)](https://tools.ietf.org/html/rfc6749#section-4.4) is not supported since it does not contain any user identity.
Also the [Extension Grants (section 4.5)](https://tools.ietf.org/html/rfc6749#section-4.5) are not supported.

**Example Request**

    GET /bsi/0.1/auth

**Example Response**

    Response Code: 200 - OK
    Body:
    {
        "oauth2_auth_url": "https://example.com/bsi/oauth2/auth",
        "oauth2_token_url": "https://example.com/bsi/oauth2/token",
        "oauth2_dynamic_client_reg_url": "https://example.com/bsi/oauth2/reg",
        "http_basic_supported": true,
        "supported_oauth2_flows": [
            "authorization_code_grant",
            "implicit_grant",
            "resource_owner_password_credentials_grant"
        ]
    }

### 2.2.2 OAuth2 Example

An example for the OAuth2 Authorization Grant workflow [can be found here](OAuth2Examples.md).

### 2.2.3 OAuth2 Protocol Flow - Dynamic Client Registration

[dynRegClient\_POST.json](schemas/dynRegClient_POST.json)

[dynRegClient\_GET.json](schemas/dynRegClient_GET.json)

The following part describes the optional dynamic registration process of a client. BSI API Servers may offer additional processes registering clients, for example allowing a client application developer to register his client on the servers website.

The resource url for this service is server specific and is returned as `oauth2_dynamic_client_reg_url` in the `GET /bsi/{version}/auth` resource.

Register a new client :

**Parameters**

JSON encoded body using the `application/json` content type.

|parameter|type|description|
|---------|----|-----------|
|client_name|string (max. length 60)|The client name|
|client_description|string (max. length 4000)|The client description|
|client_url|string|An URL providing additional information about the client|
|redirect_url|string|An URL where users are redirected after granting access to the client|

**Example Request**

    POST https://example.com/bsi/oauth2/reg
    Body:
    {
        "client_name": "Example Application",
        "client_description": "Example CAD desktop application",
        "client_url": "http://example.com",
        "redirect_url": "http://localhost:8080"
    }

**Example Response**

    Response Code: 201 - Created
    Body:
    {
        "client_id": "cGxlYXN1cmUu",
        "client_secret": "ZWFzdXJlLg=="
    }

----------

## 2.3 User Services

### 2.3.1 Get current user

[user_GET.json](schemas/user_GET.json)

**Resource URL**

    GET /bsi/{version}/current-user

**Example Request**

    GET /bsi/0.1/current-user

**Example Response**

    Response Code: 200 - OK
    Body:
    {
        "id": "Architect@example.com",
        "name": "John Doe"
    }

----------

