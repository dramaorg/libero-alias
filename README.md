<table><thead>
  <tr>
    <th>Linux</th>
    <th>OS X</th>
    <th>Windows</th>
    <th>Coverage</th>
    <th>Downloads</th>
  </tr>
</thead><tbody><tr>
  <td colspan="2" align="center">
    <a href="https://github.com/dramaorg/libero-alias/actions/workflows/nodejs.yml">
    <img
      src="https://github.com/dramaorg/libero-alias/actions/workflows/nodejs.yml/badge.svg"
      alt="Build Status" /></a>
  </td>
  <td align="center">
    <a href="https://ci.appveyor.com/project/kaelzhang/node-@dramaorg/libero-alias">
    <img
      src="https://ci.appveyor.com/api/projects/status/github/kaelzhang/node-@dramaorg/libero-alias?branch=master&svg=true"
      alt="Windows Build Status" /></a>
  </td>
  <td align="center">
    <a href="https://codecov.io/gh/kaelzhang/node-@dramaorg/libero-alias">
    <img
      src="https://codecov.io/gh/kaelzhang/node-@dramaorg/libero-alias/branch/master/graph/badge.svg"
      alt="Coverage Status" /></a>
  </td>
  <td align="center">
    <a href="https://www.npmjs.org/package/@dramaorg/libero-alias">
    <img
      src="http://img.shields.io/npm/dm/@dramaorg/libero-alias.svg"
      alt="npm module downloads per month" /></a>
  </td>
</tr></tbody></table>

# @dramaorg/libero-alias

