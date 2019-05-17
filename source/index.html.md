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

Here you will see examples on how to use our API using cURL.

It is highly recomended you use the CLI (singularity-cli) to create and manage your workloads.

The documentation for our CLI can be found here: <cli_docs>

The API is only accessable through <code>https</code> for security reasons.

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

## Verify your connection to the API

```shell
curl -X GET "https://api.singularity-technologies.io/ping"
```

> This should return the text <code>pong</code>:

```text
pong
```

<aside class="notice">This is the only endpoint that does not require a HMAC signature</aside>

### HTTPs Request

`GET https://api.singularity-technologies.io/ping`

# Batch

## Get all batches for your user

```shell
curl --header "X-singularity-apikey: key" --header "X-singularity-signature: signature" -X GET "https://api.singularity-technologies.io/batch/8a76687a-9d77-4231-8a4c-58dbc4c02f1c"
```

> Returns JSON structured like this:

```json
[
  {
    "id": "8a76687a-9d77-4231-8a4c-58dbc4c02f1c",
    "mode": "pilot",
    "status": "PROCESSING",
    "created_at": "2019-05-16T10:02:01",
    "job_count": 2,
    "jobs": [
      {
        "id": "1f177101-7e56-488e-aa15-8bc93499577e",
        "status": "COMPLETED",
        "command": "python train.py --architecture classic",
        "result": {"fitness_score": 0.43}
      }, {
        "id": "2762a3d0-beee-4f96-a88e-3301806741e4",
        "status": "PROCESSING",
        "command": "python train.py --achitecture deep",
        "result": {}
      }
    ],
    "queued": 0,
    "pending": 0,
    "processed": 0,
    "processing": 1,
    "uploading": 0,
    "canceled": 0,
    "completed": 1,
    "errored": 0,
    "processing_time": 302415
  }
]
```

This endpoint allows you to check the status of your batches

### HTTPs Request

`GET https://api.singularity-technologies.io/batch/{batch_id}`

<aside class="info">Omit <code>batch_id</code> to get all your batches</aside>
<aside class="success">Timestamps are always in UTC and processing_time is to millisecond precision</aside>

## Create a batch

```shell
curl --header "X-singularity-apikey: key" --header "X-singularity-signature: signature" -X POST "https://api.singularity-technologies.io/batch/ -d @body.json"
```

> Example Body

```json
{
  "mode": "pilot",
  "jobs": [
    {
      "command": "python train.py --architecture classic",
      "image": "singularitytechnologies/testai",
      "image_tag": "latest",
      "data_set_id": "47d2004e-8e08-4fc3-b341-7a3b163e6851"`
    }, {
      "command": "python train.py --architecture deep",
      "image": "singularitytechnologies/testai",
      "image_tag": "latest",
      "data_set_id": "47d2004e-8e08-4fc3-b341-7a3b163e6851"
    }
  "requisitions": {
    "cpu": 8.0,
    "gpu": 1.0,
  }
}
```

> Returns JSON structured like this:

```json
{
  "batch_uuid": "8a76687a-9d77-4231-8a4c-58dbc4c02f1c",
  "successful": 2
  "failed": 0,
}
```

This endpoint creates a batch of machine learning model training jobs.

### HTTPs Request

`POST https://api.singularity-technologies.io/batch`

### Body Parameters

Parameter | Required | Description
--------- | ------- | -----------
mode | true | Determines which mode to run batch in, options are: pilot or production
jobs | true | Description of each training job
requisitions | true | Resources required to run each training job

RAM is free and automatically assigned based on you CPU and GPU requirements.

<aside class="warning">While we can run jobs on fractional CPUs, GPU requisitions must be whole numbers</aside>

## Cancel a batch

```shell
curl --header "X-singularity-apikey: key" --header "X-singularity-signature: signature" -X DELETE "https://api.singularity-technologies.io/batch/8a76687a-9d77-4231-8a4c-58dbc4c02f1c"
```

This endpoint will cancel all jobs in a batch that have not been processed yet.

### HTTPs Request

`DELETE https://api.singularity-technologies.io/batch/{batch_id}`

# Jobs

Jobs can not be independently lauched, they must be a part of a batch. Single job batches are acceptable.

## Get a Job

```shell
curl --header "X-singularity-apikey: key" --header "X-singularity-signature: signature" -X GET "https://api.singularity-technologies.io/job/47d2004e-8e08-4fc3-b341-7a3b163e6851"
```

> Example Response Body

```json
[
  {
    "id": "47d2004e-8e08-4fc3-b341-7a3b163e6851"
    "command": "python train.py --architecture classic"
    "image": "184762988669.dkr.ecr.eu-west-1.amazonaws.com/testai:latest",
    "mode": "pilot",
    "processing_time": 151474,
    "requisitions": {
      "cpu": 8.0,
      "gpu": 1.0,
      "ram": 60.0
    },
    "result": {"fitness_score": 0.56},
    "status": "COMPLETED"
  }
]
```

