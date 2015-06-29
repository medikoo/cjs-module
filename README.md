# cjs-module
##  CJS (Node.js) modules resolver

Environment agnostic CJS (Node.js) modules resolver.  
It implements a _strict_ version of Node.js modules resolution logic, differences are as follows:

- [Loading from global folders](https://nodejs.org/api/all.html#all_loading_from_the_global_folders) is not supported
- Only Unix path separators (`/`) are supported in require's _path_ arguments
- There's no awareness of node.js [core modules](https://nodejs.org/api/all.html#all_core_modules)
  e.g. `resolve(dir, 'fs')` will naturally result with _null_

### Installation

	$ npm install cjs-module

### API
#### getResolver(extensions, confirmFile, resolvePackageMain)

For provided configuration, returns a CJS modules resolver:
- __extensions__ - List of supported file extensions in order of singificance, e.g. for Node.js it would be `['.js', '.json', '.node']`
- __confirmFile__ - a `confirmFile(filepath)` function. Confirms whether there's a module at provided (not normalized, absolute) file path. Returns promise-like object which resolves with either normalized full path of a module or null (if there's no module for given path).  
Although result is expected to be a promise-like object, resolution can be synchronous.
- __resolvePackageMain__ - a `resolvePackageMain(dirpath)` function. Returns value of _package.json_'s `main` property for given path. Returns promise-like object which resolves with either resolved value, or null, when either `package.json` file was not found, or it didn't have _main_ property.  
Same as with `confirmFile` resolution can be synchronous.

#### resolve(dir, path)

_Node.js resolver_

Asynchronously resolves module path against provided directory path.
Returns promise. If no matching module was found, promise resolves with `null` otherwise
full module path becomes a resolved value.

```javascript
var resolve = require('cjs-module/resolve')

// Asynchronously resolve path for 'foo' module against current path
resolve(__dirname, 'foo').done(function (fooModulePath) {
  if (!fooModulePath) {
		// 'foo' module doesn't exist
	} else {
		// 'foo' module found at fooModulePath
	}
})
```

#### resolveSync(dir, path)

_Node.js resolver_

Synchronously resolves module path against provided directory path.
If matching module was found then full module path is returned, otherwise `null`.

```javascript
var resolveSync = require('cjs-module/resolve/sync')

// Synchronously resolve path for 'foo' module against current path
var fooModulePath = resolveSync(__dirname, 'foo');
if (!fooModulePath) {
	// 'foo' module doesn't exist
} else {
	// 'foo' module found
}
```

## Tests [![Build Status](https://travis-ci.org/medikoo/cjs-module.svg)](https://travis-ci.org/medikoo/cjs-module)

	$ npm test
