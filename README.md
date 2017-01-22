
# Setting up a Minimal, Yet Useful JavaScript Dev Environment

by Kamil Ogórek   Nov 21, 2016

[Based on this article](https://dev.to/corgibytes/setting-up-a-minimal-yet-useful-javascript-dev-environment)

## Init Node.js project and GIT repository

```bash
# Create a directory and cd into it (#protip – $_ holds argument of your last command)
$ mkdir awesome-module && cd $_

# Initialize Node.js project with default settings
$ npm init --yes

# Create initial folders and files
$ mkdir lib test
$ touch index.js lib/meaningOfLife.js test/index.test.js test/meaningOfLife.test.js

# Initialize GIT repository and create initial commit
$ git init
$ git add -A; git commit -am "Awesome Module, day one"
```
## Install tools
Here, we'll use four simple modules, each having a single purpose. Ava for testing, Standard for linting, Chokidar-cli for file watching and Precommit-hook for automatically running npm scripts.

```bash
$ npm i --save-dev ava standard chokidar-cli precommit-hook
```

*Remember to create a .gitignore file and add node_modules to it! We don’t want it in our repository.*

## Set up tools
Open `package.json` and add those scripts to your file.

```json
"scripts": {
  "test": "ava",
  "lint": "standard",
  "dev": "chokidar '**/*.js' -c 'standard && ava'"
},
"pre-commit": ["test", "lint"],
```

Once you run `npm run dev` you’ll get all of your JS files linted by Standard.js and tests run by Ava. There’s nothing more to it, and you can start working right away.

The same goes for creating GIT commits. You won’t be able to do so, unless all of your tests are green and linter is happy.

Two things worth noting:

1. You don’t have to install standard or ava globally, 
as they are run from within the node context.
2. Because we use `&&` instead of `;` here in the `dev` script, 
tests won’t be triggered unless you pass linter rules. 
It makes the feedback loop even faster.

## Working with the setup

Because the environment assumes you’ll work in TDD style (and you probably should!), once you run your dev script, you can create tests and they will be added to the test suite, without any need for restarting a watcher or rebuilding anything.

Let’s create the first test.

```javascript
// test/meaningOfLife.test.js
const test = require('ava')
const meaningOfLife = require('../lib/meaningOfLife')

test('Real meaning of life', (t) => {
  t.is(meaningOfLife(), 42)
})

```

Once you save the file, you’ll get instantly notified that one of your tests is failing. Let’s fix it.

```javascript
// lib/meaningOfLife.js
module.exports = () => 42

```
And we are back to green! It's as simple as that. Let's create another module that'll multiply a numeric parameter, unit test this module and see whether it’ll work nicely with our “meaning of life” (note that it’s already an integration test, not unit!).

```javascript
// lib/multiply.js
module.exports = (number) => {
  if (typeof number !== 'number') throw new TypeError('Only numbers can be multiplied!')
  return number * 2
}

```

```javascript
// test/multiply.test.js
const test = require('ava')
const multiply = require('../lib/multiply')

test('Can multiply numbers', (t) => {
  t.is(multiply(10), 20)
})

test('Throws when you try to multiply non-number value', (t) => {
  t.throws(() => multiply('ohai!'), 'Only numbers can be multiplied!')
})

```

Now, to test it all together:

```javascript
// test/index.test.js
const test = require('ava')
const awesomeModule = require('../index')

test('Works nicely together', (t) => {
  t.is(awesomeModule(), 84)
})

```

```javascript
// index.js
const meaningOfLife = require('./lib/meaningOfLife')
const multiply = require('./lib/multiply')

module.exports = () => multiply(meaningOfLife())

```

It works! In just a few minutes, we got everything up and running.

We, as developers, are often charmed by shiny new tools. We seem to forget that those things should make our work easier, faster and less error prone. The simplest solutions are more than enough, more often than we think. Instead of spending an enormous amount of time on the setup, we should spend it on writing the software itself. And following these steps will allow you to do just that.

Once your project starts to grow, you may find yourself in need of something more complex. In most cases, however, it just won’t happen. And those tools will suit your needs quite well for a long, long time.
 
Kamil Ogórek
twitter: [kamilogorek](http://twitter.com/kamilogorek) github: [kamilogorek](http://github.com/kamilogorek) link: [kamilogorek.pl](kamilogorek.pl)

<aside class="notice">
Note: This is all based on the article from the link above. All credit goes to the original author.
</aside>