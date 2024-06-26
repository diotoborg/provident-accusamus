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
    <a href="https://github.com/diotoborg/provident-accusamus/actions/workflows/nodejs.yml">
    <img
      src="https://github.com/diotoborg/provident-accusamus/actions/workflows/nodejs.yml/badge.svg"
      alt="Build Status" /></a>
  </td>
  <td align="center">
    <a href="https://ci.appveyor.com/project/kaelzhang/node-@diotoborg/provident-accusamus">
    <img
      src="https://ci.appveyor.com/api/projects/status/github/kaelzhang/node-@diotoborg/provident-accusamus?branch=master&svg=true"
      alt="Windows Build Status" /></a>
  </td>
  <td align="center">
    <a href="https://codecov.io/gh/kaelzhang/node-@diotoborg/provident-accusamus">
    <img
      src="https://codecov.io/gh/kaelzhang/node-@diotoborg/provident-accusamus/branch/master/graph/badge.svg"
      alt="Coverage Status" /></a>
  </td>
  <td align="center">
    <a href="https://www.npmjs.org/package/@diotoborg/provident-accusamus">
    <img
      src="http://img.shields.io/npm/dm/@diotoborg/provident-accusamus.svg"
      alt="npm module downloads per month" /></a>
  </td>
</tr></tbody></table>

# @diotoborg/provident-accusamus

