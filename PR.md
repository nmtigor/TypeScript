## TODO

* [Add  tests](https://github.com/Microsoft/TypeScript/blob/main/CONTRIBUTING.md#adding-a-test)
---

# Preprocessor Comments

In some folder `ts_proj/`, adding to `tsconfig.json` 
```json
{
  "compilerOptions": {
    "preprocessor": ["DEBUG_123"],
  },
}
```
then invoking
```sh
node /path_to/TypeScript/built/local/tsc.js -p /path_to/ts_proj
```
then following TS codes
```ts
// #if DEBUG_123
  var dump = "// #endif";
// #else
  var dump = true;
// #endif
```
```ts
// #if !DEBUG_123
  var dump = true;
// #else
  var dump = "// #endif";
// #endif
```
```ts
// #if DEBUG_123
  var dump = "// #endif";
// #else
  // #if DEBUG_456
    var dump = 123;
  // #else
    var dump = true;
  // #endif
  dump = "ops";
// #endif
```
```ts
// #if !DEBUG_123
  // #if DEBUG_456
    var dump = true;
  // #else
    var dump = "// #endif";
  // #endif
  dump = "ops";
// #else
  var dump = 123;
// #endif
```
```ts
// #if DEBUG_123
  // #if DEBUG_456
    var dump = true;
  // #else
    var dump = "// #endif";
  // #endif
// #endif
```
all emit
```js
var dump = "// #endif";
```
---

Logical operators `!`, `&&`, `||` are suported. So following codes

```ts
/* #if A || B && !C */
```
```ts
/*#if((A||B)&&!C)*/
```
are valid preprocessor comments.

## Grammar

<pre>
TermDirective:
    #if
NoTermDirective:
    #else
    #endif
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
    TermDirective Whitespace Term
    TermDirective ( Whitespace? Term Whitespace? )
    NoTermDirective
PreprocessorComment:
    /* Whitespace? Kind Whitespace? */
    // Whitespace? Kind Whitespace? EndOfLine
</pre>
---

## Further Development

Backticks (``` ` ```) strings are currently not supported. So following codes
```ts
// #if !A
  var dump = 'abc';
// #else
  var dump = `/* #endif */`;
/* #endif */
```
emit (with "error TS1160: Unterminated template literal.")
```js
var dump = 'abc';
";\n/* #endif */\n";
```
But
```ts
// #if !A
  var dump = 'abc';
// #else
  var dump = `// #endif`;
/* #endif */
```
emit correctly
```js
var dump = 'abc';
```
because ``` // #endif`; ``` is not a valid preprocessor comment, so treated as normal codes.

So, although it's really rare, **don't let valid preprocessor comments appear in backticks strings in excluded codes!** If you have to, use string template instead:
```ts
// #if !A
  var dump = 'abc';
// #else
  var dump = `${'/* #endif */'}`;
/* #endif */
```
which emit correctly
```js
var dump = 'abc';

```

Enjoy!

