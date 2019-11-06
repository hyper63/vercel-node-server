# zeit-now-node-server

An unofficial package allowing you to create Node [`http.Server`](https://nodejs.org/api/http.html#http_class_http_server) instances of your [Zeit](https://zeit.co/) [`@now/node`](https://zeit.co/docs/builders#official-builders/node-js) lambdas.

Enables you to write unit/integration tests for your lambdas, or to perform manual testing against a local server instance.

[![npm](https://img.shields.io/npm/v/zeit-now-node-server.svg?style=flat-square)](http://npm.im/zeit-now-node-server)
[![MIT License](https://img.shields.io/npm/l/zeit-now-node-server.svg?style=flat-square)](http://opensource.org/licenses/MIT)
[![Travis](https://img.shields.io/travis/ctrlplusb/zeit-now-node-server.svg?style=flat-square)](https://travis-ci.org/ctrlplusb/zeit-now-node-server)
[![Codecov](https://img.shields.io/codecov/c/github/ctrlplusb/zeit-now-node-server.svg?style=flat-square)](https://codecov.io/github/ctrlplusb/zeit-now-node-server)

## Installation

```bash
npm install zeit-now-node-server
```

## Supported API

This package has taken the code from the official [`@now/node`](https://zeit.co/docs/builders#official-builders/node-js) builder in order to ensure maximum API compatibility. As far as I am aware we have 100% API coverage.

## Unit testing your lambdas

In the below example we will make use of a local server in order to perform an integration test against our lambda.

We will be making use of the [`test-listen`](https://github.com/zeit/test-listen) package, which accepts a [`http.Server`](https://nodejs.org/api/http.html#http_class_http_server) instance and will return a unique URL based on an available port.

We will also make use of [`axios`](https://github.com/axios/axios) in order to make the request against our lambda.

```javascript
import { createServer } from 'zeit-now-node-server';
import listen from 'test-listen';
import axios from 'axios';
import helloLambda from './api/hello';

let server;
let url;

beforeAll(() => {
  server = createServer(routeUnderTest);
  url = await listen(server);
});

afterAll(() => {
  server.close();
});

it('should return the expected response' async () => {
  const response = await axios.get(url, { params: { name: 'Pearl' } });
  expect(response.data).toBe('Hello Pearl');
});
```

## Running a local server

Given the following lambda.

```javascript
const helloLambda = (req, res) => {
  res.send(`Hello ${req.query.name}`);
};
```

You can create a Node [`http.Server`](https://nodejs.org/api/http.html#http_class_http_server) instance like so.

```javascript
import { createServer } from 'zeit-node-now-server';
import helloLambda from './api/hello';

const server = createServer(helloLambda);

// start listening on port 8000
server.listen(8000);
```

Then you can then make requests against it.

```bash
> curl http://localhost:8000?name=Pearl
Hello Pearl%
```
