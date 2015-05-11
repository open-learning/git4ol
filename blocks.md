# Blocks

Learning blocks can conceptually be mapped to `git` objects in the following way:

- `git` repository ⇔ `course`
- `git` orphaned branch ⇔ `lesson`
- `git` commit ⇔ `activity`
  - `git` commit message ⇔ `activity` title and instructions
  - `git` commit tree and files ⇔ `activity` assets

## References

`git4ol` objects are available as `git` references (refs):

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

## `activity` blocks from `git`commits

`activity` blocks are the basic building blocks of `git4ol` where we use the `git` commit tree and files to serve the `acticvity` assets and parse the `git` commit message to read the activity title and instructions.

### Message

A `git` commit message is composed of two parts and can be viewed individually using `git log`:

To see the `git` commit message subject:

```shell
user@shell:~/git4ol$ git log -1 --format=%s markdown@0.0.0/step/1
Our first markdown file
```

To see the `git` commit message body:

```shell
user@shell:~/git4ol$ git log -1 --format=%b markdown@0.0.0/step/1
---
type: ide
files:
  - README.md
---

[Markdown](http://daringfireball.net/projects/markdown/) is a text-to-HTML conversion tool for web writers. Markdown allows you to write using an easy-to-read, easy-to-write plain text format, then convert it to structurally valid XHTML (or HTML).

Let's start by adding a `README.md` file with some mock content.
```

The common `git` commit message usually looks like so:

```
Title in one line

Details following empty line
```

A `git4ol` message extends this with [YAML](http://yaml.org/) [Front Matter](http://jekyllrb.com/docs/frontmatter/) and [markdown](http://daringfireball.net/projects/markdown/):

```
Title in one line

---
about: "front matter block following empty line"
---

# Details in [markdown](http://daringfireball.net/projects/markdown/) (optionally) following empty line
```
