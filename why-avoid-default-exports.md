# Why avoid using default exports

## Stories

### Terser story

Ref https://twitter.com/swyx/status/1091959223406084096


terser-webpack-plugin used terser as default export.

```
import terser from 'terser';

// transpiled
var _terser = _interopRequireDefault(require("terser"));

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }
```

Terser used `exports` only as a namespace. So it's normal to consume named imports in esm.

```
(function(exports) {
    function minify() {}
    exports["minify"] = minify;
})("undefined" != typeof module ? module.exports : Terser = {});
```
Then terser migrated to rollup which added `esModule` property to exports namespace.

```
(function (global, factory) {
  typeof exports === 'object' && typeof module !== 'undefined' ? factory(exports, require('source-map')) :
  typeof define === 'function' && define.amd ? define(['exports', 'source-map'], factory) :
  (global = global || self, factory(global.Terser = {}, global.sourceMap));
}(this, function (exports, MOZ_SourceMap) { 'use strict';
  function minify () {}
  exports.minify = minify;
  Object.defineProperty(exports, '__esModule', { value: true });
}));
```

Now babel interop trusts that imported object is a namespace with named and default exports as properties. This broke the web. This is where default exports overusing let us down.

## Related

[Why I've stopped exporting defaults from my JavaScript modules by Nicholas C. Zakas](https://humanwhocodes.com/blog/2019/01/stop-using-default-exports-javascript-module/)
