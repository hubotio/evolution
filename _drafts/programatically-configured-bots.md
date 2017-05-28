---
state: In Progress
---

# Programmatic configuration of bots

A programmatically configured hubot would allow for developers to write a index.coffee (or Javascript-VM-language-of-choice file) to build their bot step-by-step, and have full control of the order of that scripts are loaded, in addition to allowing customizing things like the built-in [expressjs](https://expressjs.com/) router and the nodejs VM.

## Motivation

The only way you can have hubot run your code is by having it in the `scripts`, or specified in `external-scripts.json` (and the deprecated `hubot-scripts.json`). This means there is not an obvious place to inject framework-level customizations (like an install-specific `robot` helper function).

The current workaround is to name script files in a way that they are more likely to be loaded first. Consider:

```coffeescript
# scripts/0-myawesomehelper.coffee
module.exports = (robot) ->
  robot.myawesomehelper = () ->
    # insert awesome code here

# scripts/whatever.coffee
module.exports = (robot) ->
  robot.myawesomehelper()
```

There are some tools that need to be loaded as soon as possible to instrument as much as possible, such as [New Relic](https://newrelic.com/). There is no way currently to load that any sooner. [hubot#847](https://github.com/github/hubot/issues/847) is one example of this.

Hubot includes an instance of [expressjs](https://expressjs.com/) in it, for convenience in integrating with other services, and also because some adapters need it. Since it is a HTTP server, there are an infinite number of ways to configure it, and it is impossible to accommodate all of them. There have been a number of PRs and requests over the year to allow customization of some of its expressjs, and have involved adding an increasing number of environment variables to control the behavior. An experienced expressjs user coming onto hubot would not be able to customize the server in a way they would be used to. The specifics of allowing this are worth having its own evolution proposal, but a programmatic launch of hubot would be a prerequisite of it.

Lastly, there is a fair amount of scaffolding to get hubot running. Programmatic launching of hubot would allow for a single-file example hubot. This would make for a great getting started example, and also obviate the need for a generator which has been programmatic in the past.


## Proposed solution

> Describe your solution to the problem. Provide examples and describe how they work. Show how your solution is better than current workarounds.

TODO

```coffeescript
Hubot   = require 'hubot'
Log     = require 'log'
Path    = require 'path'

robot = new Hubot.Robot
robot.logger = new Log('info')
robot.adapter = new Hubot.BuiltinAdapters.Shell(robot)
robot.alias = '/'

# wait for adapter to be connected, since it might change things at runtime,
# like robot name
robot.adapter.on 'connected', ->

  # require external hubot scripts
  robot.loadScriptPackage require('hubot-help')
  robot.loadScriptPackage require('hubot-shipit')

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

TODO

- update internals to remove assumptions about startup (ie not from bin/hubot)
  - https://github.com/github/hubot/pull/1110 is a start
- update APIs to easier, more obvious how they work, etc when running from a single file
  - probably means `Robot` taking key/values, rather than positional
  - make arguments/options to `Robot` constructor optional, but validate required options when `run`ning

## Backward compatibility

> Describe how this change affects compatibility with existing community packages. Will they break? Will they produce different behavior? If "yes" to either of these, is it possible to support the old behavior alongside the new behavior? Can scripts be written in a way that they work before and after this change?

Changing the constructor to use an Object of options would break compatibility. It should be possible to detect if the first argument is an Object to use the new style, or use the old positional arguments.

`Robot`'s constructor might not even be a public API currently, but it's pretty likely something out there (test helpers?) use it in its current form.

## Alternatives considered

> Describe alternative approaches to addressing the same problem, and why you chose this approach instead.
