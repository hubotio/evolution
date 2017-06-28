---
state: In Review
---

# JavaScript

## Motivation

This evolution proposal is part of point 3 of [the Hubot 3.0 milestone](https://github.com/hubotio/evolution/tree/01dd8577c8e616921c3c6fb61b3ccc3801cdaf1f#hubot-30):

> Modernize the project by translating CoffeeScript to JavaScript, improving integration with various developer tools, and adding features that make it easier for developers to automate their workflow.

**Scope:**

- Simplify contribution / maintenance
- Removal of tooling / build steps
- Align Node support with [Node’s LTS schedule](https://github.com/nodejs/LTS#lts-schedule1)
- maintain backwards compatibility of user scripts written in CoffeeScript

**Out of scope:**

- no new features or breaking changes (beyond dropping support for Node <v4)
- Introduce any other tooling besides linter
- Any non-trivial refactoring of the existing code base

## Proposed solution

Converting of all CoffeeScript to JavaScript which can run natively on Node 4 ([current maintenance version until April 2018](https://github.com/nodejs/LTS#lts-schedule1)).

Probably the biggest challenge is that CoffeeScript classes are incompatible
with es2015 classes: they cannot extend from each other. It is something that
[CoffeeScript v2 is addressing](http://coffeescript.org/v2/#coffeescript-2).

The hubot eco system expects CoffeeScript-compatible "classes" being return in
`require('hubot')`, even when 3rd party adapters have been implemented in
JavaScript themselves. To address that problem, I suggest we use es2015 `class`
declarations internally but wrap them in CoffeeScript compatible classes in
`/index.js`. Additionally, we can introduce a new officially support export at
`require('hubot/es2015')` which returns our es2015 class declarations, so that
3rd party developers can use es2015 class declarations themselves and inhert
from ours.

## Detailed process

The steps of the conversation will be

- [x] Convert source files from CoffeeScript to JavaScript.
- [x] Test with popular projects depending on Hubot
- [x] Convert test files from CoffeeScript to JavaScript.
- [x] update package.json
- [x] add script for JavaScript linting
- [ ] Release `hubot@3.0.0`
  - [x] Update hubot’s version range `peerDependencies` of default scripts
  - [ ] Publish `hubot@3.0.0` with `--tag=next`
  - [ ] Convert default scripts to JavaScrip, release as new breaking versions with `@next` tag
  - [ ] Convert generator to JavaScript, release new breaking version with `@next` tag
  - [ ] Test all `@next` releases
  - [ ] Set `@latest` tag on all modules
- [ ] Update documentation

All steps will be part of the same PR but split into separate commits so people
can follow more easily.

See also [examples](#examples) below

### Convert source files from CoffeeScript to JavaScript

Convert all source files from CoffeeScript to JavaScript with a tool like [decaffeinate](https://github.com/decaffeinate/decaffeinate), see also [github/hubot#1138](https://github.com/github/hubot/issues/1138).

Make sure the existing tests (still written in CoffeeScript) run against the new JavaScript.

Now go through each file and improve the code readability by hand as needed. From my experiences with converting Hoodie from CoffeeScript to JavaScript quite a lot of manual work will be required.

### Test with popular projects depending on Hubot

- [x] [slack](https://github.com/slackapi/hubot-slack)
- [x] [hipchat](https://github.com/hipchat/hubot-hipchat)
- [x] [irc](https://github.com/nandub/hubot-irc)

Instructions to test an existing adapter with the [pull request](https://github.com/github/hubot/pull/1347):

1. Check out the pull request

   ```
   git clone git@github.com:github/hubot.git
   cd hubot
   npm install
   npm link
   ```

2. Now link it into your test app using an example adapter

   Using the example of slack here:

   ```
   npm install -g yo generator-hubot
   mkdir my-awesome-hubot && cd my-awesome-hubot
   yo hubot --adapter=slack
   npm link hubot
   ```

3. Now start hubot

   ```
   NODE_PATH=`pwd`/node_modules HUBOT_SLACK_TOKEN=<your token here> ./bin/hubot --adapter slack
   ```

   replace `<your token here>` with your own bots token from slack

### Convert test files from CoffeeScript to JavaScript

Same as the previous steps only for the tests files this time.

When finished, create a separate branch with the tests written in JavaScript but the source code still in CoffeeScript to assure integrity.

### Update package.json

Remove all CoffeeScript related dependencies and tooling. Update [engines](https://docs.npmjs.com/files/package.json#engines)

Make sure that Hubot scripts can still be written in CoffeeScript. We want to break as few existing scripts as possible and many of them are written in CoffeeScript. We treat it as legacy support and will drop support for scripts written in CoffeeScript in future.

### Add script for JavaScript linting

The only tool I would like to introduce as part of this proposal is a linting tool which will be run as part of `npm test`.

I suggest [standard](https://standardjs.com/). We’ve been using it in all our projects at Hoodie and Neighbourhoodie since 2+ years and never looked back. It's a zero-configuration JavaScript linter.

### Release `hubot@3.0.0`

#### Update hubot’s version range `peerDependencies` of default scripts

- [x] [hubotio/hubot-diagnostics](https://github.com/hubotio/hubot-diagnostics/pull/6)
- [x] [hubotio/hubot-help](https://github.com/hubotio/hubot-help/pull/35)
- [x] [hubot-scripts/hubot-heroku-keepalive](https://github.com/hubot-scripts/hubot-heroku-keepalive/pull/34)
- [x] [hubot-scripts/hubot-google-images](https://github.com/hubot-scripts/hubot-google-images/pull/44)
- [x] [hubot-scripts/hubot-google-translate](https://github.com/hubot-scripts/hubot-google-translate/pull/13)
- [x] [hubot-scripts/hubot-pugme](https://github.com/hubot-scripts/hubot-pugme/pull/11)
- [x] [hubotio/hubot-redis-brain](https://github.com/hubotio/hubot-redis-brain/pull/29/files)
- [x] [hubotio/hubot-rules](https://github.com/hubotio/hubot-rules/pull/5)
- [x] [hubot-scripts/hubot-shipit](https://github.com/hubot-scripts/hubot-shipit/pull/23)
- [ ] [gkoo/hubot-maps](https://github.com/gkoo/hubot-maps/pull/10)

replace

    "peerDependencies": {
      "hubot": "2.x"
    },

with

    "peerDependencies": {
      "hubot": ">=2 <10 || 0.0.0-development"
    },

That will give us enough space to follow semantic-versioning with `hubot` without breaking the eco system with each breaking version number. The `0.0.0-development` allows linking a local `hubot` repository for testing, the `0.0.0-development` is the default placeholder value for `"version"` set by [`semantic-release-cli setup`](https://github.com/semantic-release/cli#readme).

Now manually release patch releases for all packages

- [x] [hubot-diagnostics](https://npmjs.com/package/hubot-diagnostics)
- [x] [hubot-help](https://npmjs.com/package/hubot-help)
- [x] [hubot-heroku-keepalive](https://npmjs.com/package/hubot-heroku-keepalive)
- [x] [hubot-google-images](https://npmjs.com/package/hubot-google-images)
- [x] [hubot-google-translate](https://npmjs.com/package/hubot-google-translate)
- [x] [hubot-pugme](https://npmjs.com/package/hubot-pugme)
- [x] [hubot-redis-brain](https://npmjs.com/package/hubot-redis-brain)
- [x] [hubot-rules](https://npmjs.com/package/hubot-rules)
- [x] [hubot-shipit](https://npmjs.com/package/hubot-shipit)
- [ ] [hubot-maps](https://npmjs.com/package/hubot-maps)

Once we decide that it’s time to make a breaking change which breaks existing adapters, we jump version numbers and go directly to `hubot@10`. Semantic versioning does not forbid that.

#### Publish `hubot@3.0.0` with `--tag=next`

- Setup semantic-versioning for `hubot` as described in [`semantic-release` proposal](https://github.com/hubotio/evolution/pull/6).
- Set the publish tag to `next` in `package.json` (see [publishConfig docs](https://docs.npmjs.com/files/package.json#publishconfig)).
- Add an empty commit which will trigger the `3.0.0` release, meaning it needs
  a `BREAKING CHANGE:` with a description in the body (see [changes](https://github.com/hubotio/hubot/commit/9987f073a467a07bbf53f1180bf945c18e58dee7#diff-4ac32a78649ca5bdd8e0ba38b7006a1eR4)).

Once `hubot@3.0.0` got successfully released with, test upgrading an existing app:
Setup Hubot app with the existing generator. Then update to the latest `hubot`
with `npm install --save hubot@next`. Make sure it all works. Test in

- node v4.8.3 (npm v2.15.11)
- node v6.9.5 (npm v3.10.10)
- node v8.0.0 (npm v5.0.3)

#### Convert default scripts to JavaScrip, release as new breaking versions with `@next` tag

Here is a [video tutorial for reference](https://youtu.be/4VEfjHznReo)

- [ ] [hubotio/hubot-diagnostics](https://github.com/hubotio/hubot-diagnostics) ([PR](https://github.com/hubotio/hubot-diagnostics/pull/5))
- [ ] [hubotio/hubot-help](https://github.com/hubotio/hubot-help) ([PR](https://github.com/hubotio/hubot-help/pull/33))
- [ ] [hubotio/hubot-redis-brain](https://github.com/hubotio/hubot-redis-brain)
- [ ] [hubotio/hubot-rules](https://github.com/hubotio/hubot-rules)

Note that the others will no longer be installed by default, they can be
upgraded later. (see https://github.com/hubotio/hubot/issues/1327#issuecomment-307895635)

Process for each script:

1. Convert from CoffeeScript to JavaScript. There should be no breaking changes,
   but we should release a new breaking version just in case. Only `hubot-help`
   requires `hubot` in its test. We should update the test to use `hubot@3` in
   `devDependencies`. Here is [a tutorial that might be helpful](https://youtu.be/4VEfjHznReo).
   Make sure to also remove all references to CoffeeScript from all documentation
   files (README, CONTRIBUTING ...) and code comments
2. Add coverage (compare to https://github.com/hubotio/hubot/pull/1350)
3. remove Gruntfile if present
4. remove `script/*` files – we no longer need them. Running tests only requires `npm test`
5. set "engines" in package.json to require node > 4
6. setup semantic-release using `@next` tag, see above

#### Convert generator to JavaScript, release new breaking version with `@next` tag

1. Convert all CoffeeScript to JavaScript. Make sure to update hubot
   peerDependencies & devDependencies in the script template. Check how to
   install `@next` versions of the standard scripts with [`this.npmInstall`](https://git.io/vHdYf).
2. remove Gruntfile
3. remove deprecated scripts from the default install
   - hubot-heroku-keepalive
   - hubot-google-images
   - hubot-google-translate
   - hubot-shipit
3. set "engines" in package.json to require node > 4
4. setup semantic-release using `@next` tag, see above

#### Test all `@next` releases

Install the new generator with `npm install -g generator-hubot@next`.
Generate a new app and a new script. Make sure everything works. Test in

- node v4.8.3 (npm v2.15.11)
- node v6.9.5 (npm v3.10.10)
- node v8.0.0 (npm v5.0.3)

In case of bugs release patch versions as needed. If `semantic-release` is setup
in all repositories correctly, this should be a quick and safe process, as all
patch versions will continue to be released with the `@next` tag

#### Set `@latest` tag on all modules

- Release another patch versions for [hubotio/generator-hubot](https://github.com/hubotio/hubot)
which no longer installs with `@next`.

Then set the `@latest` tag to the same version as `@next` tag on all of these
packages with `npm dist-tag add <package>@next latest`

- [ ] [hubot-diagnostics](https://npmjs.com/package/hubot-diagnostics)
- [ ] [hubot-help](https://npmjs.com/package/hubot-help)
- [ ] [hubot-redis-brain](https://npmjs.com/package/hubot-redis-brain)
- [ ] [hubot-rules](https://npmjs.com/package/hubot-rules)
- [ ] [generator-hubot](https://npmjs.com/package/hubot)
- [ ] [hubot](https://npmjs.com/package/hubot)

- update all package.json files which have configured `semantic-release` to
  release with `@next` version to release with `@latest` instead (remove the `publishConfig` from package.json).
  Commit with `chore: ...` commits, no new releases are necessary.

### Update documentation

- [ ] Remove mention of CoffeeScript in [github/hubot.github.com](https://github.com/github/hubot.github.com) (currently private)
- [ ] Add note for Debian & Ubuntu based Linux users to docs:
      [install latest Node.js](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)

## Backward compatibility

Only breaking change is dropping support of Node 0.10 and 0.12.

## Alternatives considered

- **[CoffeeScript 6.0](https://github.com/coffeescript6/discuss)**

  As stated in the [Background section](https://github.com/coffeescript6/discuss#background), JavaScript caught up with most of the features that only CoffeeScript offered in the past.

  The argument for the clear syntax is valid. But we can enforce that with a JavaScript linter.

  The main reason we decided to convert Hubot to JavaScript is to make it easier to contribute to for a wider range of people.

- **Continue to support Node <v4**

  Node versions smaller than v4 are no longer maintained and have known security vulnerabilities. The only major operating system which distributes a no longer maintained Node version by default is Debian, and there are alternative ways to install a recent Node version, see [Debian and Ubuntu based Linux distributions](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions).

  Mikeal Rogers (Node Foundation) says about Debian’s official Node packages:

  > The "official" repositories are outdated and even insecure. We need to treat them as legacy and move on.

  The Node Foundation has tried to get Debian to adapt modern Node versions like "[pretty much every other repository for packages takes modern versions.](https://twitter.com/mikeal/status/869646796888330240)" but could not succeed. There will also be [a new release coming on June 17th which will have node@4.8.2 pre-installed](https://twitter.com/olov/status/872783380445843456).

  See more responses to my question regarding old Node versions at https://twitter.com/gr2m/status/869305267464306689.
  Probably the most relevant responses is "[](https://twitter.com/olov/status/872783380445843456)"

- **Dropping support for Node <v7.6**

  _**Note:** this is still up for discussion even though the text below is written in past tense. But if we decide against, I would like to document why. Same if we decide in favour of requiring Node v7.6+_

  Node 7.6 introduced native support for async/await. For asynchronous code like much of Hubot is, this is a very nice feature. It makes the code more readable and hence more accessible for contributors which is a main objective of the Hubot community.

  The reason we decided against it is that the complexity of Hubot core is not too high, the code can be made very readable even when using Promises. Once the one-time conversion from CoffeeScript to JavaScript is done, only smaller parts of the code base will be touched, the effect of async/await vs. promises will be limited.

  More importantly, we would make it much harder for existing users to upgrade to the new Hubot version. Hubot is a widely used project and having a clear upgrade plan with a reasonable pace is critical. If we only drop support for versions which are no longer maintained we can align with Node’s [LTS schedule](https://github.com/nodejs/LTS#readme) so we can avoid a recurring discussion of when to drop support for what versions once and for all.

  Besides, the current Hubot does not use a single promise in its core implementation at this point.

<a name="examples"></a>
## Examples

- [`index.coffee`](#example1)
- [`src/robot.coffee`](#example2)

<a name="example1"></a>
### `index.coffee`

Original CoffeeScript

```coffeescript
User                                                                 = require './src/user'
Brain                                                                = require './src/brain'
Robot                                                                = require './src/robot'
Adapter                                                              = require './src/adapter'
Response                                                             = require './src/response'
{Listener,TextListener}                                              = require './src/listener'
{Message,TextMessage,EnterMessage,LeaveMessage,TopicMessage,CatchAllMessage} = require './src/message'

module.exports = {
  User
  Brain
  Robot
  Adapter
  Response
  Listener
  TextListener
  Message
  TextMessage
  EnterMessage
  LeaveMessage
  TopicMessage
  CatchAllMessage
}

module.exports.loadBot = (adapterPath, adapterName, enableHttpd, botName, botAlias) ->
  new Robot adapterPath, adapterName, enableHttpd, botName, botAlias
```

JavaScript compatible with Node 4

```javascript
'use strict'

const User = require('./src/user')
const Brain = require('./src/brain')
const Robot = require('./src/robot')
const Adapter = require('./src/adapter')
const Response = require('./src/response')
const Listener = require('./src/listener')
const Message = require('./src/message')

module.exports = {
  User,
  Brain,
  Robot,
  Adapter,
  Response,
  Listener: Listener.Listener,
  TextListener: Listener.TextListener,
  Message: Message.Message,
  TextMessage: Message.TextMessage,
  EnterMessage: Message.EnterMessage,
  LeaveMessage: Message.LeaveMessage,
  TopicMessage: Message.TopicMessage,
  CatchAllMessage: Message.CatchAllMessage,

  loadBot (adapterPath, adapterName, enableHttpd, botName, botAlias) {
    return new Robot(adapterPath, adapterName, enableHttpd, botName, botAlias)
  }
}
```

JavaScript compatible with Node 7.6+

```js
const User = require('./src/user')
const Brain = require('./src/brain')
const Robot = require('./src/robot')
const Adapter = require('./src/adapter')
const Response = require('./src/response')
const {Listener, TextListener} = require('./src/listener')
const {Message, TextMessage, EnterMessage, LeaveMessage, TopicMessage, CatchAllMessage} = require('./src/message')

module.exports = {
  User,
  Brain,
  Robot,
  Adapter,
  Response,
  Listener,
  TextListener,
  Message,
  TextMessage,
  EnterMessage,
  LeaveMessage,
  TopicMessage,
  CatchAllMessage,

  loadBot (adapterPath, adapterName, enableHttpd, botName, botAlias) {
    return new Robot(adapterPath, adapterName, enableHttpd, botName, botAlias)
  }
}
```

<a name="example2"></a>
### `src/robot.coffee` (excerpt)

Original

```coffeescript
class Robot

  constructor: (adapterPath, adapter, httpd, name = 'Hubot', alias = false) ->
    @adapterPath ?= Path.join __dirname, "adapters"

    @name       = name

    @on 'error', (err, res) =>
      @invokeErrorHandlers(err, res)
    @onUncaughtException = (err) =>
      @emit 'error', err
    process.on 'uncaughtException', @onUncaughtException

  send: (envelope, strings...) ->
    @adapter.send envelope, strings...
```

JavaScript compatible with Node 4

```javascript
'use strict'

class Robot {
  constructor (adapterPath, adapter, httpd, name, alias) {
    if (name == null) { name = 'Hubot' }
    if (alias == null) { alias = false }

    this.name = name

    this.on('error', (err, res) => {
      return this.invokeErrorHandlers(err, res)
    })
    this.onUncaughtException = err => {
      return this.emit('error', err)
    }
    process.on('uncaughtException', this.onUncaughtException)
  }

  send (envelope /*, ...strings */) {
    const strings = [].slice.call(arguments, 1)
    return this.adapter.send(envelope, strings)
  }
}
```

JavaScript compatible with Node 7.6+

```javascript
class Robot {
  constructor (adapterPath, adapter, httpd, name, alias) {
    if (name == null) { name = 'Hubot' }
    if (alias == null) { alias = false }

    this.name = name

    this.on('error', (err, res) => {
      return this.invokeErrorHandlers(err, res)
    })
    this.onUncaughtException = err => {
      return this.emit('error', err)
    }
    process.on('uncaughtException', this.onUncaughtException)
  }

  send (envelope, ...strings) {
    return this.adapter.send(envelope, ...Array.from(strings))
  }
}
```
