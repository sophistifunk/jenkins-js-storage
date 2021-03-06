Jenkins client-side storage JavaScript API, providing a simple API on top of the HTML5 [Storage] (Web Storage API).

HTML5 [Storage] is great for storing per-domain/session name/value pairs ([localStorage]/[sessionStorage]), but it's a totally flat and untyped structure. The lack of hierarchy/namespacing makes it difficult to perform operations on subsets of data stored in [localStorage] e.g. to clear out (invalidate) a subset of [localStorage] NVPs when a new instance of Jenkins is installed, or to group a subset of NVPs and have APIs that allow the client code to just navigate those properties only e.g. log categories.

This API attempts to do the following: 

1. Namespace the [Storage] so that we can easily perform operations on a subset of NVPs (or on a subset of a subset etc).
1. Support more than just `string` values e.g. `boolean`, `number` and `object`. Not `function` though, obviously.
1. Support subspaces i.e. namespaces inside namespaces e.g. you could have the "log-gategory" namespace inside the "jenkins-instance" namespace i.e. "jenkins-instance/log-gategory". Another one might be "jenkins-instance/classes-info" for storing classes metadata Vs the client UI contantly firing REST API calls to get it (see JENKINS-40080).
1. Support `get` fallback options, whereby you can configure the `get` to look in the parent namespaces, or if using a dot separated key in the name (the N part of the NVP) that it can be configured to check back along the "dot parent path" e.g. if "org.jenkins.a.b" has no value, then fallback and check "org.jenkins.a" etc. This would be useful for e.g. log categories.

## jenkinsNamespace <span style="opacity: 0.6;">(StorageNamespace)</span>

The API offers a few top-level utility functions, but the one of most interest to Jenkins UI code is [`jenkinsNamespace`](./global.html#jenkinsNamespace),
which returns an instance of the [StorageNamespace] class, in which all Jenkins "instance" specific data should be stored.

__Examples__:

```javascript
// Store some info in the Jenkins namespace.
const storage = require('@jenkins-cd/storage');
const jenkinsNS = storage.jenkinsNamespace();
jenkinsNS.set('version', versionString);
jenkinsNS.set('plugins', pluginsArray);
```

```javascript
// After detecting that the Jenkins instance version has changed, or
// active plugins have changed etc, lets clear the "jenkins-instance"
// namespace.
const storage = require('@jenkins-cd/storage');
const jenkinsNS = storage.jenkinsNamespace();
const lastVersion = jenkinsNS.get('version');

if (lastVersion !== version) {
    jenkinsNS.clear(); // Clear all NVPs in the Jenkins namespace.
    jenkinsNS.set('version', versionString);
}
```

See the [`jenkinsNamespace`](./global.html#jenkinsNamespace) and [StorageNamespace] docs for more details and examples.

<a href="https://github.com/tfennelly/jenkins-js-storage" target="_blank"><img style="position: absolute; top: 0; right: 0; border: 0;" src="https://camo.githubusercontent.com/365986a132ccd6a44c23a9169022c0b5c890c387/68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f6769746875622f726962626f6e732f666f726b6d655f72696768745f7265645f6161303030302e706e67" alt="Fork me on GitHub" data-canonical-src="https://s3.amazonaws.com/github/ribbons/forkme_right_red_aa0000.png"></a>

[Storage]: https://developer.mozilla.org/en-US/docs/Web/API/Storage
[localStorage]: https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage
[sessionStorage]: https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage
[StorageNamespace]: ./StorageNamespace.html

