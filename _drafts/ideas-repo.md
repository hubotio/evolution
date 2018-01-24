---
state: In Progress
---

# Hubot workflow ideas repo

We’d like to get an idea of what future Hubot needs to be capable of to provide maximum value to GitHub users, since it should evolve from a humble chatbot to a full workflow automation framework. For this, we ask the community for workflow automation ideas/wishes/utopias and collect them in an `ideas` repo.

## Motivation

Hubot is a tool to make peoples’ lives easier, and we have to do some degree of user research in order to understand what sorts of problems GitHub users want to solve, which types of processes they want to automate, which services they’d like to be able to talk to each other, and which interaction points would be useful to them. Even if the ideas yield nothing we aren‘t already covering, they’ll serve as a basis for further discussion, but also as useful confirmation that we’re on the right path.

## Proposed solution

Starting a `hubot/ideas` repo calling for… Hubot ideas, basically. We provide an issue template we expect people to adhere to, and invite users to post idea issues.

For the time being, we only collect ideas, making sure that there’s a relatively low threshold when it comes to format and process requriements. Authors of ideas that are deemed promising and realistic by the Hubot maintainers can be invited to contribute a proposal to `hubot/evolution` (or we do it for them, if they can’t or won’t). This of course only if the idea/proposal is directly related to hubot.

In all other cases, where the idea requires enhancements outside of Hubot or even outside of GitHub, we have no real leverage except pointing those institutions to the respective issue ideas and hoping that each issue’s popularity carries some weight. Nevertheless, we’d have a central, public repository of ideas, and we have a means of recording and displaying user feedback and enthusiasm for each idea.

## Detailed design

Set up a `hubot/ideas` repo with a welcoming `README.md`, a Code of Conduct, and an `ISSUE_TEMPLATE.md` that automatically pre-populates each new issue with our idea submission template. The template is modeled after this file (the `*-evolution` proposal), but is less strict and shorter. Instead, it requires a `Required Services`-section.

### `ISSUE_TEMPLATE.md`

```markdown

> Thanks for taking the time to submit your idea for a Hubot automation workflow! Please adhere to this template when formulating your idea, and also remove all block quotes (like this one) before posting.

# Your Hubot Workflow Idea Name

> A short description of what the feature is. Try to keep it to a single-paragraph "elevator pitch" so the reader understands what problem this proposal is addressing.

## Motivation

> Describe the problems that this idea seeks to address. If the problem is that some common pattern is currently hard to implement or execute, show how one can currently get a similar effect and describe its drawbacks. If it's completely new functionality that cannot be emulated, motivate why this new functionality would help developers create better workflows. Include any links to Issues or Pull Requests where the need for this feature came up.

## Proposed solution

> Describe your solution to the problem. Provide examples and describe how they work. Show how your solution is better than current workarounds.

## Required Services

> Add a list of services that are required for this idea to work (GitHub, Slack, IFTTT, Travis or similar). If you already know that a service exposes or doesn’t expose the necessary APIs to build this workflow, please add this information.

> Again: thanks a lot for your contribution! The Hubot maintainers thank you :)

```

### Examples

#### Tell me when it closes

```markdown
# Tell me when it closes

Notifies you when an issue closes. Or a PR lands in master or a release.

## Motivation

As [tellmewhenitcloses.com](http://tellmewhenitcloses.com) so succcinctly summarises:

- You could subscribe to the issue (and deal with all the comments that come with it)
- You could check back every few days (wasting your precious time)
- You could forget about it (and miss the new feature or fixed bug)

Instead of these, being able to tell a TMWIC-bot to watch an issue or PR would be much more convenient.

## Proposed solution

Future Hubot might be summonable in the text area at the end of each comment thread on github with a slash command. Simply typing `/@hubot tell me when (it|this) closes` would not post a comment, but a special Hubot comment only the summoning user can see, along the lines of: `I’ll let you know when this issue closes. Your Hubot.`, along with a `I’m no longer interested`-button.

For PRs, Hubot might support `/@hubot tell me when this lands in master`, or `/@hubot tell me when this is released.`

All of these would trigger a single notification by Hubot, both on GH and by email.

## Required Services

- GitHub

```

#### Keep parent/child issues in sync

```markdown
# Keep parent/child issues in sync

Automatically checks off todo list items if they include an issue/PR that was closed/merged.

## Motivation

Issue/Milestone subtasks are often referenced in a todo list:

- [ ] #122: Fix the wharrgarbl
- [ ] #124: Implement the basic wotsit
- [ ] Add the welp feature to the wotsit
- [ ] Implement the hook so we can know when the welp has completed

These frequently get forgotten by committers, leading to a lot of clicking around, trying to figure out what state things are in. Especially if issues get re-opened. It would be convenient if these would basically un/check themselves.

## Proposed solution

Future Hubot might be summonable in the text area that contains the list with something like `/@hubot keep child issues in sync`. That would add a special Hubot comment underneath the post with the list, that everyone can see, along the lines of: `I’m keeping child issues in this post in sync. Your Hubot.`, along with a `Stop doing this`-button only visible to maintainers or owners.

## Required Services

- GitHub

```

## Alternatives considered

An alternative approach would have been to adhere to the `*-evolution` format for the ideas, forcing everyone with an idea to open a pull request. We deemed this to be too much of a hurdle for participation. More fundamentally, the ideas do not require our approval, since we’re not going to directly implement them. We only aim to make (some of) them possible. Requiring a full draft/proposal process does not provide any added benefits.
