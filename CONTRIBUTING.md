# Hubot Evolution Process

[all-proposals]: https://github.com/hubotio/evolution/projects/1
[chat]: https://hubot-slackin.herokuapp.com/

This document outlines how a Hubot feature grows from a rough idea into something that can improve workflows for millions of developers.

This process covers all changes to Hubot, such as new features or adding, changing, or removing any APIs (no matter how small). Smaller changes, such as bug fixes, optimizations, or other improvements can be contributed via the [normal contribution process](https://github/github/hubot/blob/master/CONTRIBUTING.md) on the relevant repository.

## Goals

This process aims to leverage the collective ideas, insights, and experience of the community to improve the Hubot project and ecosystem. Its two primary goals are:

* Engage the wider community in the ongoing evolution of Hubot, and
* Maintain the vision and conceptual coherence of Hubot.

There is a natural tension between these two goals. Open evolution processes are, by nature, chaotic. Yet, maintaining a coherent vision requires some level of coordination. This process aims to strike a balance that best serves the entire Hubot ecosystem.

## Participation

Everyone is welcome to propose, discuss, and review ideas to improve Hubot.

The Hubot maintainers are responsible for the strategic direction of Hubot. Maintainers initiate, participate in, and manage the public review of proposals and have the authority to accept or reject changes to Hubot.

## How to propose a change

1. **Check prior proposals**: many ideas come up frequently, and may either be in active discussion, or may have been discussed and rejected. Please check the [list of proposals][all-proposals] before proposing something new.
1. **Consider the goals of the upcoming release**: Each major release is focused on a [specific set of goals](README.md) described early in the release cycle. When proposing a change, please consider how your proposal fits in with the larger goals of the upcoming release. Proposals that are out of scope may still be considered, but will likely be postponed.
1. **Socialize the idea**: Before starting the review process, it's helpful to gauge interest from the community. Ideally, the idea would have come up in a discussion in another issue or in [chat][] and the consensus was to start an evolution proposal.
1. **Develop the proposal**: open a Pull Request with a rough sketch of the proposal using the [proposal template](template.md) in the [_drafts](https://github.com/hubotio/evolution/new/master/_drafts) directory. Prototyping an implementation and its uses along with the proposal is encouraged, because it helps ensure both technical feasibility of the proposal as well as validating that the proposal solves the problems it is meant to solve.
1. **Request a review**: When you are ready for feedback from the Hubot community, change the frontmatter in your proposal from `state: In Progress` to `state: In Review`.
1. **Address feedback**: be responsive to questions and feedback about the proposal. When the proposal is sufficiently detailed and clear, and addresses feedback from earlier discussions of the idea, the `accepted` label will be added and a maintainer will be assigned to manage the review.

## Review process

When you change the frontmatter in your proposal from `state: In Progress` to `state: In Review`, a message will be posted in the [#evolution channel in chat][chat] requesting feedback from the community.

Reviews should usually be completed in a single week, but can run longer for particularly large or complex proposals. To avoid delays, it is important that the proposal authors be available to answer questions, address feedback, and clarify their intent during the review period.

After feedback from the community has been gathered, the maintainers will make a decision on whether to `accept` or `reject` the proposal.

Once a proposal has been accepted, the file should be renamed to `proposals/###-feature-name.md`, where `###` is the Pull Request number. It is usually up to the proposal author to implement it or discuss with the maintainers who will lead the implementation.

Once a proposal has been implemented, the Pull Request will be merged.

## Proposal states

A proposal can be in one of several states:

* **In Progress**: The authors are still working on the proposal and are not yet ready for review.
* **In Review**: The proposal is ready for review and comments from the community.
* **Withdrawn**: The proposal has been withdrawn by the original submitter.
* **Deferred**: Consideration of the proposal has been deferred because it does not meet the [goals of the upcoming release](README.md). Deferred proposals will be reconsidered when scoping the next major release.
* **Accepted**: The proposal has been accepted and is either awaiting implementation or is actively being implemented.
* **Rejected**: The proposal has been considered and rejected.
* **Implemented**: The proposal has been implemented. Add the `version` to the frontmatter of the proposal and merge the Pull Request.
