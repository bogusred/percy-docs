# JavaScript API client
#### [percy-js on GitHub <i class="fa fa-github" aria-hidden="true"></i>](https://github.com/percy/percy-js)

[![Build Status](https://travis-ci.org/percy/percy-js.svg?branch=master)](https://travis-ci.org/percy/percy-js)
[![Package Status](https://img.shields.io/npm/v/percy-client.svg)](https://www.npmjs.com/package/percy-client)

JavaScript API client library for [Percy](https://percy.io).

<div class="Alert Alert--warning">
  <strong>NOTE:</strong> This is a low-level API library, you probably want to use a higher-level framework plugin. Check out:

* [ember-percy](/docs/clients/javascript/ember)

  We are looking for sponsorship for developing wrappers for Angular, React, and other JavaScript frameworks. If you are interested in this please [reach out](mailto:hello@percy.io)!
</div>

## How to use

1. You'll need to setup a node based test runner such as Protractor or Karma. You can use your existing end to end test setup if you have one.
1. During the setup phase, before the tests begin executing you need to:
  1. create an instance of `PercyClient`.
  1. create a manifest of all of your assets (css, js, images, svgs).
  1. construct a build promise
1. In each test case, such as a jasmine `it()` block, you'll need to get the string representation of the dom and create a resource with `PercyClient` `makeResource` method.
1. After tests are finished executing, you'll need to finalize the build.
  1. Resolve all remaining promise collections in order:
    - build promise
    - all snapshot promises
    - `PercyClient` `finalizeBuild`

## Documentation

### PercyClient

#### Constructor Usage

```javascript

var percyClient;

function setup() {
  percyClient = new PercyClient({
    // This is your unique token set in your CI environment.
    token: process.env.PERCY_TOKEN,
    // This is your project name set in your CI environment.
    // E.g. 'your-company/your-project'
    clientInfo: process.env.PERCY_PROJECT,
    environmentInfo: ''
  });
}


```

##### Arguments:
| Param| Type | Details |
| options | `{token: string, clientInfo: string, environmentInfo: string, environment?: Environment, apiUrl?: string}` | 


#### Methods

##### `createBuild`

###### Returns

`Promise<{body: {data: {id: number }}}>`




##### `finalizeBuild`

###### Arguments

| Param| Type | Details |
| buildId | `number` | The unique id which is returned in the promise response of the `createBuild` method. E.g. `response.body.data.id`

###### Returns
`Promise<>`

### Environment
Grabs environment information such as git branch, git commit, and `PERCY_PROJECT` through node's `process.env` object.

#### Constructor Usage
You shouldn't have to create this yourself because one will be created automatically when you create `PercyClient` but it might be helpful if you need to manually pass in environment variables or mock `process.env`.

```
const Environment = require('percy-client/dist/environment');

new Environment(process.env);
```


## Contributing

1.  Fork it ([https://github.com/percy/percy-js/fork](https://github.com/percy/percy-js/fork))
2.  Create your feature branch (`git checkout -b my-new-feature`)
3.  Commit your changes (`git commit -am 'Add some feature'`)
4.  Push to the branch (`git push origin my-new-feature`)
5.  Create a new Pull Request

### Running Tests

* `npm run test`
* `npm run tdd`
