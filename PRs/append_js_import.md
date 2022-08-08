## TODO

* [Add  tests](https://github.com/Microsoft/TypeScript/blob/main/CONTRIBUTING.md#adding-a-test)
---

# Append js in import

I removed (with "replace all") ".js" of module specifiers in import declaraions. But quickly found that ".js"s were not emitted in transpiled js-files. Unfortunately, adding ".js" back in ts-files manually is a nightmare for large projects. According to [this](https://github.com/microsoft/TypeScript/issues/16577#issuecomment-754941937), official project will not add this feature. So I did it by myself.

In some folder `ts_proj/`, adding to `tsconfig.json` 
```jsonc
{
  "compilerOptions": {
    "append_js_import": "js", # Valid strings are "js", "cjs", "mjs"
  },
}
```
then invoking
```sh
node /path_to/TypeScript/built/local/tsc.js -p /path_to/ts_proj
```
then following TS codes
```ts
import { blah } from /* abcd */ "hello" /* blah */; // xyz
import { blah_1 } from "hello_1.js";
```
emit
```js
import { blah } from /* abcd */ "hello.js" /* blah */; // xyz
import { blah_1 } from "hello_1.js";
```

Enjoy!

