# Blocks

Learning blocks can conceptually be mapped to `git` objects in the following way:

- `git` repository ⇔ `course`
- `git` orphaned branch ⇔ `lesson`
- `git` commit ⇔ `activity`
  - `git` commit message ⇔ `activity` title and instructions
  - `git` commit contents ⇔ `activity` assets

## References

Blocks that map to `git` commits are located using `git` references (refs):

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

## Message

The common `git` commit message usually looks like so:

```
Title in one line

Details following empty line
```

A `git4ol` message extends this with [YAML](http://yaml.org/) [Front Matter](http://jekyllrb.com/docs/frontmatter/) and [markdown](http://daringfireball.net/projects/markdown/):

```
Title in one line

---
about: "front=matter block following empty line"
---

# Details in [markdown](http://daringfireball.net/projects/markdown/) (optionally) following empty line
```
