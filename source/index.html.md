---
title: Singularity Technologies API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:

includes:
  - errors

search: true
---

# Introduction

This is the Singularity Technologies Machine Learning factory API documentation.

Here you will see examples on how to use our API using both cURL and our Command Line Interface (CLI) singularity-cli.

It is highly recomended to use the CLI to create and manage your workloads.

The API is only accessable through <code>https</code>.

# Authentication

The API use both keys and secrets in conjunction with HMAC signatures to allow access to the API and verify message integrity.

The API expects a valid key and HMAC signature to be included in each request, using the following headers:

`X-singularity-apikey: key`

`X-singularity-signature': signature`

<aside class="notice">
You must replace <code>key</code> and <code>signature</code> with your personal API key and generated signature.
</aside>

The HMAC uses SHA-512 as its hashing algorithm and the signature pattern is as follows:

`<method>\n<endpoint>\n<payload>`

This is handled autonomously by the CLI.

# Ping

Verify your connection to the API

<aside class="notice">This is the only endpoint that does not require a HMAC signature</aside>

```shell
curl -X GET "https://api.singularity-technologies.io/ping"

or

singularity-cli ping
```

> This should return the text <code>pong</code>:

```text
pong
```

# Batch

## Create a batch
This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

