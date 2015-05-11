# `git4ol` : `git` for Open Learning

`git4ol` defines conventions for manipulating `git` objects for the purpose of open and distributed learning.

> **note**
>
> As we have not yet made our first publich draft available you can expect breaking changes all the time, don't be angry.

## Blocks

Learning blocks can conceptually be mapped to `git` objects in the following way:

- `git` repository ⇔ `course`
- `git` orphaned branch ⇔ `lesson`
- `git` commit ⇔ `activity`

### Commits

A `git` commit has two parts that can further be mapped into blocks:

- `git` commit contents ⇔ `assets`
- `git` commit message ⇔ `body` 

Where `body` follows the following format:

```
Title as a one line

Instructions as multiple lines
```

### References

Learning blocks are located using `git` references (refs):

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

People wanted get a deeper understading of `git4ol` also read:

- The [`git4ol` guide](guide.md) takes the reader from authoring to certification
- The [`git4ol` instructions](instructions.md) page teaches the reader how to format your commit messages
- The [`git4ol` publishing](publishing.md) page teaches the reader how to publish `git4ol` lessons
