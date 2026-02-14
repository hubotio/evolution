# Hubot Evolution

[proposal-status]: https://apple.github.io/swift-evolution/

This repository tracks the ongoing evolution of [Hubot](https://github.com/github/hubot). It contains:

* [Goals](#goals) for upcoming releases.
* [Proposals](https://github.com/hubotio/evolution/projects/1) to change Hubot
* The [process](CONTRIBUTING.md) that governs the evolution of Hubot.

This document describes the high-level goals for the next couple months. Smaller features or changes independent of these larger goals will also be made, and some goals may not be reached.

## Goals

Hubot aims to be an automation framework optimized for developers and developer workflows, with great integration with the most popular chat clients and developer tools, and an active community that is sharing scripts and best practices.

The current focus is:

1. Return the project to a “maintained” status by creating a core team, documenting all policies and processes, reviewing stale Issues and Pull Requests, and establishing a regular release cadence.
2. Modernize the community by consolidating officially supported repositories into one GitHub organization, creating a place for users to get support and contributors to collaborate, improving contributor experience, and establishing this process for evolving Hubot going forward.
3. Modernize the project by translating CoffeeScript to JavaScript, improving integration with various developer tools, and adding features that make it easier for developers to automate their workflow.

The high-priority features for upcoming releases are:

* **Commands**: an explicit interface for exposing discrete pieces of functionality as an alternative to regular expressions (like Slack’s slash commands), along with an RPC spec and implementation for exposing commands to and consuming commands from other services.

* **Multiple adapters and archetypes**: Adding support for multiple adapters in a single instance, and some notion of archetypes (chat, deployment, CI, github, etc) will allow Hubot scripts to become the glue between the various tools and systems in a developer's workflow.

* **Improved chat client integration**: slash commands, rich messages, and interactive scripts on platforms that support it.

---

_Thanks to the team at Apple for laying the groundwork for this process, which was inspired by and largely borrowed from [Swift Evolution](https://github.com/apple/swift-evolution)._
