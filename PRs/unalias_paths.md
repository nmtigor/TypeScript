## TODO

* [Add  tests](https://github.com/Microsoft/TypeScript/blob/main/CONTRIBUTING.md#adding-a-test)
---

# Unalias import paths

By TSConfig Reference, a ["paths"](https://www.typescriptlang.org/tsconfig#paths) map could be setup to change imports lookup locations. But the imports module specifiers in the emitted js file does not change. I add a boolean option "unaliasImportPaths" to tackle this inconvenience:

In some folder <ins>ts_proj/</ins>, adding to tsconfig.json 
```jsonc
{
  "compilerOptions": {
    "paths": {
      "@abc/*": ["./*"]
    },

    // Default is false.
    "unaliasImportPaths": true,
  },
}
```
then invoking
```bash
node /path_to/TypeScript/built/local/tsc.js -p /path_to/ts_proj
```
then following TS codes
```ts
import { Blah } from "@abc/hello.js";
Blah.toUpperCase();

(await import(`@abc/hello_1.js`)).blah_1.toUpperCase();
```
emit
```js
import { Blah } from "./hello.js";
Blah.toUpperCase();
(await import(`./hello_1.js`)).blah_1.toUpperCase();
```

Enjoy!