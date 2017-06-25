---
state: In Review
---

# Set up release workflow with `semantic-release`

`semantic-release` makes releasing faster, simpler and safer. All of these are valuable if we want to increase public participation in the project and increase momentum and motivation.

## Motivation

Releasing a package is always a bit of a Big Thing™: decisions have to be made about versioning, an error-prone manual release process has to be performed, and somebody has to actually do the work and take the responsibility if something goes wrong. So there are a few barriers to frequent releases, and  many potential sources of error. Automating this process takes away all the uncertainty and hassle out of releasing, makes our version numbers more consistent and reliable, and increases release frequency and project momentum. It also does away with the concept of intentional, manual releases: whenever something lands in master and passes all the tests, `semantic-release` will package an npm release. If we do need to keep control of release timing for marketing reasons, `semantic-release` supports that too.

## Proposed solution

Set up `semantic-release` on `hubotio/hubot` and let it release the npm module for us automatically and continuously.

## Detailed design

An admin/owner [sets up](https://github.com/semantic-release/semantic-release#setup) [semantic-release](https://github.com/semantic-release/semantic-release) on `hubotio/hubot` using `semantic-release-cli setup`. Since we’ve already got tests and Travis set up, this shouldn’t require any additional steps.

We should update `CONTRIBUTING.md`:
- include more details about our [expected commit message format](https://github.com/semantic-release/semantic-release#default-commit-message-format), and why this is important
- remove the instructions for manual releases and replace them with info about `semantic-release`, how it works and why we’re using it.

Manually keeping the `CHANGELOG.md` up to date no longer necessary, since `semantic-release` automatically adds each release’s changelog to `https://github.com/hubotio/hubot/releases`. We can remove it from the repo, but in the release in which we remove it, we can add a link to the `CHANGELOG.md` in the last tag that still has it, for reference.

## Backward compatibility

Major version numbers will be incremented more frequently, since `semantic-release` is very strict about semantic versioning. This will lead to problems with the existing Hubot Generator script, since that has limited the version range for Hubot to `^2.x`. However, this is already being [discussed](https://github.com/hubotio/hubot/issues/1057) and worked on.

## Alternatives considered

There are no obvious alternatives to `semantic-release`, but we did consider adding additional tooling to improve consistency and quality of commit messages:
- [commitizen](http://commitizen.github.io/cz-cli/) to help new/less experienced contributors with their commit messages
- [validate-commit-msg](https://github.com/kentcdodds/validate-commit-msg) to enforce commit message formats

We’ve decided against this for now to reduce complexity, but will keep an eye on commit message quality with regards to the new automated release process and revisit these options if neccessary.
