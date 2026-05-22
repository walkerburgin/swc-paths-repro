# `swc-paths-repro`

```
swc-paths-repro
├── apis
│   └── auth.ts
├── bazel-out
│   ├── apis -> ../apis
│   └── foo-app
│       ├── src
│       │   └── app.ts -> ../../../foo-app/src/app.ts
│       └── swcrc.json
├── foo-app
│   └── src
│       └── app.ts
├── package.json
└── pnpm-lock.yaml
```

## Repro

Run `pnpm repro`:

```
➜  swc-paths-repro git:(master) pnpm repro

> @ repro /Users/wburgin/Repositories/walkerburgin/swc-paths-repro
> swc --config-file bazel-out/foo-app/swcrc.json bazel-out/foo-app/src/app.ts

Successfully compiled 1 file with swc.
"use strict";
Object.defineProperty(exports, "__esModule", {
    value: true
});
Object.defineProperty(exports, "greet", {
    enumerable: true,
    get: function() {
        return greet;
    }
});
var _auth = require("../../../../apis/auth");
var greet = function greet() {
    return (0, _auth.hello)();
};
```

The issue is that `require("../../../../apis/auth")` is not the correct relative path.


If you run `pnpm repro:absolute` to make the input file an absolute path:

```
➜  swc-paths-repro git:(master) pnpm repro:absolute

> @ repro:absolute /Users/wburgin/Repositories/walkerburgin/swc-paths-repro
> swc --config-file bazel-out/foo-app/swcrc.json $PWD/bazel-out/foo-app/src/app.ts

Successfully compiled 1 file with swc.
"use strict";
Object.defineProperty(exports, "__esModule", {
    value: true
});
Object.defineProperty(exports, "greet", {
    enumerable: true,
    get: function() {
        return greet;
    }
});
var _auth = require("../../apis/auth");
var greet = function greet() {
    return (0, _auth.hello)();
};
```

We get the correct relative path: `require("../../apis/auth")`.
