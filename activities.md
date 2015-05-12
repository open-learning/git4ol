# `git4ol` Activities

At it's core a learning experience can be thought of as a guided set of activities a student takes to eventually get certified on a defined topic.

In `git4ol` we translate that experience to `git` commits and refs and the same way that `git` commits are the main building blocks of a `git` repository activities are the main building blocks of a `git4ol` lesson.

## Assets

Activity assets are located in directories according to this naming scheme:

- Step assets are located in directories of the format `refs/{lesson}@{version}/step/{n}` where
  - `lesson` is the name of the lesson
  - `version` is a valid semver
  - `n` is a positive number indicating the step number

- Challenge assets are located in directories of the format `refs/{lesson}@{version}/challenge/{challenge}` where
  - `lesson` is the name of the lesson
  - `version` is a valid semver
  - `challenge` is the name of the challenge

### Markdown

***Describe the `.md` format***

### JSON

***Describe the `.json` format***