This endpoint gives you detailed information about the status of a particular job.

The result is a completely customisable and user defined fitness report.

### HTTPs Request

`GET https://api.singularity-technologies.io/job/{job_id}`

<aside class="info">Omit <code>job_id</code> to get all your batches</aside>

## Cancel a Job

```shell
curl --header "X-singularity-apikey: key" --header "X-singularity-signature: signature" -X DELETE "https://api.singularity-technologies.io/job/2762a3d0-beee-4f96-a88e-3301806741e4"
```

This endpoint will cancel a single job if it has not been processed yet.

### HTTPs Request

`DELETE https://api.singularity-technologies.io/job/{job_id}`

# Data

## Create Data Set

```shell
curl --header "X-singularity-apikey: key" --header "X-singularity-signature: signature" -X POST "https://api.singularity-technologies.io/data -d @body.json"
```

> Example Body

```json
{
  "name": "clean_data_set",
  "pilot_count": 10
}
```

> Example Response Body

```json
{
  "name": "clean_data_set",
  "dataset_uuid": "47c07222-d400-4970-9831-0abdfe24ff2a",
  "pilot_ids": [
    "47d2004e-8e08-4fc3-b341-7a3b163e6851",
    "8b92bb5b-235d-4dae-8329-417303d56b9b",
    "e1baf151-89f2-44fb-bbc5-efe48cc21719",
    "1521598f-74e0-4b30-8a23-7ceb77d021c9",
    "543c42c0-c375-442a-864a-8a05e01ce896",
    "cd2b0e33-ff79-4c5e-baf9-91ca57faba27",
    "660084e1-ddd8-4c0b-a702-2db19b081e6a",
    "b173b393-f62d-4938-a508-8369639298cd",
    "d6f02325-ab8d-47d1-a5b9-16cbe523753f",
    "507d088d-18ca-4681-a59b-671b1341efc4"
  ]
}
```

Parameter | Required | Description
--------- | ------- | -----------
name | true | Name of the data set
pilot_count | true | How many pilot data sets to create and associate with the parent

This endpoint will create a blank data set and associated pilot sets.

### HTTPs Request

`POST https://api.singularity-technologies.io/data`

## Get Data Set

```shell
curl --header "X-singularity-apikey: key" --header "X-singularity-signature: signature" -X GET "https://api.singularity-technologies.io/data/clean-data-set"
```

> Example Response Body

```json
{
  "name": "clean_data_set",
  "shard_count:" 100,
  "pilot_ids": [
    "51fb3eea-daa5-432f-800a-b8997e48d24a",
    "8b92bb5b-235d-4dae-8329-417303d56b9b",
    "e1baf151-89f2-44fb-bbc5-efe48cc21719",
    "1521598f-74e0-4b30-8a23-7ceb77d021c9",
    "543c42c0-c375-442a-864a-8a05e01ce896",
    "cd2b0e33-ff79-4c5e-baf9-91ca57faba27",
    "660084e1-ddd8-4c0b-a702-2db19b081e6a",
    "b173b393-f62d-4938-a508-8369639298cd",
    "d6f02325-ab8d-47d1-a5b9-16cbe523753f",
    "507d088d-18ca-4681-a59b-671b1341efc4"
  ]
}
```

This endpoint allows you to check the status of a data set and get a array of
associated pilot data sets.

### HTTPs Request

`POST https://api.singularity-technologies.io/data/{data_set_id}`

## Upload Data Shard

```shell
curl --header "X-singularity-apikey: key" --header "X-singularity-signature: signature" -X POST "https://api.singularity-technologies.io/data/clean-data-set/shard/e91c254ad58860a02c788dfb5c1a65d6a8846ab1dc649631c7db16fef4af2dec" -d @shard"
```

This endpoint accepts shard payloads and will assign each one to the parent data
set and a random pilot sub set. A shard should be:

1. No more that 10Mb in size.
2. A base64 encoded zipfile of cleaned data that your model training script uses.
3. A shard id should be generated as a SHA256 hash of its contents. If you omit the
   id, the api will generate one for you.

Data shards are still a new concept within the factory system and may change in
the future.

### HTTPs Request

`POST https://api.singularity-technologies.io/data/{data_set_id}/shard/{shard_id}`

# Models

## Download a model

```shell
curl --header "X-singularity-apikey: key" --header "X-singularity-signature: signature" -X GET "https://api.singularity-technologies.io/model/47d2004e-8e08-4fc3-b341-7a3b163e6851" > super_model.zip
```

This endpoint streams all captured components of a trained model.

### HTTPs Request

`GET https://api.singularity-technologies.io/model/{job_id}`
