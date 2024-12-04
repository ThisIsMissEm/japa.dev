---
title: OpenAPI Assertions
description: OpenAPI Assertions plugin is used to validate responses against an OpenAPI specification.
ogImage: openapi-assertions-plugin.jpeg
---

# OpenAPI Assertions

Using this plugin, you can test HTTP responses against one or more open API schemas. Just make sure to register the path to schema files when using the plugin.

## Setup

Install the package from the npm registry as follows.

```sh
npm i -D @japa/api-client@1.4.4

# yarn
yarn add -D @japa/api-client@1.4.4
```

And register it as a plugin within the `bin/test.js` file.

:::languageSwitcher

```ts
// title: ESM
import { assert } from '@japa/assert'
// highlight-start
import { openapi } from '@japa/openapi-assertions'
// highlight-end
import { configure, processCliArgs } from '@japa/runner'

configure({
  ...processCliArgs(process.argv.slice(2)),
  ...{
    files: ['tests/**/*.spec.js'],
    plugins: [
      assert(),
      // highlight-start
      openapi({
        schemas: [new URL('../api-spec.json', import.meta.url)]
      })
      // highlight-end
    ]
  }
})
```

```ts
// title: CommonJS
const { assert } = require('@japa/assert')
// highlight-start
const { openapi } = require('@japa/openapi-assertions')
const path = require('node:path')
// highlight-end
const { configure, processCliArgs } = require('@japa/runner')

configure({
  ...processCliArgs(process.argv.slice(2)),
  ...{
    files: ['tests/**/*.spec.js'],
    plugins: [
      assert(),
      // highlight-start
      openapi({
        schemas: [path.resolve(__dirname, '../api-spec.json')]
      })
      // highlight-end
    ]
  }
})
```
:::

## Testing API Responses

You can test response objects from `axios`, `superagent`, `supertest`, `request`, and `light-my-request` libraries using the following method.

```ts
test('get /users', async ({ openapi }) => {
  const response = await supertest(baseUrl).get('/')
  openapi.isValidResponse(response)
})
```

The response is validated as follows:

- An error is raised if the request path is not documented in the API schema file.
- An error is raised if the response status code is not documented in the API schema file.
- An error is raised if the body properties are not the same as expected by the schema. Use the `required` array to validate required response properties.
- The response status code is used to find the body schema for validation.
- Response headers are also validated against the expected headers mentioned inside the API schema file.

## Migrating from `@japa/assert` v3.0.0

To migrate from the OpenAPI testing in `@japa/assert` version 3.0.0, you need to configure the plugin as above, and then change from `assert.isValidApiResponse` to `openapi.isValidResponse`:

```ts
// title: Old Syntax
test('get /users', async ({ assert }) => {
  const response = await supertest(baseUrl).get('/')
  assert.isValidApiResponse(response)
})
```

```ts
// title: New Syntax
test('get /users', async ({ openapi }) => {
  const response = await supertest(baseUrl).get('/')
  openapi.isValidResponse(response)
})
```
