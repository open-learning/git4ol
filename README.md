# `git4ol` : `git` for Open Learning

`git4ol` defines conventions for manipulating `git` objects for the purpose of open and distributed learning.

> **note**
>
> As we have not yet made our first publich draft available you can expect breaking changes all the time, don't be angry.

## Blocks

Learning blocks can conceptually be mapped to `git` objects in the following way:

- "course" ⇔ `git` repository
- "lesson" ⇔ `git` orphaned branch
- "step" and "challenge" ⇔ `git` commit

### Commits

A `git` commit has two parts that can further be mapped into blocks:

- "step assets" ⇔ `git` commit contents
- "step body" ⇔ `git` commit message

The formatting of the `git` commit message follows:

> **note**
>
> The *instructions* format is not covered in this guide (but the [`git4ol`](https://github.com/open-learning/git4ol) repository uses markdown)

```
Title as a one line

Instructions as multiple paragraphs
```

### Refs

Learning blocks are located using `git` refs:

- Lesson starting points are located using refs of the format `refs/{lesson}@{version}/head` where
  - `lesson` is the name of the lesson
  - `version` is a valid semver

- Lesson ending points are located using refs of the format `refs/{lesson}@{version}/tail` where
  - `lesson` is the name of the lesson
  - `version` is a valid semver

- Steps are located using refs of the format `refs/{lesson}@{version}/step/{n}` where
  - `lesson` is the name of the lesson
  - `version` is a valid semver
  - `n` is a positive number indicating the step number

- Challenges are located using refs of the format `refs/{lesson}@{version}/challenge/{challenge}` where
  - `lesson` is the name of the lesson
  - `version` is a valid semver
  - `challenge` is the name of the challenge

## Actors

These are the main actors and a quick summary of what their functions are:

- `author`: Creates learning materials and instructions
- `student`: Studies learning materials and submits assignments for review
- `teacher`: Reviews student assignments and certifies them if passed

## Further reading

From here you can continue reading the [guide](guide.md) or if you already have `git4ol` lessons you want to learn how to [publish](publishing.md) then just go ahead.
