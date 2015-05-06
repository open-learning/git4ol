# `git` for Open Learning

`git4ol` defines conventions for manipulating `git` objects for the purpose of open and distributed learning.

> **note**
>
> As we have not yet made our first publich draft available you can expect breaking changes all the time, don't be angry.

## Learning objects

Learning objects can conceptually be mapped to `git` objects in the following way:

> **note**
>
> The format of instructions is not covered in this guide (but the [`git4ol`](https://github.com/open-learning/git4ol) repository uses markdown)

- "course" ⇔ `git` repository
- "lesson" ⇔ `git` orphaned branch
- "step" and "assignment" ⇔ `git` commit
  - "assets" ⇔ `git` commit contents
  - "instructions" ⇔ `git` commit message

Learning objects are managed with `git` refs:

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

- Assignments are located using refs of the format `refs/{lesson}@{version}/assignment/{n}` where
  - `lesson` is the name of the lesson
  - `version` is a valid semver
  - `n` is a positive number indicating the assignment number

## Actors

These are the main actors and a quick summary of what their functions are:

- `author`: Creates learning materials and instructions
- `student`: Studies learning materials and submits assignments for review
- `teacher`: Reviews student assignments and certifies them if passed

> **note**
>
> In this document you'll be able to see what the active role is in two ways:
>
> - Actor changes are noteted like so: **actor: xxx**
> - When a shell command is issued check the format of the prompt:
>
>```shell
>actor@shell:cwd$
>```

## Further reading

From here you can continue reading the [guide](guide.md) or if you already have `git4ol` lessons you want to [publishing](publishing.md) we have some documentation for that to.
