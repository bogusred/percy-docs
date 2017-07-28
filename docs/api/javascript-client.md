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
  1. create a manifest of all of your assets (css, js, images, svgs) using `makeResource` for each asset.
  1. construct a build promise
1. In each test case, such as a jasmine `it()` block, you'll need to get the string representation of the dom and create a resource with `PercyClient` `makeResource` method.
1. After tests are finished executing, you'll need to finalize the build.
  1. Resolve all remaining promise collections in order:
    - build promise
    - all snapshot promises
    - `PercyClient` `finalizeBuild`

## Documentation

### `PercyClient`

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
|---|---|---|
| options | `{token: string, clientInfo: string, environmentInfo: string, environment?: Environment, apiUrl?: string}` | 


#### Methods

##### `makeResource`

```javascript

var percyClient = new PercyClient({...});

var resourceManifest = {};
var resource = percyClient.makeResource({
  resourceUrl: '...', // E.g. '/css/main.min.css'
  sha: '...', // Unique sha created from the contents of the file.
  localPath: '...' // Full path to the file on this machine.
});

```

###### Arguments

| Param| Type | Details |
|---|---|---|
| resourceObj | `{resourceUrl: string, sha: string, localPath: string}` | This object tells percy's api if the asset is one it already has or not based on the sha created from the file contents. `resourceUrl` is used to specify the url at which it will be accessed by your app.



##### `createBuild`

###### Arguments

| Param| Type | Details |
|---|---|---|
| repo | `string` | The `repo` property of your instance of `Environment` or the `PERCY_PROJECT`.
| buildObj | `{resources: Array<Resource>}` | An object containing an array of your resource objects.

###### Returns

```
Promise<{
  body: {
    data: {id: number },
    relationships: {
      'missing-resources': {
        data: Array<{id: number}>
      }
    }
  }
}>
```

The `missing-resources.data` property contains an array of objects with an `id` property matching the unique asset build id. You can then use this to look up the Resource object in the resource manifest you created at the beginning.


##### `uploadResource`

```javascript

var percyClient = new PercyClient({...});

percyClient.uploadResource(buildId, content)
    .then(() => {
      // After we're sure all build resources are uploaded,
      // finalize the snapshot.
      // ...
    });

```

###### Arguments

| Param| Type | Details |
|---|---|---|
| buildId | `number` | The unique id which is returned in the promise response of the `createBuild` method. E.g. `response.body.data.id`.


###### Returns
`Promise<>`

##### `finalizeBuild`

###### Arguments

| Param| Type | Details |
|---|---|---|
| buildId | `number` | The unique id which is returned in the promise response of the `createBuild` method. E.g. `response.body.data.id`.

###### Returns
`Promise<>`

### `Environment`
Grabs environment information such as git branch, git commit, and `PERCY_PROJECT` through node's `process.env` object.

#### Constructor Usage
You may not need to create one of these because one will be created automatically when you create `PercyClient` but it might be helpful if you need to manually pass in environment variables or mock `process.env`.

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
