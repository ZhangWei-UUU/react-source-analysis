---
title: "Codebase Overview"
date: 2018-01-28T21:48:57+01:00
anchor: "Codebase Overview"
weight: 1
---

### External Dependencies

React has almost no external dependencies. Usually, a require() points to a file in React’s own codebase. However, there are a few relatively rare exceptions.

The [fbjs repository]("#") exists because React shares some small utilities with libraries like Relay, and we keep them in sync. We don’t depend on equivalent small modules in the Node ecosystem because we want Facebook engineers to be able to make changes to them whenever necessary. None of the utilities inside fbjs are considered to be public API, and they are only intended for use by Facebook projects such as React.

### Top-Level Folders

After cloning the React repository, you will see a few top-level folders in it:

* [packages]("#") contains metadata (such as package.json) and the source code (src subdirectory) for all packages in the React repository. If your change is related to the code, the src subdirectory of each package is where you’ll spend most of your time.

* [fixtures](https://github.com/facebook/react/blob/master/fixtures/art/VectorWidget.js) contains a few small React test applications for contributors.

* build is the build output of React. It is not in the repository but it will appear in your React clone after you build it for the first time.

he documentation is hosted in a separate repository from React.

There are a few other top-level folders but they are mostly used for the tooling and you likely won’t ever encounter them when contributing.

### Colocated Tests

We don’t have a top-level directory for unit tests. Instead, we put them into a directory called `__tests__` relative to the files that they test.

For example, a test for setInnerHTML.js is located in `__tests__`/setInnerHTML-test.js right next to it.

### Warnings and Invariants

The React codebase uses the warning module to display warnings:

```js
var warning = require('warning');

warning(
  2 + 2 === 4,
  'Math is not working today.'
);
```
The warning is shown when the warning condition is false.

One way to think about it is that the condition should reflect the normal situation rather than the exceptional one.

It is a good idea to avoid spamming the console with duplicate warnings:

```js
var warning = require('warning');

var didWarnAboutMath = false;
if (!didWarnAboutMath) {
  warning(
    2 + 2 === 4,
    'Math is not working today.'
  );
  didWarnAboutMath = true;
}

```
Warnings are only enabled in development. In production, they are completely stripped out. If you need to forbid some code path from executing, use invariant module instead:

```js
var invariant = require('invariant');

invariant(
  2 + 2 === 4,
  'You shall not pass!'
);
```

The invariant is thrown when the invariant condition is false.

“Invariant” is just a way of saying “this condition always holds true”. You can think about it as making an assertion.

It is important to keep development and production behavior similar, so invariant throws both in development and in production. The error messages are automatically replaced with error codes in production to avoid negatively affecting the byte size.

### Development and Production
You can use `__DEV__` pseudo-global variable in the codebase to guard development-only blocks of code.

It is inlined during the compile step, and turns into process.env.NODE_ENV !== 'production' checks in the CommonJS builds.

For standalone builds, it becomes true in the unminified build, and gets completely stripped out with the if blocks it guards in the minified build.

```js
if (__DEV__) {
}
```

### Flow
We recently started introducing Flow checks to the codebase. Files marked with the @flow annotation in the license header comment are being typechecked.

We accept pull requests adding Flow annotations to existing code. Flow annotations look like this: