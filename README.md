# GIT-Tutor

[![Join the chat at https://gitter.im/git-tutor/format](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/git-tutor/format?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

GIT-Tutor is a format for packaging and distributing learning material using git.

# Repository layout

It helps to mentaly map learning objects to git objects, so let's start with a short list:

## Learning objects

- The analogy of a "course" is a git repository
- The analogy of a "lesson" is a git branch
- The analogy of a "step" is a git commit
- The analogy of a "stimulus" is a git commit message

The idea is to use git refs to locate the variuos learning object.

- Lessons are located using annotated tags of the format `lesson-name@version` where
  - `lesson-name` is the name of the lesson
  - `version` is a valid semver

- Steps are located using refs of the format `lesson-name@version/step/n` where
  - `lesson-name` is the name of the lesson
  - `version` is a valid semver
  - `n` is a positive number indicating the step number

## Assignments

Assignments are managed using git pull-requests.

The "challenge" part of an assignment is a git commit containing stimulus and potential artifacts needed to validate an assignment (like unit tests). A user starts an assignment by branching from the "challenge" commit and then start making changes to the code.

When all the unit tests pass the assignment is ready for "review". The review is initiated by senting a pull-request to whoever should review the code.

## Certification

A "certificate" is git commit containing a [mozilla open badge assertion](https://github.com/mozilla/openbadges-specification/blob/master/Assertion/latest.md) with evidence pointing to the original assignment.

# Mock repository

Let's imagine a fictive course with three lessons. These are the branches available in the repo:

```
.
├── amd-modules
├── javascript-object-composition
└── javascript-promises-a-plus
```
