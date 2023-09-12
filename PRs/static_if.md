## TODO

* [Add  tests](https://github.com/Microsoft/TypeScript/blob/main/CONTRIBUTING.md#adding-a-test)
---

# Static If

In some folder <ins>ts_proj/</ins>, adding to tsconfig.json
```json
{
  "compilerOptions": {
    "preprocessorFile": "static_if.ts",
  },
}
```
then invoking
```sh
node /path_to/TypeScript/built/local/tsc.js -p /path_to/ts_proj
```
then following TS codes of static_if.ts
```ts
const FFF = false;
export const TTT = !FFF;
let abc = !FFF;

/*#static*/ if (!(TTT && !TTT || TTT)) {
  1;
} else {
  2;
  // #static
  if (FFF) 3;
  else 4;

  // Not satisfy the #Grammar, so treated as normal codes.
  // #static
  if (FFF as any) 5;

  // Not a logical expression with preprocessor names, so treated as normal codes.
  // #static
  if (FFF || abc) 6;
}
if (FFF) {}

// `ConditionalExpression` is also supported.
abc = /*#static*/ TTT ? /*#static*/ !FFF ? FFF && TTT : false : !abc;
/*#static*/ if (/*#static*/ FFF || TTT ? FFF : TTT) {}
```
emit
```js
const FFF = false;
export const TTT = !FFF;
let abc = !FFF;
/*#static*/  {
    2;
    // #static
        4;
    // Not satisfy the #Grammar, so treated as normal codes.
    // #static
    if (FFF)
        5;
    // Not a logical expression with preprocessor names, so treated as normal codes.
    // #static
    if (FFF || abc)
        6;
}
if (FFF) { }
// `ConditionalExpression` is also supported.
abc = /*#static*/ FFF && TTT;
/*#static*/ if ( /*#static*/FFF) { }
```
`FFF`, `TTT` are **preprocessor names**. They are given in `"preprocessorFile"` by top level const `VariableDeclaration`s with initializer being either `true`, `false`, or logical expressions with preprocessor names.

Then `IfStatement`s and `ConditionalExpression`s can emit one branch codes if they have
* trimed string "#static" as last preceding comment;
* the condition expression satisfy the [Grammar](#Grammar) with preprocessor names.

One important feature of this approach is, even without enabling this feature (not provide `"preprocessorFile"`, or run by deno), all codes are still valid.

## Grammar

<pre>
Whitespace:
    ch+ which `isWhiteSpaceSingleLine(ch)`
NameStart:
    [A-Za-z_]
NamePart:
    [A-Za-z_0-9]
Name:
    NameStart NamePart*
Term:
    Name
    ! Whitespace? Term
    Term Whitespace? && Whitespace? Term
    Term Whitespace? || Whitespace? Term
    ( Whitespace? Term Whitespace? )
Kind:
    Whitespace? Term Whitespace?
</pre>

---

For more applications, see [pdf.ts](https://github.com/nmtigor/pdf.ts/blob/main/src/global.ts).

Enjoy!
