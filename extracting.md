# `git4ol` Extracting

A packaged `git4ol` repository itself really just a regular `git` repository with commits and refs following the `git4ol` conventions. We conceptually map learning objects to to `git` objects in the following way:

- `git` repository ⇔ course
- `git` orphaned branch ⇔ lesson
- `git` commit ⇔ activity
  - `git` commit tree and blobs ⇔ activity assets
  - `git` commit message ⇔ activity title and instructions

## References

We locate activities using `git` references (refs) according to this naming scheme:

- Steps are located using refs of the format `refs/{lesson}@{version}/step/{n}` where
  - `lesson` is the name of the lesson
  - `version` is a valid semver
  - `n` is a positive number indicating the step number

- Challenges are located using refs of the format `refs/{lesson}@{version}/challenge/{challenge}` where
  - `lesson` is the name of the lesson
  - `version` is a valid semver
  - `challenge` is the name of the challenge

## Activity assets, title and instructions

Activity assets are simply a snapshot of the tree at the `git` commit the activity refs.

The activity title and instructions are serialized in the `git` commit message. Your average `git` commit message is composed of two parts - subject and body.

The recomended format for a `git` commit message is:

```
Subject in one line

Body following empty line
```

A `git4ol` message extends this with [YAML](http://yaml.org/) and [markdown](http://daringfireball.net/projects/markdown/):

```
Subject in one line

about: "yaml following empty line"
---

# Body in [markdown](http://daringfireball.net/projects/markdown/) following `---` and (optional) empty line
```

To see the full message of a `git` commit use `git log`:

```shell
user@shell:~/git4ol$ git log -1 --format=%B markdown@0.0.0/step/1
Our first markdown file

type: ide
files:
  - README.md
---

[Markdown](http://daringfireball.net/projects/markdown/) is a text-to-HTML conversion tool for web writers. Markdown allows you to write using an easy-to-read, easy-to-write plain text format, then convert it to structurally valid XHTML (or HTML).

Let's start by adding a `README.md` file with some mock content.
```

To only see the `git` commit message subject:

```shell
user@shell:~/git4ol$ git log -1 --format=%s markdown@0.0.0/step/1
Our first markdown file
```

To only see the `git` commit message body:

```shell
user@shell:~/git4ol$ git log -1 --format=%b markdown@0.0.0/step/1
type: ide
files:
  - README.md
---

[Markdown](http://daringfireball.net/projects/markdown/) is a text-to-HTML conversion tool for web writers. Markdown allows you to write using an easy-to-read, easy-to-write plain text format, then convert it to structurally valid XHTML (or HTML).

Let's start by adding a `README.md` file with some mock content.
```

To format the `git` message like [Jekyll](http://jekyllrb.com/) content we can actually get away with only using `git log`:

```shell
git log -1 --format="---%nid: %H%n%ntitle: %s%nnotes:%N%n%b" markdown@0.0.0/step/1
---
id: 1158e62f02de11ddb51609a9efc8bebe655e1017
title: Our first markdown file
notes:
type: ide
files:
  - README.md
---

[Markdown](http://daringfireball.net/projects/markdown/) is a text-to-HTML conversion tool for web writers. Markdown allows you to write using an easy-to-read, easy-to-write plain text format

Let's start by adding a `README.md` file with some mock content.
```
