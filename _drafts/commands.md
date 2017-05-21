---
state: In Progress
---

# Commands

"Commands" are an explicit interface for defining discrete pieces of functionality in a Hubot script. Initially, a command is a subset of the existing `robot.respond` interface. Over time, commands will start to gain more power and will be exposed through a command line interface or as native "slash commands" on services that support it, like Slack.

## Motivation

Most Hubot scripts have a command-ish interface, but the semantics of that interface are left to the whim of the script author. This can lead to confusing or conflicting scripts with unexpected behavior.

Here are some of the problems with `robot.hear` and `robot.respond` for defining scripts:

- listeners are registered with **regular expressions**, which can be cumbersome and error-prone, and encourage authors to design scripts with unpredictable interfaces.  
- **Help documentation** must be manually defined because it cannot be generated from the definition of the listener, which leads to inconsistencies between implementation and documentation.
- Many Hubot scripts would also be valuable if they were exposed through a command line interface or to other tools and services, but are currently **tightly coupled to a chat interface**.

Here are some issues where similar features have been discussed:

- https://github.com/github/hubot/issues/249
- https://github.com/github/hubot/issues/873
- https://github.com/slackapi/hubot-slack/issues/202
- https://github.com/slackapi/hubot-slack/issues/223

## Proposed solution

A new API based on [Commander.js](https://github.com/tj/commander.js), a library with nice APIs for defining command-line interfaces, would be added on `robot.command`.

```js
robot
  .command('cowsay <words ...>')
  .description('make the cow say words')
  .action(function(words) {
    return require('cowsay').say(words.join(' '));
  });
```

## Detailed design

> Describe the design of the solution in detail. The detail in this section should be sufficient for someone who is *not* one of the authors to be able to reasonably implement the feature.

TODO:

- explicit args
  - required: `deploy <branch>`
  - optional: `deploy <branch> [environment]`
  - Variadic: `deploy <branch> [hosts...]`
  - coercion & regex for advanced syntax
  - default values
- subcommands: `deploy list`
- automated help
- disable `option`

Initially, this API will act as a subset of `respond`. The cowsay example above would look like this with existing APIs:

```js
robot.respond(/cowsay (.*)/, (res) => {
  res.reply(require('cowsay').say(res.matches[1]));
});
```

## Backward compatibility

Since this is a new API, it would not affect existing scripts.

## Alternatives considered

There have been a few previous attempts to define new interfaces for scripts:

- https://github.com/github/hubot/issues/249 - a proposal very similar to this one from 5 years ago that even suggests using commander.js. As "slash commands" have become a common feature in chat clients, this feels even more like the right direction.
- https://github.com/github/hubot/issues/873 - an alternative that proposes using classes to define an entire script. While this is interesting, it feels like a very dramatic departure from the current style.

TODO: more on why this feels like the right approach as we look toward supporting non-chat interfaces
