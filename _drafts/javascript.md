---
state: In Progress
---

# JavaScript

## Motivation

This evolution proposal is part of point 3 of the Hubot 3.0 milestone:

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

The only tool I would like to introduce is a linting tool which will be run as part of `npm test`.

I suggest [standard](https://standardjs.com/). We’ve been using it in all our projects at [Hoodie](http://hood.ie/) and [Neighbourhoodie](http://neighbourhood.ie) since 2+ years and never looked back. It's a zero-configuration JavaScript linter.

## Detailed process

**TODO:** make one or two sample CoffeeScript to JavaScript conversions (I’m working on it right now and will make it part of the proposal PR for the evolution repository)

The steps of the conversation will be

- [ ] Convert source files from CoffeeScript to JavaScript.
- [ ] Convert test files from CoffeeScript to JavaScript.
- [ ] update package.json
- [ ] add script for JavaScript linting
- [ ] Update documentation

### Convert source files from CoffeeScript to JavaScript

Convert all source files from CoffeeScript to JavaScript with a tool like [espresso](https://github.com/HipsterBrown/espresso).

Make sure the existing tests (still written in CoffeeScript) run against the new JavaScript.

Now go trough each file and improve the code readability by hand as needed. From my experiences with converting Hoodie from CoffeeScript to JavaScript quite a lot of manual work will be required.

### Convert test files from CoffeeScript to JavaScript

Same as the previous steps only for the tests files this time.

When finished, create a separate branch with with the tests written in JavaScript but the source code still in CoffeeScript to assure integrity.

### Update package.json

Remove all CoffeeScript related dependencies and tooling. Update [engines](https://docs.npmjs.com/files/package.json#engines)

Make sure that Hubot scripts can still be written in CoffeeScript. We want to break as few existing scripts as possible and many of them are written in CoffeeScript. We treat it as legacy support and will drop support for scripts written in CoffeeScript in future.

### Add script for JavaScript linting

The only tool I would like to introduce as part of this proposal is a linting tool which will be run as part of `npm test`.

I suggest [standard](https://standardjs.com/). We’ve been using it in all our projects at Hoodie and Neighbourhoodie since 2+ years and never looked back. It's a zero-configuration JavaScript linter.

### Update documentation

Remove any reference of CoffeeScript from the documentation.

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

  The Node Foundation has tried to get Debian to adapt modern Node versions like "[pretty much every other repository for packages takes modern versions.](https://twitter.com/mikeal/status/869646796888330240)" but could not succeed.

  See more responses to my question regarding old Node versions at https://twitter.com/gr2m/status/869305267464306689

- **Dropping support for Node <v7.6**

  _**Note:** this is still up for discussion even though the text below is written in past tense. But if we decide against, I would like to document why. Same if we decide in favour of requiring Node v7.6+_

  Node 7.6 introduced native support for async/await. For asynchronous code like much of Hubot is, this is a very nice feature. It makes the code more readable and hence more accessible for contributors which is a main objective of the Hubot community.

  The reason we decided against it is that the complexity of Hubot  core is not too high, the code can be made very readable even when using Promises. Once the one-time conversion from CoffeeScript to JavaScript is done, only smaller parts of the code base will be touched, the effect of async/await vs. promises will be limited.

  More importantly, we would make it much harder for existing users to upgrade to the new Hubot version. Hubot is a widely used project and having a clear upgrade plan with a reasonable pace is critical. If we only drop support for versions which are no longer maintained we can align with Node’s [LTS schedule](https://github.com/nodejs/LTS#readme) so we can avoid a recurring discussion of when to drop support for what versions once and for all.
