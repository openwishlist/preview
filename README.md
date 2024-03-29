<!--
title: 'Serverless Link Preview Service'
description: 'This service provide simple HTTP API to get url preview with Node.js running on AWS Lambda and API Gateway using the Serverless Framework.'
layout: Doc
framework: v3
platform: AWS
language: nodeJS
authorLink: 'https://github.com/openwishlist/preview'
authorName: 'Milind Singh'
authorAvatar: 'https://avatars1.githubusercontent.com/u/13742415?s=200&v=4'
-->

# Serverless Link Preview Service

![GitHub package.json version](https://img.shields.io/github/package-json/v/openwishlist/preview?color=dark-green) ![GitHub code size in bytes](https://img.shields.io/github/languages/code-size/openwishlist/preview) ![GitHub top language](https://img.shields.io/github/languages/top/openwishlist/preview) [![Deployment](https://github.com/openwishlist/preview/actions/workflows/deploy.yml/badge.svg?branch=main)](https://github.com/openwishlist/preview/actions/workflows/deploy.yml) [![Codacy Badge](https://app.codacy.com/project/badge/Grade/de29ea4c910f49a0bf80c0d4c55f03a8)](https://www.codacy.com/gh/openwishlist/preview/dashboard?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=openwishlist/preview&amp;utm_campaign=Badge_Grade)

This service provide simple HTTP API to get url preview with Node.js running on AWS Lambda and API Gateway using the Serverless Framework.

Status:

- [x] curl based scraping
- [x] browser based scraping
- [x] user agent randomization
- [x] stealth browser plugin
- [x] json schema parsing
- [ ] unit tests
- [ ] e2e tests
- [ ] api errors and http status correction
- [ ] api gateway v2 migration
- [ ] performance tests

## Usage

### Deployment

```bash
serverless deploy

# deploy dev env
serverless deploy --stage dev

# deploy live env with debug logs
serverless deploy --stage live --verbose
```

After deploying, you should see output similar to:

```bash
Deploying aws-node-http-api-project to stage dev (us-east-1)

✔ Service deployed to stack aws-node-http-api-project-dev (152s)

endpoint: GET - https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/
functions:
  hello: aws-node-http-api-project-dev-hello (1.9 kB)
```

_Note_: In current form, after deployment, your API is public and can be invoked by anyone. For production deployments, you might want to configure an authorizer. For details on how to do that, refer to [http event docs](https://www.serverless.com/framework/docs/providers/aws/events/apigateway/).

### Invocation

After successful deployment, you can call the created application via HTTP:

```bash
curl https://xxxxxxx.execute-api.us-east-1.amazonaws.com/
```

Which should result in response similar to the following (removed `input` content for brevity):

```json
{
    "status": 200,
    "data": {
        "schema": {
            // product schema object
        },
        "image": "string",
        "meta": {
            "parser": "string",
            "modifiedDate": "date",
            "createdDate": "date"
        },
        "name": "string",
        "description": "string",
        "sku": "string",
        "url": "string"
    }
}
```

### Local development

#### Setup

```bash
PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=1 npm install
```

#### Run

You can invoke your function locally by using the following command:

```bash
serverless invoke local --function preview --data '{ "pathParameters": {"url":"aHR0cHM6Ly9hbXpuLmV1L2QvNzBPN2JMVQ=="}}'
```

`url` is base64 encode string url of a product.  

Which should result in response similar to the following:

```json
{
    "status": 200,
    "data": {
        "schema": {
            "offers": {
                "priceCurrency": "₹",
                "@id": "B08TTPZ6MW",
                "sku": "B08TTPZ6MW",
                "offeredBy": "SRINIVASA Textiles",
                "@type": "Offer",
                "price": "309.00"
            },
            "image": "https://m.media-amazon.com/images/I/41b42TMM3SL._SX342_SY445_.jpg",
            "@type": "Product",
            "name": "Buy Amazon Brand - Eono Reusable Tote Bags | 100% Organic Cotton Shopping/Grocery Bag | Eco-Friendly Bag | Sturdy Canvas Bag with 15kgs Capacity | Pack of 2 | Life happens. Coffee helps & Salty but sweet at Amazon.in",
            "description": "Buy Amazon Brand - Eono Reusable Tote Bags | 100% Organic Cotton Shopping/Grocery Bag | Eco-Friendly Bag | Sturdy Canvas Bag with 15kgs Capacity | Pack of 2 | Life happens. Coffee helps & Salty but sweet from Travel Tote Bags at Amazon.in. 30 days free exchange or return",
            "category": "Shopping Bags & Baskets",
            "sku": "B08TTPZ6MW",
            "@context": "https://schema.org",
            "brand": "Amazon Brand – Eono",
            "url": "https://www.amazon.in/dp/B08TTPZ6MW?_encoding=UTF8&psc=1&ref_=cm_sw_r_cp_ud_dp_YS0GEB7DADQSW3JE4K0X"
        },
        "image": "https://m.media-amazon.com/images/I/41b42TMM3SL._SX342_SY445_.jpg",
        "meta": {
            "parser": "amazon",
            "modifiedDate": "2022-08-17T12:24:45.760Z",
            "createdDate": "2022-08-17T12:24:45.760Z"
        },
        "name": "Buy Amazon Brand - Eono Reusable Tote Bags | 100% Organic Cotton Shopping/Grocery Bag | Eco-Friendly Bag | Sturdy Canvas Bag with 15kgs Capacity | Pack of 2 | Life happens. Coffee helps & Salty but sweet at Amazon.in",
        "description": "Buy Amazon Brand - Eono Reusable Tote Bags | 100% Organic Cotton Shopping/Grocery Bag | Eco-Friendly Bag | Sturdy Canvas Bag with 15kgs Capacity | Pack of 2 | Life happens. Coffee helps & Salty but sweet from Travel Tote Bags at Amazon.in. 30 days free exchange or return",
        "sku": "B08TTPZ6MW",
        "url": "https://www.amazon.in/dp/B08TTPZ6MW?_encoding=UTF8&psc=1&ref_=cm_sw_r_cp_ud_dp_YS0GEB7DADQSW3JE4K0X"
    }
}
```


Alternatively, it is also possible to emulate API Gateway and Lambda locally by using `serverless-offline` plugin. In order to do that, execute the following command:

```bash
serverless plugin install -n serverless-offline
```

It will add the `serverless-offline` plugin to `devDependencies` in `package.json` file as well as will add it to `plugins` in `serverless.yml`.

After installation, you can start local emulation with:

```
serverless offline start
```

To learn more about the capabilities of `serverless-offline`, please refer to its [GitHub repository](https://github.com/dherault/serverless-offline).

#### DynamoDB Local Setup
```bash
npm install serverless-dynamodb-local 
```	

```bash
serverless dynamodb install 
# (or to use a persistent docker dynamodb instead, open a new terminal: cd ./dynamodb && docker-compose up -d)

# (below 2 commands start offline and imports schema in local dynamodb)
serverless offline start
serverless dynamodb migrate

# (below start imports schema in local dynamodb only)
serverless dynamodb start --migrate 
```

#### DynamoDB Admin

```bash
npm install -g dynamodb-admin

dynamodb-admin
```

### Open API Generation

- Convert model schema

`./node_modules/.bin/typeconv -f ts -t oapi -o spec/models --oapi-format json 'src/models/*.ts'`

- Generate schema

`serverless openapi generate -o spec/openapi.json -f json -a 3.0.3`
