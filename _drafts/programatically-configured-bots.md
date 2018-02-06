---
state: In Review
---

# Programmatic configuration of bots

A programmatically configured hubot would allow for developers to write a index.coffee (or Javascript-VM-language-of-choice file) to build their bot step-by-step. This would give fine-grained control of a Hubot instance, such as control of the script load order, customization of the built-in [Express](https://expressjs.com/) router, tuning and instrumentation of the Node.js VM.

## Motivation

Hubot runs your code is by having it in a `scripts` directory, or specified in `external-scripts.json` (and the deprecated `hubot-scripts.json`). This means there is not an obvious place to inject framework-level customizations (like an install-specific `robot` helper function).

Hubot loads scripts alphabetically in the script directory, so the current workaround is to make a script file named that Hubot will load first. Consider:

```coffeescript
# scripts/0-myawesomehelper.coffee
module.exports = (robot) ->
  robot.myawesomehelper = () ->
    # insert awesome code here

# scripts/whatever.coffee
module.exports = (robot) ->
  robot.myawesomehelper()
```

Some tools require initialization as soon as possible to instrument as much as possible. There's no easy way to load that any sooner than when Hubot starts loading scripts. [hubot#847](https://github.com/github/hubot/issues/847) is one example of this, where the user wants to instrument Hubot with [New Relic](https://newrelic.com/), but it can't load soon enough to be effective.

Hubot includes an instance of [Express](https://expressjs.com/) in it, for convenience in integrating with other services, and also because some adapters need it. Express is has innumerable configuration options, and it's difficult to accommodate that configurability from Hubot. There have been PRs and requests over the year to allow customization of some of its Express, and have involved adding an increasing number of environment variables to control the behavior. An experienced Express user starting with Hubot would not be able to configure the server in all the ways they are familiar with. It's worth a separate evolution proposal for more specifics on that, but a programmatic launch of hubot would be a prerequisite of it.

Lastly, there is a fair amount of scaffolding to get hubot running. Programmatic launching of hubot would allow for a single-file example hubot. This would make for a great getting started example, and also obviate the need for a generator which has been programmatic in the past.

## Proposed solution

> Describe your solution to the problem. Provide examples and describe how they work. Show how your solution is better than current workarounds.

Add a public API for using hubot as a module, in addition to the `bin/hubot` launcher. `bin/hubot` will continue to work for basic usage of loading scripts from a local directory and from npm modules, and using `hubot` as a module will be an option for more advanced usage, including controlling load-order, extending hubot, or anything else you can do with Javascript.


Here is a an example `index.coffee` for using hubot as a module to with similar behavior to `bin/hubot`:

```coffeescript
Hubot   = require 'hubot'
Log     = require 'log'
Path    = require 'path'

robot = new Hubot.Robot
robot.logger = new Log('info')
robot.adapter = new Hubot.BuiltinAdapters.Shell(robot)
robot.alias = '/'

# wait for adapter to connect, since it might change robot.name at runtime,
# like robot name
robot.adapter.once 'connected', ->

  # require external hubot script packages
  robot.loadPackage 'hubot-help'
  robot.loadPackage 'hubot-shipit'

  # load scripts from a local scripts directory
  # NOTE you need to either specify NODE_PATH=. or Path.resolve a local directory for this to work
  robot.load Path.resolve 'scripts'

  # or do things them inline
  robot.respond /ping/i, (res) ->
    res.send 'PONG'

# then run hubot
robot.run()
```

## Detailed design

> Describe the design of the solution in detail. The detail in this section should be sufficient for someone who is *not* one of the authors to be able to reasonably implement the feature.

Much of the logic for loading scripts is currently in `bin/hubot`. It should all be moved to `Robot`. You can see there are several `load` methods, `loadExternalScripts` which loads external scripts from packages, `loadScripts` which calls that plus loads scripts the `scripts` directory. 

The `Robot` constructor current takes several positional arguments. This doesn't make initialzing the Robot simple, because it's not clear when initialzing what each argument is, especially when some are `false` or `null`. As part of this evolution, the constructor should take an object of options, so the object properties reflect what is being set. There is some validation already, but further validation will be needed to make sure required parameteres are passed it. It should be possible to make this backwards compatible by inspecting `arguments[0]`, and if it's an object, then it's this new style, otherwise use the old way of doing things.

In addition, it might be helpful to be able to set parameters on new lines, rather than at `Robot` creation. In that case, the parameters shouldn't be required at startup, but at the time that `robot.run()` is called.

In order to control the load order of scripts, there will need to be a new method, like `robot.loadPackage`. It would just `require` the pased in package name, then call it as a function with the current `robot`. This could be re-used in at least `robot.loadExternalScripts`.

Here is a rough checklist of steps above. It should be possible to do in a number of PRs, rather than one big one.

- [ ] move script-loading logic from `bin/hubot` to `Robot`, and update `bin/hubot` to call it
- [ ] update internals to remove assumptions about startup (ie not from bin/hubot)
  - https://github.com/github/hubot/pull/1110 is a first attempt at it
  - it is mostly moving a lot of logic from `bin/hubot` into the `hubot` so it can be re-used
- [ ] update APIs to easier, more obvious how they work, etc when running from a single file
  - probably means `Robot` taking key/values, rather than positional arguments
  - make arguments/options to `Robot` constructor optional, but validate required options when `run`ning
- [ ] update documentation for module usage

## Backward compatibility

> Describe how this change affects compatibility with existing community packages. Will they break? Will they produce different behavior? If "yes" to either of these, is it possible to support the old behavior alongside the new behavior? Can scripts be written in a way that they work before and after this change?

Changing the constructor to use an Object of options would break compatibility. It should be possible to detect if the first argument is an Object to use the new style, or use the old positional arguments to preserve existing behavior though.

`Robot`'s constructor might not even be a public API, but it's likely there is code out there (test helpers?) using it in its current form.

## Alternatives considered

> Describe alternative approaches to addressing the same problem, and why you chose this approach instead.

Considered having the `bin/hubot` launcher interact with a `index.coffee` or `index.js`. It wasn't clear how that would work, and just running `index.coffee` or `index.js` makes more sense if you are familiar with running javascript projects.

Considered adding properties to `Robot` for configuring script packages and script directories handled by `Robot#run`. This would take away much of the flexibility of being able to control load order.
