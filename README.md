<p align="center">

<a href="https://github.com/assemble/assemble">
<img height="250" width="250" src="https://raw.githubusercontent.com/assemble/assemble-core/master/docs/logo.png">
</a>
</p>

# assemble-core

[![NPM version](https://img.shields.io/npm/v/assemble-core.svg?style=flat)](https://www.npmjs.com/package/assemble-core) [![NPM monthly downloads](https://img.shields.io/npm/dm/assemble-core.svg?style=flat)](https://npmjs.org/package/assemble-core) [![Build Status](https://img.shields.io/travis/assemble/assemble-core.svg?style=flat)](https://travis-ci.org/assemble/assemble-core) [![Gitter](https://badges.gitter.im/join_chat.svg)](https://gitter.im/assemble/assemble-core)

Built on top of [base](https://github.com/node-base/base) and [templates](https://github.com/jonschlinkert/templates), assemble-core is used in [assemble](https://github.com/assemble/assemble) to provide the baseline features and API necessary for rendering templates, working with the file system, and running tasks.

Implementors and hackers can use assemble-core to create rich and powerful build tooling, project scaffolding systems, documentation generators, or even your completely custom static site generators.

<details>
<summary><strong>Table of contents</strong></summary>
- [What can I do with assemble-core?](#what-can-i-do-with-assemble-core)
- [Install](#install)
- [Install](#install-1)
- [Usage](#usage)
- [Examples](#examples)
- [API](#api)
  * [File System API](#file-system-api)
    + [.src](#src)
    + [.dest](#dest)
    + [.copy](#copy)
    + [.symlink](#symlink)
  * [Task API](#task-api)
    + [.task](#task)
    + [.build](#build)
    + [.watch](#watch)
- [FAQ](#faq)
- [Toolkit suite](#toolkit-suite)
- [About](#about)
  * [Related projects](#related-projects)
  * [Tests](#tests)
  * [Contributing](#contributing)
  * [Release History](#release-history)
  * [Authors](#authors)
  * [License](#license)

_(TOC generated by [verb](https://github.com/verbose/verb) using [markdown-toc](https://github.com/jonschlinkert/markdown-toc))_
</details>

## What can I do with assemble-core?

Create your own:

* blog engine
* project generator / scaffolder
* e-book development framework
* build system
* landing page generator
* documentation generator
* front-end UI framework
* rapid prototyping framework
* static site generator
* web application

## Install

**NPM**

## Install

Install with [npm](https://www.npmjs.com/):

```sh
$ npm install --save assemble-core
```

**yarn**

Install with [yarn](yarnpkg.com):

```sh
$ yarn add assemble-core && yarn upgrade
```

## Usage

```js
var assemble = require('assemble-core');
var app = assemble();
```

## Examples

**view collections**

Create a custom view collection:

```js
var app = assemble();
app.create('pages');
```

Now you can add pages with `app.page()` or `app.pages()`:

```js
app.page('home.hbs', {content: 'this is the home page!'});
```

**render**

Render a view:

```js
var app = assemble();

var view = app.view('foo', {content: 'Hi, my name is <%= name %>'});

app.render(view, { name: 'Brian' }, function(err, res) {
  console.log(res.content);
  //=> 'Hi, my name is Brian'
});
```

Render a view from a collection:

```js
var app = assemble();
app.create('pages');

app.page('foo', {content: 'Hi, my name is <%= name %>'})
  .set('data.name', 'Brian')
  .render(function (err, res) {
    console.log(res.content);
    //=> 'Hi, my name is Brian'
  });
```

## API

### [Assemble](index.js#L22)

Create an `assemble` application. This is the main function exported by the assemble module.

**Params**

* `options` **{Object}**: Optionally pass default options to use.

**Example**

```js
var assemble = require('assemble');
var app = assemble();
```

***

### File System API

Assemble has the following methods for working with the file system:

* [src](#src)
* [dest](#dest)
* [copy](#copy)
* [symlink](#symlink)

Assemble v0.6.0 has full [vinyl-fs](http://github.com/wearefractal/vinyl-fs) support, so any [gulp](http://gulpjs.com) plugin should work with assemble.

#### .src

Use one or more glob patterns or filepaths to specify source files.

**Params**

* `glob` **{String|Array}**: Glob patterns or file paths to source files.
* `options` **{Object}**: Options or locals to merge into the context and/or pass to `src` plugins

**Example**

```js
app.src('src/*.hbs', {layout: 'default'});
```

#### .dest

Specify the destination to use for processed files.

**Params**

* `dest` **{String|Function}**: File path or custom renaming function.
* `options` **{Object}**: Options and locals to pass to `dest` plugins

**Example**

```js
app.dest('dist/');
```

#### .copy

Copy files from A to B, where `A` is any pattern that would be valid in [app.src](#src) and `B` is the destination directory.

**Params**

* `patterns` **{String|Array}**: One or more file paths or glob patterns for the source files to copy.
* `dest` **{String|Function}**: Desination directory.
* `returns` **{Stream}**: The stream is returned, so you can continue processing files if necessary.

**Example**

```js
app.copy('assets/**', 'dist/');
```

#### .symlink

Glob patterns or paths for symlinks.

**Params**

* `glob` **{String|Array}**

**Example**

```js
app.symlink('src/**');
```

***

### Task API

Assemble has the following methods for running tasks and controlling workflows:

* [task](#task)
* [build](#build)
* [watch](#watch)

#### .task

Define a task. Tasks are functions that are stored on a `tasks` object, allowing them to be called later by the [build](#build) method. (the [CLI](https://github.com/assemble/assemble-cli) calls [build](#build) to run tasks)

**Params**

* `name` **{String}**: Task name
* `fn` **{Function}**: function that is called when the task is run.

**Example**

```js
app.task('default', function() {
  return app.src('templates/*.hbs')
    .pipe(app.dest('dist/'));
});
```

#### .build

Run one or more tasks.

**Params**

* `tasks` **{Array|String}**: Task name or array of task names.
* `cb` **{Function}**: callback function that exposes `err`

**Example**

```js
app.build(['foo', 'bar'], function(err) {
  if (err) console.error('ERROR:', err);
});
```

#### .watch

Watch files, run one or more tasks when a watched file changes.

**Params**

* `glob` **{String|Array}**: Filepaths or glob patterns.
* `tasks` **{Array}**: Task(s) to watch.

**Example**

```js
app.task('watch', function() {
  app.watch('docs/*.md', ['docs']);
});
```

## FAQ

**How does assemble-core differ from [assemble](https://github.com/assemble/assemble)?**

| **feature** | **assemble-core** | **assemble** | **notes** | 
| --- | :---: | :---: | --- |
| front-matter parsing | No | Yes | use [assemble](https://github.com/assemble/assemble) or use [parser-front-matter](https://github.com/jonschlinkert/parser-front-matter) as an `.onLoad` middleware. |
| CLI | No | Yes | Create your own CLI experience, or use [assemble](https://github.com/assemble/assemble) |
| Built-in template collections | No | Yes | Use `.create()` to add collections |
| Built-in template engine | No | Yes | [assemble](https://github.com/assemble/assemble) ships with [engine-handlebars](https://github.com/jonschlinkert/engine-handlebars). Use `.engine()` to register any [consolidate](https://github.com/visionmedia/consolidate.js)-compatible template engine. |

## Toolkit suite

assemble-core is a standalone application that was created using applications and plugins from the [toolkit suite](https://github.com/node-toolkit/getting-started):

**Building blocks**

* [base](https://github.com/node-base/base): framework for rapidly creating high quality node.js applications, using plugins like building blocks.
* [templates](https://github.com/jonschlinkert/templates): used to create the foundation of assemble-core's API, along with support for managing template collections, template engines and template rendering support

**Plugins**

* [assemble-fs](https://github.com/assemble/assemble-fs): adds support for using [gulp](http://gulpjs.com) plugins and working with the file system
* [assemble-streams](https://github.com/assemble/assemble-streams): adds support for pushing views and view collections into a [vinyl](https://github.com/gulpjs/vinyl) stream
* [base-task](https://github.com/node-base/base-task): adds flow control methods

## About

### Related projects

Assemble is built on top of these great projects:

* [assemble](https://www.npmjs.com/package/assemble): Get the rocks out of your socks! Assemble makes you fast at creating web projects… [more](https://github.com/assemble/assemble) | [homepage](https://github.com/assemble/assemble "Get the rocks out of your socks! Assemble makes you fast at creating web projects. Assemble is used by thousands of projects for rapid prototyping, creating themes, scaffolds, boilerplates, e-books, UI components, API documentation, blogs, building websit")
* [boilerplate](https://www.npmjs.com/package/boilerplate): Tools and conventions for authoring and using declarative configurations for project "boilerplates" that can be… [more](https://github.com/jonschlinkert/boilerplate) | [homepage](https://github.com/jonschlinkert/boilerplate "Tools and conventions for authoring and using declarative configurations for project "boilerplates" that can be consumed by any build system or project scaffolding tool.")
* [composer](https://www.npmjs.com/package/composer): API-first task runner with three methods: task, run and watch. | [homepage](https://github.com/doowb/composer "API-first task runner with three methods: task, run and watch.")
* [generate](https://www.npmjs.com/package/generate): Command line tool and developer framework for scaffolding out new GitHub projects. Generate offers the… [more](https://github.com/generate/generate) | [homepage](https://github.com/generate/generate "Command line tool and developer framework for scaffolding out new GitHub projects. Generate offers the robustness and configurability of Yeoman, the expressiveness and simplicity of Slush, and more powerful flow control and composability than either.")
* [scaffold](https://www.npmjs.com/package/scaffold): Conventions and API for creating declarative configuration objects for project scaffolds - similar in format… [more](https://github.com/jonschlinkert/scaffold) | [homepage](https://github.com/jonschlinkert/scaffold "Conventions and API for creating declarative configuration objects for project scaffolds - similar in format to a grunt task, but more portable, generic and can be used by any build system or generator - even gulp.")
* [templates](https://www.npmjs.com/package/templates): System for creating and managing template collections, and rendering templates with any node.js template engine… [more](https://github.com/jonschlinkert/templates) | [homepage](https://github.com/jonschlinkert/templates "System for creating and managing template collections, and rendering templates with any node.js template engine. Can be used as the basis for creating a static site generator or blog framework.")
* [verb](https://www.npmjs.com/package/verb): Documentation generator for GitHub projects. Verb is extremely powerful, easy to use, and is used… [more](https://github.com/verbose/verb) | [homepage](https://github.com/verbose/verb "Documentation generator for GitHub projects. Verb is extremely powerful, easy to use, and is used on hundreds of projects of all sizes to generate everything from API docs to readmes.")

### Tests

Running and reviewing unit tests is a great way to get familiarized with a library and its API. You can install dependencies and run tests with the following command:

```sh
$ npm install && npm test
```

### Contributing

Pull requests and stars are always welcome. For bugs and feature requests, [please create an issue](../../issues/new).

Please read the [contributing guide](.github/contributing.md) for advice on opening issues, pull requests, and coding standards.

If Assemble doesn't do what you need, [please let us know](../../issues).

### Release History

#### key

Changelog entries are classified using the following labels from [keep-a-changelog](https://github.com/olivierlacan/keep-a-changelog):

* `added`: for new features
* `changed`: for changes in existing functionality
* `deprecated`: for once-stable features removed in upcoming releases
* `removed`: for deprecated features removed in this release
* `fixed`: for any bug fixes

Custom labels used in this changelog:

* `dependencies`: bumps dependencies
* `housekeeping`: code re-organization, minor edits, or other changes that don't fit in one of the other categories.

**Heads up!**

Please [let us know](../../issues) if any of the following heading links are broken. Thanks!

#### [0.31.0](https://github.com/assemble/assemble-core/compare/0.30.0...0.31.0) - 2017-02-11

**dependencies**

* Bumps [assemble-streams](https://github.com/assemble/assemble-streams) to ensure that `view` is decorated with `.toStream()` when created by app (instead of a collection). This is arguably a bugfix, but it might break someone's code.

#### [0.30.0](https://github.com/assemble/assemble-core/compare/0.29.0...0.30.0) - 2017-08-01

**dependencies**

* Bumps [assemble-fs](https://github.com/assemble/assemble-fs) and [assemble-render-file](https://github.com/assemble/assemble-render-file) to get updates that merge the dest path information onto the context so that it can be used to calculate relative paths for navigation, pagination, etc.

#### [0.29.0](https://github.com/assemble/assemble-core/compare/0.28.0...0.29.0) - 2017-02-01

**dependencies**

* Bumps [assemble-fs](https://github.com/assemble/assemble-fs) to v0.9.0 to take advantage of improvements to `.dest()` handling.

#### [0.28.0](https://github.com/assemble/assemble-core/compare/0.27.0...0.28.0) - 2017-02-01

**dependencies**

* Bumps [templates](https://github.com/jonschlinkert/templates) to v1.2.0 to take advantage of new methods available on `list`s

#### [0.27.0](https://github.com/assemble/assemble-core/compare/0.26.0...0.27.0) - 2016-12-27

**dependencies**

* Bumps [templates](https://github.com/jonschlinkert/templates) to v1.0.0 to take advantage of new template inheritance features!
* Bumps [assemble-fs](https://github.com/assemble/assemble-fs) to v0.8.0

#### [0.26.0](https://github.com/assemble/assemble-core/compare/0.25.0...0.26.0) - 2016-08-06

**dependencies**

* Bumps [assemble-fs](https://github.com/assemble/assemble-fs) to v0.7.0 to take advantage of `handle.once`
* Bumps [templates](https://github.com/jonschlinkert/templates) to v0.25.0 to take advantage of async collection loaders. No changes to existing API.

#### [0.25.0](https://github.com/assemble/assemble-core/compare/0.24.0...0.25.0) - 2016-07-05

**changed**

* Minor code organization and [private properties were changed](https://github.com/assemble/assemble-core/commit/5746164004647f2b7dc8883a3323922839f56958). This is technically a housekeeping change since these methods weren't exposed on the API, but it's possible someone was using them in an unintended way.

#### [0.24.0](https://github.com/assemble/assemble-core/compare/0.23.0...0.24.0)

**dependencies**

* Bumps [templates](https://github.com/jonschlinkert/templates) to v0.24.0 to use the latest [base-data](https://github.com/node-base/base-data)

**removed**

* The bump in `templates` removes the `renameKey` option from the `.data` method. Use the `namespace` option instead.

#### [0.23.0](https://github.com/assemble/assemble-core/compare/0.22.0...0.23.0)

**fixed**

* Bumps [templates](https://github.com/jonschlinkert/templates) to v0.23.0 to fix bug with double rendering in [engine-cache](https://github.com/jonschlinkert/engine-cache).

#### [0.22.0](https://github.com/assemble/assemble-core/compare/0.21.0...0.22.0)

**dependencies**

* Bumps [templates](https://github.com/jonschlinkert/templates) to v0.22.0 to take advantage of fixes and improvements to lookup methods: `.find` and `getView`. No API changes were made. Please [let us know](../../issues) if regressions occur.
* Bumps [base](https://github.com/node-base/base) to take advantages of code optimizations.

**fixed**

* fixes `List` bug that was caused collection helpers to explode

**changed**

* Improvements to lookup functions: `app.getView()` and `app.find()`

#### [0.21.0](https://github.com/assemble/assemble-core/compare/0.20.0...0.21.0)

**dependencies**

* Bumps [templates](https://github.com/jonschlinkert/templates) to v0.21.0.

**removed**

* Support for the `queue` property was removed on collections. See [templates](https://github.com/jonschlinkert/templates) for additional details.

#### [0.20.0](https://github.com/assemble/assemble-core/compare/0.19.0...0.20.0)

**dependencies**

* Bumps [templates](https://github.com/jonschlinkert/templates) to v0.20.0.

**changed**

* Some changes were made to context handling that effected one unit test out of ~1,000. although it's unlikely you'll be effected by the change, it warrants a minor bump

#### [0.19.0](https://github.com/assemble/assemble-core/compare/0.18.0...0.19.0)

**dependencies**

* Bumps [templates](https://github.com/jonschlinkert/templates) to v0.19.0

**housekeeping**

* Externalizes tests (temporarily) to base-test-runner, until we get all of the tests streamlined to the same format.

#### [0.18.0](https://github.com/assemble/assemble-core/compare/0.17.0...0.18.0)

**dependencies**

* Bumps [assemble-loader](https://github.com/assemble/assemble-loader) to v0.5.0, which includes which fixes a bug where `renameKey` was not always being used when defined on collection loader options.
* Bumps [templates](https://github.com/jonschlinkert/templates) to v0.18.0, which includes fixes for resolving layouts

#### [0.17.0](https://github.com/assemble/assemble-core/compare/0.16.0...0.17.0)

**dependencies**

* Bumps [templates](https://github.com/jonschlinkert/templates) to v0.17.0

#### [0.16.0](https://github.com/assemble/assemble-core/compare/0.15.0...0.16.0)

**dependencies**

* Bumps [assemble-render-file](https://github.com/assemble/assemble-render-file) to v0.5.0 and [templates](https://github.com/jonschlinkert/templates) to v0.16.0

#### [0.15.0](https://github.com/assemble/assemble-core/compare/0.14.0...0.15.0)

**dependencies**

* Bumps [assemble-streams](https://github.com/assemble/assemble-streams) to v0.5.0

#### [0.14.0](https://github.com/assemble/assemble-core/compare/0.13.0...0.14.0)

**dependencies**

* Bumps [templates](https://github.com/jonschlinkert/templates) to v0.15.1

**deprecated**

* `.handleView` method is now deprecated, use `.handleOnce` instead

**changed**

* Private method `.mergePartialsSync` rename was reverted to `.mergePartials` to be consistent with other updates in `.render` and `.compile`.

**added**

* adds logging methods from [base-logger](https://github.com/node-base/base-logger) (`.log`, `.verbose`, etc)
* No other breaking changes, but some new features were added to [templates](https://github.com/jonschlinkert/templates) for handling context in views and helpers.

#### [0.13.0](https://github.com/assemble/assemble-core/compare/0.9.0...0.13.0)

* Breaking change: bumps [templates](https://github.com/jonschlinkert/templates) to v0.13.0 to fix obscure rendering bug when multiple duplicate partials were rendered in the same view. But the fix required changing the `.mergePartials` method to be async. If you're currently using `.mergePartials`, you can continue to do so synchronously using the `.mergePartialsSync` method.

#### [0.9.0](https://github.com/assemble/assemble-core/compare/0.8.0...0.9.0)

* Updates [composer](https://github.com/doowb/composer) to v0.11.0, which removes the `.watch` method in favor of using the [base-watch](https://github.com/node-base/base-watch) plugin.

#### [0.8.0](https://github.com/assemble/assemble-core/compare/0.7.0...0.8.0)

* Bumps several deps. [templates](https://github.com/jonschlinkert/templates) was bumped to 0.9.0 to take advantage of event handling improvements.

#### [0.7.0](https://github.com/assemble/assemble-core/compare/0.6.0...0.7.0)

* Bumps [templates](https://github.com/jonschlinkert/templates) to 0.8.0 to take advantage of `isType` method for checking a collection type, and a number of improvements to how collections and views are instantiated and named.

#### [0.6.0](https://github.com/assemble/assemble-core/compare/0.5.0...0.6.0)

* Bumps [assemble-fs](https://github.com/assemble/assemble-fs) plugin to 0.5.0, which introduces `onStream` and `preWrite` middleware handlers.
* Bumps [templates](https://github.com/jonschlinkert/templates) to 0.7.0, which fixes how non-cached collections are initialized. This was done as a minor instead of a patch since - although it's a fix - it could theoretically break someone's setup.

#### [0.5.0](https://github.com/assemble/assemble-core/compare/0.4.0...0.5.0)

* Bumps [templates](https://github.com/jonschlinkert/templates) to latest, 0.6.0, since it uses the latest [base-methods](https://github.com/jonschlinkert/base-methods), which introduces prototype mixins. No API changes.

#### [0.4.0]

* Removed [emitter-only](https://github.com/doowb/emitter-only) since it was only includes to be used in the default listeners that were removed in an earlier release. In rare cases this might be a breaking change, but not likely.
* Adds lazy-cache
* Updates [assemble-streams](https://github.com/assemble/assemble-streams) plugin to latest

_(Changelog generated by [helper-changelog](https://github.com/helpers/helper-changelog))_

### Authors

**Jon Schlinkert**

* [github/jonschlinkert](https://github.com/jonschlinkert)
* [twitter/jonschlinkert](http://twitter.com/jonschlinkert)

**Brian Woodward**

* [github/doowb](https://github.com/doowb)
* [twitter/doowb](http://twitter.com/doowb)

### License

Copyright © 2017, [Jon Schlinkert](https://github.com/jonschlinkert).
MIT

***

_This file was generated by [verb-generate-readme](https://github.com/verbose/verb-generate-readme), v0.4.2, on February 11, 2017._