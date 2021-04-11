# Gitflow Branching Strategy

To define which branches produce which kinds of artifacts, this framework offers a minimal set of definitions and rules which sit somewhere in the middle of the [Gitflow Branching Model](https://nvie.com/posts/a-successful-git-branching-model/) and the [GitHub Flow Branching Model](https://guides.github.com/introduction/flow/).

![build-system-00](branching-strategy-00.svg)

In the figure above there are four types of branches, and only three of them produce artifacts:

## `feature/XYZ` branches

These branches can be used to extensively commit and test a new feature. They produce `SNAPSHOT` artifacts, which are always re-deployable by Maven. These branches have the highest commit frequency, and they SHOULD get squash-merged (and then quickly discarded) into the next type of branch.

## The `develop` branch

This is a single branch where many `feature/XYZ` branches converge into to produce `MILESTONE` artifacts, and contains something which a team is starting to get confident about releasing, but still requires more testing. The `develop` branch SHOULD get merged straight into `master/main` so that they are always even (0 commits ahead/0 commits behind).

This gives you certainty that `MILESTONE` artifacts originated from a well-know Git commit hash.

## The `master/main` branch

This branch MUST be the only branch from which `RELEASE` artifacts are derived, but does NOT itself produce such `RELEASE` artifacts. Instead, it acts as a `PRERELEASE` build stage where a Pull request from `develop` into `master/main` can get reviewed one last time. For example:

- Make sure that documentation is up to date.
- Perform release gating (i.e. make sure the right dependencies are getting imported).
- Determine official release version major, minor and patch number increments etc.).

## `tags/X.Y.Z`

Tags derived from the `master/main` branch freeze a commit in the `master/main` branch timeline, and produce `RELEASE` artifacts which only contain only major, minor and patch version numbers for the artifact.

> Note: while it MAY seem that this model imposes excessive burden on branch management, it's also possible to discard the `develop` branch entirely, and only work with `feature/xyz` branches which merge directly into `master/main` since the only important point where `RELEASE` artifacts are created are tags: `tag/X.Y.Z`.

One applicable use case for dropping the use of the `DEVELOP` branch could be where a single developer is working on multiple code bases, so it's no necessary to impose the level of control that the `develop` branch offers.

With these concepts, we can now define how to configure Gradle and Github Actions to implement this framework.