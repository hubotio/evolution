# Hubot Evolution

[proposal-status]: https://apple.github.io/swift-evolution/

This repository tracks the ongoing evolution of Hubot. It contains:

* Goals for upcoming releases. Up next: [3.0](#hubot-3.0)
* [Proposals](https://github.com/hubotio/evolution/pulls) to change Hubot
* The [process](CONTRIBUTING.md) that governs the evolution of Hubot.

This document describes goals on a per-release basis, usually listing minor releases adding to the currently shipping version and one major release out. Each release will have many smaller features or changes independent of these larger goals, and not all goals are reached for each release.

## Hubot 3.0

Expected release date: Late 2017

Hubot 3.0 aims to be an automation framework optimized for developers and developer workflows, with great integration with the most popular chat clients and developer tools, and an active community that is sharing scripts and best practices.

On the path to Hubot 3.0, we will:

1. Return the project to a “maintained” status by creating a core team, documenting all policies and processes, reviewing stale Issues and Pull Requests, and establishing a regular release cadence.
2. Modernize the community by consolidating officially supported repositories into on GitHub organization, creating a place for users to get support and contributors to collaborate, improving contributor experience, and establishing this process for evolving Hubot going forward.
3. Modernize the project by translating CoffeeScript to JavaScript, improving integration with various developer tools, and adding features that make it easier for developers to automate their workflow.

The high-priority features for Hubot 3.0 are:

* **Commands**: an explicit interface for exposing discrete pieces of functionality as an alternative to regular expressions (like Slack’s slash commands), along with an RPC spec and implementation for exposing commands to and consuming commands from other services.

* **Multiple adapters and archetypes**: Adding support for multiple adapters in a single instance, and some notion of archetypes (chat, deployment, CI, github, etc) will allow Hubot scripts to become the glue between the various tools and systems in a developer's workflow.

* **GitHub integration**: merge with [@probot](https://github.com/probot) and build out first-class support for GitHub.

* **Improved chat client integration**: slash commands, rich messages, and interactive scripts on platforms that support it.

---

_Thanks to the team at Apple for laying the groundwork for this process, which was inspired by and largely borrowed from [Swift Evolution](https://github.com/apple/swift-evolution)._