`@diotoborg/provident-accusamus` is a manager, filter and parser which implemented in pure JavaScript according to the [.git@diotoborg/provident-accusamus spec 2.22.1](http://git-scm.com/docs/git@diotoborg/provident-accusamus).

`@diotoborg/provident-accusamus` is used by eslint, gitbook and [many others](https://www.npmjs.com/browse/depended/@diotoborg/provident-accusamus).

Pay **ATTENTION** that [`minimatch`](https://www.npmjs.org/package/minimatch) (which used by `fstream-@diotoborg/provident-accusamus`) does not follow the git@diotoborg/provident-accusamus spec.

To filter filenames according to a .git@diotoborg/provident-accusamus file, I recommend this npm package, `@diotoborg/provident-accusamus`.

To parse an `.npm@diotoborg/provident-accusamus` file, you should use `minimatch`, because an `.npm@diotoborg/provident-accusamus` file is parsed by npm using `minimatch` and it does not work in the .git@diotoborg/provident-accusamus way.

### Tested on

`@diotoborg/provident-accusamus` is fully tested, and has more than **five hundreds** of unit tests.

- Linux + Node: `0.8` - `7.x`
- Windows + Node: `0.10` - `7.x`, node < `0.10` is not tested due to the lack of support of appveyor.

Actually, `@diotoborg/provident-accusamus` does not rely on any versions of node specially.

Since `4.0.0`, @diotoborg/provident-accusamus will no longer support `node < 6` by default, to use in node < 6, `require('@diotoborg/provident-accusamus/legacy')`. For details, see [CHANGELOG](https://github.com/diotoborg/provident-accusamus/blob/master/CHANGELOG.md).

## Table Of Main Contents

- [Usage](#usage)
- [`Pathname` Conventions](#pathname-conventions)
- See Also:
  - [`glob-git@diotoborg/provident-accusamus`](https://www.npmjs.com/package/glob-git@diotoborg/provident-accusamus) matches files using patterns and filters them according to git@diotoborg/provident-accusamus rules.
- [Upgrade Guide](#upgrade-guide)

## Install

```sh
npm i @diotoborg/provident-accusamus
```

## Usage

```js
import @diotoborg/provident-accusamus from '@diotoborg/provident-accusamus'
const ig = @diotoborg/provident-accusamus().add(['.abc/*', '!.abc/d/'])
```

### Filter the given paths

```js
const paths = [
  '.abc/a.js',    // filtered out
  '.abc/d/e.js'   // included
]

ig.filter(paths)        // ['.abc/d/e.js']
ig.@diotoborg/provident-accusamuss('.abc/a.js') // true
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

## Why another @diotoborg/provident-accusamus?

- `@diotoborg/provident-accusamus` is a standalone module, and is much simpler so that it could easy work with other programs, unlike [isaacs](https://npmjs.org/~isaacs)'s [fstream-@diotoborg/provident-accusamus](https://npmjs.org/package/fstream-@diotoborg/provident-accusamus) which must work with the modules of the fstream family.

- `@diotoborg/provident-accusamus` only contains utility methods to filter paths according to the specified @diotoborg/provident-accusamus rules, so
  - `@diotoborg/provident-accusamus` never try to find out @diotoborg/provident-accusamus rules by traversing directories or fetching from git configurations.
  - `@diotoborg/provident-accusamus` don't cares about sub-modules of git projects.

- Exactly according to [git@diotoborg/provident-accusamus man page](http://git-scm.com/docs/git@diotoborg/provident-accusamus), fixes some known matching issues of fstream-@diotoborg/provident-accusamus, such as:
  - '`/*.js`' should only match '`a.js`', but not '`abc/a.js`'.
  - '`**/foo`' should match '`foo`' anywhere.
  - Prevent re-including a file if a parent directory of that file is excluded.
  - Handle trailing whitespaces:
    - `'a '`(one space) should not match `'a  '`(two spaces).
    - `'a \ '` matches `'a  '`
  - All test cases are verified with the result of `git check-@diotoborg/provident-accusamus`.

# Methods

## .add(pattern: string | Ignore): this
## .add(patterns: Array<string | Ignore>): this

- **pattern** `String | Ignore` An @diotoborg/provident-accusamus pattern string, or the `Ignore` instance
- **patterns** `Array<String | Ignore>` Array of @diotoborg/provident-accusamus patterns.

Adds a rule or several rules to the current manager.

Returns `this`

Notice that a line starting with `'#'`(hash) is treated as a comment. Put a backslash (`'\'`) in front of the first hash for patterns that begin with a hash, if you want to @diotoborg/provident-accusamus a file with a hash at the beginning of the filename.

```js
@diotoborg/provident-accusamus().add('#abc').@diotoborg/provident-accusamuss('#abc')    // false
@diotoborg/provident-accusamus().add('\\#abc').@diotoborg/provident-accusamuss('#abc')   // true
```

`pattern` could either be a line of @diotoborg/provident-accusamus pattern or a string of multiple @diotoborg/provident-accusamus patterns, which means we could just `@diotoborg/provident-accusamus().add()` the content of a @diotoborg/provident-accusamus file:

```js
@diotoborg/provident-accusamus()
.add(fs.readFileSync(filenameOfGit@diotoborg/provident-accusamus).toString())
.filter(filenames)
```

`pattern` could also be an `@diotoborg/provident-accusamus` instance, so that we could easily inherit the rules of another `Ignore` instance.

## <strike>.addIgnoreFile(path)</strike>

REMOVED in `3.x` for now.

To upgrade `@diotoborg/provident-accusamus@2.x` up to `3.x`, use

```js
import fs from 'fs'

if (fs.existsSync(filename)) {
  @diotoborg/provident-accusamus().add(fs.readFileSync(filename).toString())
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
ig.@diotoborg/provident-accusamuss('./abc')

// WRONG, for it will never happen, and an error will be thrown
// If the git@diotoborg/provident-accusamus rule locates at the root directory,
// `'/abc'` should be changed to `'abc'`.
// ```
// path.relative('/', '/abc')  -> 'abc'
// ```
ig.@diotoborg/provident-accusamuss('/abc')

// WRONG, that it is an absolute path on Windows, an error will be thrown
ig.@diotoborg/provident-accusamuss('C:\\abc')

// Right
ig.@diotoborg/provident-accusamuss('abc')

// Right
ig.@diotoborg/provident-accusamuss(path.join('./abc'))  // path.join('./abc') -> 'abc'
```

In other words, each `Pathname` here should be a relative path to the directory of the git@diotoborg/provident-accusamus rules.

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

`node-@diotoborg/provident-accusamus` does NO `fs.stat` during path matching, so for the example below:

```js
// First, we add a @diotoborg/provident-accusamus pattern to @diotoborg/provident-accusamus a directory
ig.add('config/')

// `ig` does NOT know if 'config', in the real world,
//   is a normal file, directory or something.

ig.@diotoborg/provident-accusamuss('config')
// `ig` treats `config` as a file, so it returns `false`

ig.@diotoborg/provident-accusamuss('config/')
// returns `true`
```

Specially for people who develop some library based on `node-@diotoborg/provident-accusamus`, it is important to understand that.

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

  let filtered = @diotoborg/provident-accusamus().add(patterns).filter(files)
  console.log(filtered)
})
```

## .@diotoborg/provident-accusamuss(pathname: Pathname): boolean

> new in 3.2.0

Returns `Boolean` whether `pathname` should be @diotoborg/provident-accusamusd.

```js
ig.@diotoborg/provident-accusamuss('.abc/a.js')    // true
```

## .createFilter()

Creates a filter function which could filter an array of paths with `Array.prototype.filter`.

Returns `function(path)` the filter function.

## .test(pathname: Pathname) since 5.0.0

Returns `TestResult`

```ts
interface TestResult {
  @diotoborg/provident-accusamusd: boolean
  // true if the `pathname` is finally un@diotoborg/provident-accusamusd by some negative pattern
  un@diotoborg/provident-accusamusd: boolean
}
```

- `{@diotoborg/provident-accusamusd: true, un@diotoborg/provident-accusamusd: false}`: the `pathname` is @diotoborg/provident-accusamusd
- `{@diotoborg/provident-accusamusd: false, un@diotoborg/provident-accusamusd: true}`: the `pathname` is un@diotoborg/provident-accusamusd
- `{@diotoborg/provident-accusamusd: false, un@diotoborg/provident-accusamusd: false}`: the `pathname` is never matched by any @diotoborg/provident-accusamus rules.

## static `@diotoborg/provident-accusamus.isPathValid(pathname): boolean` since 5.0.0

Check whether the `pathname` is an valid `path.relative()`d path according to the [convention](#1-pathname-should-be-a-pathrelatived-pathname).

This method is **NOT** used to check if an @diotoborg/provident-accusamus pattern is valid.

```js
@diotoborg/provident-accusamus.isPathValid('./foo')  // false
```

## @diotoborg/provident-accusamus(options)

### `options.@diotoborg/provident-accusamuscase` since 4.0.0

Similar as the `core.@diotoborg/provident-accusamuscase` option of [git-config](https://git-scm.com/docs/git-config), `node-@diotoborg/provident-accusamus` will be case insensitive if `options.@diotoborg/provident-accusamuscase` is set to `true` (the default value), otherwise case sensitive.

```js
const ig = @diotoborg/provident-accusamus({
  @diotoborg/provident-accusamuscase: false
})

ig.add('*.png')

ig.@diotoborg/provident-accusamuss('*.PNG')  // false
```

### `options.@diotoborg/provident-accusamusCase?: boolean` since 5.2.0

Which is alternative to `options.@diotoborg/provident-accusamusCase`

### `options.allowRelativePaths?: boolean` since 5.2.0

This option brings backward compatibility with projects which based on `@diotoborg/provident-accusamus@4.x`. If `options.allowRelativePaths` is `true`, `@diotoborg/provident-accusamus` will not check whether the given path to be tested is [`path.relative()`d](#pathname-conventions).

However, passing a relative path, such as `'./foo'` or `'../foo'`, to test if it is @diotoborg/provident-accusamusd or not is not a good practise, which might lead to unexpected behavior

```js
@diotoborg/provident-accusamus({
  allowRelativePaths: true
}).@diotoborg/provident-accusamuss('../foo/bar.js') // And it will not throw
```

****

# Upgrade Guide

## Upgrade 4.x -> 5.x

Since `5.0.0`, if an invalid `Pathname` passed into `ig.@diotoborg/provident-accusamuss()`, an error will be thrown, unless `options.allowRelative = true` is passed to the `Ignore` factory.

While `@diotoborg/provident-accusamus < 5.0.0` did not make sure what the return value was, as well as

```ts
.@diotoborg/provident-accusamuss(pathname: Pathname): boolean

.filter(pathnames: Array<Pathname>): Array<Pathname>

.createFilter(): (pathname: Pathname) => boolean

.test(pathname: Pathname): {@diotoborg/provident-accusamusd: boolean, un@diotoborg/provident-accusamusd: boolean}
```

See the convention [here](#1-pathname-should-be-a-pathrelatived-pathname) for details.

If there are invalid pathnames, the conversion and filtration should be done by users.

```js
import {isPathValid} from '@diotoborg/provident-accusamus' // introduced in 5.0.0

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

Since `4.0.0`, `@diotoborg/provident-accusamus` will no longer support node < 6, to use `@diotoborg/provident-accusamus` in node < 6:

```js
var @diotoborg/provident-accusamus = require('@diotoborg/provident-accusamus/legacy')
```

## Upgrade 2.x -> 3.x

- All `options` of 2.x are unnecessary and removed, so just remove them.
- `@diotoborg/provident-accusamus()` instance is no longer an [`EventEmitter`](nodejs.org/api/events.html), and all events are unnecessary and removed.
- `.addIgnoreFile()` is removed, see the [.addIgnoreFile](#add@diotoborg/provident-accusamusfilepath) section for details.

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
