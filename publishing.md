# `git4ol` Publishing

> ***This document is under heavy development as we're currently working on [open learning `read`](//github.com/open-learning/read) and that has impact on what you see here***

If you are publishing a website from a `git` repo and you have a `bash` shell around there are some tricks you can use to publish `git4ol` lessons.

## Fetch

Make sure to `git fetch` refs first:

> **note**
>
> In this example we're going to publish `activity` blocks from the lesson `markdown@0.0.0` from the remote `origin`

```shell
user@shell:~/git4ol$ git fetch origin refs/markdown@0.0.0/*:refs/markdown@0.0.0/*
```
## Assets

Generate `git subtree`s from refs:

> **note**
>
> In this example we're using the current repository `./` to reuse our current `origin`.

```shell
user@shell:~/git4ol$ for ref in $(git for-each-ref --format="%(refname)" refs/markdown@0.0.1); do git subtree add --message "published $ref" --message "published $ref" --prefix=$ref $ref; done
```

## Instructions

Generate `.md` file with the commit message from refs:

```shell
user@shell:~/git4ol$ for ref in $(git for-each-ref --format="%(refname)" refs/markdown@0.0.0); do git log -1 --format="---%nid: %H%ntitle: %s%n%b" $ref -- > $ref.md; done
```

## GitHub pages

If you intend to host your lesson using [GitHub pages](https://pages.github.com/) you should turn off [Jekyll](http://jekyllrb.com/) by [adding a `.nojekyll` file](https://help.github.com/articles/using-jekyll-with-pages/#turning-jekyll-off) to the root of your `gh-pages` branch.

> **note**
>
> If you need Jekyll for this site there may be ways to optionally turn this of per path, but I don't know how. Please PR if you do.

## Publish

Now all that is left is to `git add` and `git commit` our changes before finally doing a `git push` - but you can do that on your own. 