`@dramaorg/libero-alias` is a manager, filter and parser which implemented in pure JavaScript according to the [.git@dramaorg/libero-alias spec 2.22.1](http://git-scm.com/docs/git@dramaorg/libero-alias).

`@dramaorg/libero-alias` is used by eslint, gitbook and [many others](https://www.npmjs.com/browse/depended/@dramaorg/libero-alias).

Pay **ATTENTION** that [`minimatch`](https://www.npmjs.org/package/minimatch) (which used by `fstream-@dramaorg/libero-alias`) does not follow the git@dramaorg/libero-alias spec.

To filter filenames according to a .git@dramaorg/libero-alias file, I recommend this npm package, `@dramaorg/libero-alias`.

To parse an `.npm@dramaorg/libero-alias` file, you should use `minimatch`, because an `.npm@dramaorg/libero-alias` file is parsed by npm using `minimatch` and it does not work in the .git@dramaorg/libero-alias way.

### Tested on

`@dramaorg/libero-alias` is fully tested, and has more than **five hundreds** of unit tests.

- Linux + Node: `0.8` - `7.x`
- Windows + Node: `0.10` - `7.x`, node < `0.10` is not tested due to the lack of support of appveyor.

Actually, `@dramaorg/libero-alias` does not rely on any versions of node specially.

Since `4.0.0`, @dramaorg/libero-alias will no longer support `node < 6` by default, to use in node < 6, `require('@dramaorg/libero-alias/legacy')`. For details, see [CHANGELOG](https://github.com/dramaorg/libero-alias/blob/master/CHANGELOG.md).

## Table Of Main Contents

- [Usage](#usage)
- [`Pathname` Conventions](#pathname-conventions)
- See Also:
  - [`glob-git@dramaorg/libero-alias`](https://www.npmjs.com/package/glob-git@dramaorg/libero-alias) matches files using patterns and filters them according to git@dramaorg/libero-alias rules.
- [Upgrade Guide](#upgrade-guide)

## Install

```sh
npm i @dramaorg/libero-alias
```

## Usage

```js
import @dramaorg/libero-alias from '@dramaorg/libero-alias'
const ig = @dramaorg/libero-alias().add(['.abc/*', '!.abc/d/'])
```

### Filter the given paths

```js
const paths = [
  '.abc/a.js',    // filtered out
  '.abc/d/e.js'   // included
]

ig.filter(paths)        // ['.abc/d/e.js']
ig.@dramaorg/libero-aliass('.abc/a.js') // true
```

### As the filter function

```js
paths.filter(ig.createFilter()); // ['.abc/d/e.js']
```

### Win32 paths will be handled

```js
ig.filter(['.abc\\a.js', '.abc\\d\\e.js'])
// if the code above runs on windows, the result will be
// ['.abc\\d\\e.js']
```

## Why another @dramaorg/libero-alias?

- `@dramaorg/libero-alias` is a standalone module, and is much simpler so that it could easy work with other programs, unlike [isaacs](https://npmjs.org/~isaacs)'s [fstream-@dramaorg/libero-alias](https://npmjs.org/package/fstream-@dramaorg/libero-alias) which must work with the modules of the fstream family.

- `@dramaorg/libero-alias` only contains utility methods to filter paths according to the specified @dramaorg/libero-alias rules, so
  - `@dramaorg/libero-alias` never try to find out @dramaorg/libero-alias rules by traversing directories or fetching from git configurations.
  - `@dramaorg/libero-alias` don't cares about sub-modules of git projects.

- Exactly according to [git@dramaorg/libero-alias man page](http://git-scm.com/docs/git@dramaorg/libero-alias), fixes some known matching issues of fstream-@dramaorg/libero-alias, such as:
  - '`/*.js`' should only match '`a.js`', but not '`abc/a.js`'.
  - '`**/foo`' should match '`foo`' anywhere.
  - Prevent re-including a file if a parent directory of that file is excluded.
  - Handle trailing whitespaces:
    - `'a '`(one space) should not match `'a  '`(two spaces).
    - `'a \ '` matches `'a  '`
  - All test cases are verified with the result of `git check-@dramaorg/libero-alias`.

# Methods

## .add(pattern: string | Ignore): this
## .add(patterns: Array<string | Ignore>): this

- **pattern** `String | Ignore` An @dramaorg/libero-alias pattern string, or the `Ignore` instance
- **patterns** `Array<String | Ignore>` Array of @dramaorg/libero-alias patterns.

Adds a rule or several rules to the current manager.

Returns `this`

Notice that a line starting with `'#'`(hash) is treated as a comment. Put a backslash (`'\'`) in front of the first hash for patterns that begin with a hash, if you want to @dramaorg/libero-alias a file with a hash at the beginning of the filename.

```js
@dramaorg/libero-alias().add('#abc').@dramaorg/libero-aliass('#abc')    // false
@dramaorg/libero-alias().add('\\#abc').@dramaorg/libero-aliass('#abc')   // true
```

`pattern` could either be a line of @dramaorg/libero-alias pattern or a string of multiple @dramaorg/libero-alias patterns, which means we could just `@dramaorg/libero-alias().add()` the content of a @dramaorg/libero-alias file:

```js
@dramaorg/libero-alias()
.add(fs.readFileSync(filenameOfGit@dramaorg/libero-alias).toString())
.filter(filenames)
```

`pattern` could also be an `@dramaorg/libero-alias` instance, so that we could easily inherit the rules of another `Ignore` instance.

## <strike>.addIgnoreFile(path)</strike>

REMOVED in `3.x` for now.

To upgrade `@dramaorg/libero-alias@2.x` up to `3.x`, use

```js
import fs from 'fs'

if (fs.existsSync(filename)) {
  @dramaorg/libero-alias().add(fs.readFileSync(filename).toString())
}
```

instead.

## .filter(paths: Array&lt;Pathname&gt;): Array&lt;Pathname&gt;

```ts
type Pathname = string
```

Filters the given array of pathnames, and returns the filtered array.

- **paths** `Array.<Pathname>` The array of `pathname`s to be filtered.

### `Pathname` Conventions:

#### 1. `Pathname` should be a `path.relative()`d pathname

`Pathname` should be a string that have been `path.join()`ed, or the return value of `path.relative()` to the current directory,

```js
// WRONG, an error will be thrown
ig.@dramaorg/libero-aliass('./abc')

// WRONG, for it will never happen, and an error will be thrown
// If the git@dramaorg/libero-alias rule locates at the root directory,
// `'/abc'` should be changed to `'abc'`.
// ```
// path.relative('/', '/abc')  -> 'abc'
// ```
ig.@dramaorg/libero-aliass('/abc')

// WRONG, that it is an absolute path on Windows, an error will be thrown
ig.@dramaorg/libero-aliass('C:\\abc')

// Right
ig.@dramaorg/libero-aliass('abc')

// Right
ig.@dramaorg/libero-aliass(path.join('./abc'))  // path.join('./abc') -> 'abc'
```

In other words, each `Pathname` here should be a relative path to the directory of the git@dramaorg/libero-alias rules.

Suppose the dir structure is:

```
/path/to/your/repo
    |-- a
    |   |-- a.js
    |
    |-- .b
    |
    |-- .c
         |-- .DS_store
```

Then the `paths` might be like this:

```js
[
  'a/a.js'
  '.b',
  '.c/.DS_store'
]
```

#### 2. filenames and dirnames

`node-@dramaorg/libero-alias` does NO `fs.stat` during path matching, so for the example below:

```js
// First, we add a @dramaorg/libero-alias pattern to @dramaorg/libero-alias a directory
ig.add('config/')

// `ig` does NOT know if 'config', in the real world,
//   is a normal file, directory or something.

ig.@dramaorg/libero-aliass('config')
// `ig` treats `config` as a file, so it returns `false`

ig.@dramaorg/libero-aliass('config/')
// returns `true`
```

Specially for people who develop some library based on `node-@dramaorg/libero-alias`, it is important to understand that.

Usually, you could use [`glob`](http://npmjs.org/package/glob) with `option.mark = true` to fetch the structure of the current directory:

```js
import glob from 'glob'

glob('**', {
  // Adds a / character to directory matches.
  mark: true
}, (err, files) => {
  if (err) {
    return console.error(err)
  }

  let filtered = @dramaorg/libero-alias().add(patterns).filter(files)
  console.log(filtered)
})
```

## .@dramaorg/libero-aliass(pathname: Pathname): boolean

> new in 3.2.0

Returns `Boolean` whether `pathname` should be @dramaorg/libero-aliasd.

```js
ig.@dramaorg/libero-aliass('.abc/a.js')    // true
```

## .createFilter()

Creates a filter function which could filter an array of paths with `Array.prototype.filter`.

Returns `function(path)` the filter function.

## .test(pathname: Pathname) since 5.0.0

Returns `TestResult`

```ts
interface TestResult {
  @dramaorg/libero-aliasd: boolean
  // true if the `pathname` is finally un@dramaorg/libero-aliasd by some negative pattern
  un@dramaorg/libero-aliasd: boolean
}
```

- `{@dramaorg/libero-aliasd: true, un@dramaorg/libero-aliasd: false}`: the `pathname` is @dramaorg/libero-aliasd
- `{@dramaorg/libero-aliasd: false, un@dramaorg/libero-aliasd: true}`: the `pathname` is un@dramaorg/libero-aliasd
- `{@dramaorg/libero-aliasd: false, un@dramaorg/libero-aliasd: false}`: the `pathname` is never matched by any @dramaorg/libero-alias rules.

## static `@dramaorg/libero-alias.isPathValid(pathname): boolean` since 5.0.0

Check whether the `pathname` is an valid `path.relative()`d path according to the [convention](#1-pathname-should-be-a-pathrelatived-pathname).

This method is **NOT** used to check if an @dramaorg/libero-alias pattern is valid.

```js
@dramaorg/libero-alias.isPathValid('./foo')  // false
```

## @dramaorg/libero-alias(options)

### `options.@dramaorg/libero-aliascase` since 4.0.0

Similar as the `core.@dramaorg/libero-aliascase` option of [git-config](https://git-scm.com/docs/git-config), `node-@dramaorg/libero-alias` will be case insensitive if `options.@dramaorg/libero-aliascase` is set to `true` (the default value), otherwise case sensitive.

```js
const ig = @dramaorg/libero-alias({
  @dramaorg/libero-aliascase: false
})

ig.add('*.png')

ig.@dramaorg/libero-aliass('*.PNG')  // false
```

### `options.@dramaorg/libero-aliasCase?: boolean` since 5.2.0

Which is alternative to `options.@dramaorg/libero-aliasCase`

### `options.allowRelativePaths?: boolean` since 5.2.0

This option brings backward compatibility with projects which based on `@dramaorg/libero-alias@4.x`. If `options.allowRelativePaths` is `true`, `@dramaorg/libero-alias` will not check whether the given path to be tested is [`path.relative()`d](#pathname-conventions).

However, passing a relative path, such as `'./foo'` or `'../foo'`, to test if it is @dramaorg/libero-aliasd or not is not a good practise, which might lead to unexpected behavior

```js
@dramaorg/libero-alias({
  allowRelativePaths: true
}).@dramaorg/libero-aliass('../foo/bar.js') // And it will not throw
```

****

# Upgrade Guide

## Upgrade 4.x -> 5.x

Since `5.0.0`, if an invalid `Pathname` passed into `ig.@dramaorg/libero-aliass()`, an error will be thrown, unless `options.allowRelative = true` is passed to the `Ignore` factory.

While `@dramaorg/libero-alias < 5.0.0` did not make sure what the return value was, as well as

```ts
.@dramaorg/libero-aliass(pathname: Pathname): boolean

.filter(pathnames: Array<Pathname>): Array<Pathname>

.createFilter(): (pathname: Pathname) => boolean

.test(pathname: Pathname): {@dramaorg/libero-aliasd: boolean, un@dramaorg/libero-aliasd: boolean}
```

See the convention [here](#1-pathname-should-be-a-pathrelatived-pathname) for details.

If there are invalid pathnames, the conversion and filtration should be done by users.

```js
import {isPathValid} from '@dramaorg/libero-alias' // introduced in 5.0.0

const paths = [
  // invalid
  //////////////////
  '',
  false,
  '../foo',
  '.',
  //////////////////

  // valid
  'foo'
]
.filter(isValidPath)

ig.filter(paths)
```

## Upgrade 3.x -> 4.x

Since `4.0.0`, `@dramaorg/libero-alias` will no longer support node < 6, to use `@dramaorg/libero-alias` in node < 6:

```js
var @dramaorg/libero-alias = require('@dramaorg/libero-alias/legacy')
```

## Upgrade 2.x -> 3.x

- All `options` of 2.x are unnecessary and removed, so just remove them.
- `@dramaorg/libero-alias()` instance is no longer an [`EventEmitter`](nodejs.org/api/events.html), and all events are unnecessary and removed.
- `.addIgnoreFile()` is removed, see the [.addIgnoreFile](#add@dramaorg/libero-aliasfilepath) section for details.

****

# Collaborators

- [@whitecolor](https://github.com/whitecolor) *Alex*
- [@SamyPesse](https://github.com/SamyPesse) *Samy Pessé*
- [@azproduction](https://github.com/azproduction) *Mikhail Davydov*
- [@TrySound](https://github.com/TrySound) *Bogdan Chadkin*
- [@JanMattner](https://github.com/JanMattner) *Jan Mattner*
- [@ntwb](https://github.com/ntwb) *Stephen Edgar*
- [@kasperisager](https://github.com/kasperisager) *Kasper Isager*
- [@sandersn](https://github.com/sandersn) *Nathan Shively-Sanders*
