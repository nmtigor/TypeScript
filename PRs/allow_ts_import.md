## TODO

* [Add  tests](https://github.com/Microsoft/TypeScript/blob/main/CONTRIBUTING.md#adding-a-test)
---

# Allow ts in import

In order to use Deno, and motivated by [#35148](https://github.com/microsoft/TypeScript/pull/35148), [#27481](https://github.com/microsoft/TypeScript/issues/27481), [#38149](https://github.com/microsoft/TypeScript/issues/38149), [#37582](https://github.com/microsoft/TypeScript/issues/37582), ..., I implemented the feature.

In some folder <ins>ts_proj/</ins>, adding to tsconfig.json 
```jsonc
{
  "compilerOptions": {
    // If true, ts -> js, mts -> mjs, cts -> cjs. Default is false.
    "allowTsImport": true,
  },
}
```
then invoking
```bash
node /path_to/TypeScript/built/local/tsc.js -p /path_to/ts_proj
```
then following TS codes
```ts
import { Blah } from "hello.ts";
Blah.toUpperCase();
export const { blah_1 } = await import(`hello_1.ts`);
const blah_2 = "Blah" as import("./hello.ts").Blah;
```
emit
```js
import { Blah } from "hello.js";
Blah.toUpperCase();
export const { blah_1 } = await import(`hello_1.js`);
const blah_2 = "Blah";
```

Enjoy!
