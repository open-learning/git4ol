# GIT-Tutor

GIT-Tutor is a format for packaging and distributing learning material using git.

## Learning objects

- The analogy of a "course" is a git repository
- The analogy of a "lesson" is a git tag
- The analogy of a "step" is a git commit
- The analogy of a "stimulus" is a git commit message

Locating learning objects is done with git refs:

- Lessons are located using refs of the format `refs/tags/lesson-name@version` where
  - `lesson-name` is the name of the lesson
  - `version` is a valid semver

- Steps are located using refs of the format `refs/lesson-name@version/step/n` where
  - `lesson-name` is the name of the lesson
  - `version` is a valid semver
  - `n` is a positive number indicating the step number

## Authoring

When authoring lessons there are two flows to follow, first version or amend.

### Authoring a first version

To create a first version of a lesson we need to first create an orphaned branch:

```shell
author@shell:~/mock$ git checkout --orphan markdown
```

> In this example `markdown` is the name of our lesson

Now it's time to add steps to our lesson. Incrementally add commits to this branch with the commit message following this format:

```
Step title as a one line

Stimulus as multiple paragraphs
```

When all of the steps are added we need to create a tag containing the lesson name and version linked to the last commit in the lesson:

```shell
author@shell:~/mock$ git tag markdown@0.0.0
```
> We use `0.0.0` as our initial version. After this the rule of thumb is:
>
> - addition of steps increments `patch` number
> - modification of steps increments `minor` number
> - at the moment we reserve updates to `major` number

After this it's safe to (force) remove our WIP branch (after switching to the master branch):

```shell
author@shell:~/mock$ git checkout master
author@shell:~/mock$ git branch -D markdown
```

Now it's time to add step refs. Let's list the (imaginary) commits we just added using `git log`:

```shell
author@shell:~/mock$ git log --oneline --reverse markdown@0.0.0
e19c2e6 Our first markdown file
9da9f3c Paragraphs, Headers, Blockquotes
cf92883 Phrase Emphasis
```

To add corresponding step refs we use `git update-ref`:

```shell
author@shell:~/mock$ git update-ref refs/markdown@0.0.0/step/1 e19c2e6
author@shell:~/mock$ git update-ref refs/markdown@0.0.0/step/2 9da9f3c
author@shell:~/mock$ git update-ref refs/markdown@0.0.0/step/3 cf92883
```

After all the step refs are added you can confirm that it's all good using `git log` again:

```shell
author@shell:~/mock$ git log --oneline --reverse --decorate=full markdown@0.0.0
e19c2e6 (refs/markdown@0.0.0/step/1) Our first markdown file
9da9f3c (refs/markdown@0.0.0/step/2) Paragraphs, Headers, Blockquotes
cf92883 (tag: refs/tags/markdown@0.0.0, refs/markdown@0.0.0/step/3) Phrase Emphasis
```

The last thing to do is to make all of this available to the world:

```shell
author@shell:~/mock$ git push origin refs/tags/markdown@0.0.0 refs/markdown@0.0.0/*
```

## Assignments

Assignments are managed using git pull-requests.

The "challenge" part of an assignment is a git commit containing stimulus and potential artifacts needed to validate an assignment (like unit tests). A user starts an assignment by branching from the "challenge" commit and then start making changes to the code.

When all the unit tests pass the assignment is ready for "review". The review is initiated by senting a pull-request to whoever should review the code.

## Certification

A "certificate" is git commit containing a [mozilla open badge assertion](https://github.com/mozilla/openbadges-specification/blob/master/Assertion/latest.md) with evidence pointing to the original "assignment" commit.
