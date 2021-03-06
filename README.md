<!--
Table of contents created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)

use the following command: `gh-md-toc README.md | grep -v g-emoji` to filter out lines with emoji
-->

<!--
---
title: API Manifesto
tags: [api,manifesto,best practice]
link: https://github.com/monstar-lab-oss/API-manifesto
---
-->

<!--
New section template:

## Section

### TODO
<details>
<summary>Click to see examples</summary>

#### ✅

#### ⛔️

</details>
-->

# API Manifesto
Documents how to write APIs

## Introduction

TODO

Table of Contents
=================

   * [API Manifesto](#api-manifesto)
      * [Introduction](#introduction)
   * [Table of Contents](#table-of-contents)
   * [Requests](#requests)
      * [URLs](#urls)
         * [Prefix API endpoints](#prefix-api-endpoints)
         * [API versioning](#api-versioning)
      * [Request Headers](#request-headers)
         * [Protected endpoints](#protected-endpoints)
         * [Supporting localization](#supporting-localization)
         * [Making debugging easier](#making-debugging-easier)
   * [Responses](#responses)
      * [Response Body](#response-body)
         * [Object at the root level](#object-at-the-root-level)
         * [Return an empty collection when there are no results](#return-an-empty-collection-when-there-are-no-results)
      * [Use null or unset keys that are not set](#use-null-or-unset-keys-that-are-not-set)
      * [Status Codes](#status-codes)
         * [TODO](#todo)
   * [Auth](#auth)
      * [TODO](#todo-1)
   * [Error Handling](#error-handling)
      * [TODO](#todo-2)
   * [Localization](#localization)
      * [TODO](#todo-3)
   * [Timeouts](#timeouts)
      * [TODO](#todo-4)
   * [Pagination](#pagination)
      * [TODO](#todo-5)
   * [Inspiration](#inspiration)
   
# Requests

## URLs

### Prefix API endpoints

Prefix API endpoints with `/api/` to separate them from other URLs like HTML views served on the same server.

<details>
<summary>Click to see examples</summary>

#### ✅

Make sure to prefix your API endpoints with `/api/`:

```bash
www.example.com/api/v1/products/1
```

</details>

### API versioning

Versioning your API allows you to make non-backwards compatible changes to your API for newer clients by introducing new versions of endpoints while not breaking existing clients.

Include the API version in the URL. Versions start with 1 and are prefixed with `v`. The version path component should come right after the [`api`](#prefix-api-endpoints) path component.

In case of an existing API that doesn't have this versioning scheme but needs a new version, skip `v1` and go straight to `v2`.

> We're not recommending to use the headers (typically the Accept header) for versioning. The URL based approach is more obvious, usually simpler to implement, and testing URLs can be done in the browser.

<details>
<summary>Click to see examples</summary>

#### ✅

Include the API's version in the URL:

```bash
www.example.com/api/v2/auth/login
```

#### ⛔️

Don't depend on a header like "Accept" for versioning:

```bash
Accept = "application/vnd.example.v1+json"
```

</details>

### REST resources
After the API prefix and the version comes the part of the URL path that identifies the resource -- the piece of data you are interested in. Refer to a type of resource with a plural noun (eg. "users"). Directly following such a noun can be an identifier that points to a single instance.
A resource can also be nested, usually if there some sort of parent/child relationship. This can be expressed by appending another plural noun to the URL.

<details>
<summary>Click to see examples</summary>

#### ✅

Refer to a resource with a plural noun:

```bash
/api/v1/shops
```

#### ✅

Use an identifier following a noun to refer to a single entity:

```bash
/api/v1/products/42
```

#### ✅

Refer to a nested resource like so:

```bash
/api/v1/posts/1/comments
```

In some cases it can be ok to simplify and have the child object at the beginning as long as the child object's id is globally unique:

```bash
/api/v1/comments/87
```

Be careful with this since this approach lack the extra safety of asserting that the resource you are referring to belongs to the parent resource you think it does.

</details>

### Query parameters

Query parameters are like meta data to the (usually GET) URL request. They can be used when you need more control over what data should be returned. Good use cases include filters and sorting. Some things are better suited for headers, such as providing authentication and indicating the preferred encoding type.

<details>
<summary>Click to see examples</summary>

#### ✅

Use query parameters for a paginated endpoint to define which page and with how many results per page you want to retrieve:

```bash
/api/v1/posts?page=2&perPage=10
```

#### ⛔️

Do not use query parameters for authentication:

```bash
/api/v1/posts?apiKey=a7dhas8u
```

</details>

### HTTP Methods

HTTP methods are used to indicate what action to perform with the resource.

#### GET

A GET call is used to retrieve data and should not result in changes to the accessed resource. Multiple identical requests should have the same effect as a single request (idempotency).

#### POST

POST is used to create new resources.

#### PATCH

PATCH requests modify existing resources. Only fields that need to be updated need to be included - all others will be left as they are. In order to "unset" optional properties use `null` for the value.

#### PUT

With PUT calls we can replace entire objects. Only the database identifier should not be changed.

#### DELETE

To delete a resource, use the DELETE method.

#### HEAD

A HEAD call must never return a body. It can be used to see if an object exists and to see if a cached value is still up to date.

## Request Headers

### Protected endpoints

Use the `Authorization` header to consume protected endpoints. See the [Auth](#auth) section for more information on how to handle authorization and authentication.

<details>
<summary>Click to see examples</summary>

#### ✅

Use `Authorization` to authorize:

```bash
Authorization = "Basic QWxhZGRpbjpPcGVuU2VzYW1l"
```

#### ⛔️

Avoid using custom headers for authorization:

```bash
UserToken = "QWxhZGRpbjpPcGVuU2VzYW1l"
```

</details>

### Supporting localization

In order to support localization now and in the future, the `Accept-Language` should be used to indicate the client's language towards the API. 

<details>
<summary>Click to see examples</summary>

#### ✅

Use [ISO 639-1](http://www.loc.gov/standards/iso639-2/php/code_list.php) codes to indicate the preferred language of the response.

```bash
Accept-Language = "da"
```

Use a prioritized list of languages to influence the fallback language:

```bash
Accept-Language = "da, en"
```

#### ⛔️

Avoid using other standards than ISO 639-1 for specifying the preferred language:

```bash
Accept-Language = "danish"
```

</details>

### Making debugging easier

Use headers to give the API information about the consumer to ease debugging. There's no industry standard, so feel free to make your own convention, just remember to use it consistently.

<details>
<summary>Click to see examples</summary>

#### ✅

```bash
Client-Meta-Information = iOS;staging;v1.2;iOS12;iPhone13
```

</details>

See [N-Meta-Vapor](https://github.com/nodes-vapor/n-meta)
See [N-Meta-PHP](https://github.com/monstar-lab-oss/n-meta-php)
See [N-Meta-Laravel](https://github.com/monstar-lab-oss/n-meta-laravel)

# Responses

## Response Body

### Object at the root level

A body should always return an object at the root level. This enables including additional data about the response such as metadata separate from the object(s). We recommend using `data` for successful requests with meaningful response data and `error` for unsuccessful requests with error data being returned.

<details>
<summary>Click to see examples</summary>

#### ✅

Returning a collection should be encapsulated in a key:

```json
{
    "data": [
        {
            "username": "..."
        },
        {
            "username": "..."
        }
    ]
}
```

Returning an object (e.g. a user) should also use the `data` key:

```json
{
    "data": {
        "username": "..."
    }
}
```

Returning an error should use the `error` key:

```json
{
    "error": {
        "description": "..."
    }
}
```

Please see the [error section](#errors) for more information.

#### ⛔️

Avoid returning collections at the top level in the response:

```json
[
    {
        "email": "..."
    },
    {
        "email": "..."
    }
]
```

Avoid returning data that are not encapsulated in a root key (`data` or `error`):

```json
{
    "error": true,
    "description": "..."
}
```

</details>

### Return an empty collection when there are no results

To make it easier for the API consumer, return HTTP status code `200` with an empty collection instead of e.g. `204` with no body.

<details>
<summary>Click to see examples</summary>

#### ✅

Combine HTTP status code `200` with empty collections:

```json
{
    "data": []
}
```

#### ⛔️

Avoid using HTTP status code `204` for empty collections.

</details>

### Use `null` or unset keys that are not set

In case of missing values return them as `null` or don't include them. Do not use empty objects or empty strings.

<details>
<summary>Click to see examples</summary>

#### ✅

Return a value as `null`:

```json
{
    "data": {
        "email": null,
        "name": "..."
    }
}
```

Unset a key without a value:

```json
{
    "data": {
        "name": "..."
    }
}
```

#### ⛔️

Avoid including a key without a meaningful value:

```json
{
    "data": {
        "name": ""
    }
}
```
</details>

## Status Codes

### TODO

<details>
<summary>Click to see examples</summary>

#### ✅

#### ⛔️

</details>

# Auth

## TODO

<details>
<summary>Click to see examples</summary>

### ✅

### ⛔️

</details>

# Error Handling

## TODO

<details>
<summary>Click to see examples</summary>

### ✅

### ⛔️

</details>

# Localization

## TODO

<details>
<summary>Click to see examples</summary>

### ✅

### ⛔️

</details>

# Timeouts

## TODO

<details>
<summary>Click to see examples</summary>

### ✅

### ⛔️

</details>

# Pagination

## TODO

<details>
<summary>Click to see examples</summary>

### ✅

### ⛔️

</details>

# Inspiration

These guidelines have been made with inspiration from the following API guidelines:

- [Microsoft API Guidelines](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md)
- [Zalando API Guidelines](https://opensource.zalando.com/restful-api-guidelines/)
- [PayPal API Guidelines](https://github.com/paypal/api-standards/blob/master/api-style-guide.md)
- [Atlassian API Guidelines](https://developer.atlassian.com/server/framework/atlassian-sdk/atlassian-rest-api-design-guidelines-version-1/)
