## Quick publishing

If you are publishing a website from a `git` repo and you have a `bash` shell around there are some tricks you can use to publish `git4ol` lessons.

> **note**
>
> All refs that will be added as `git submodule`s have to be reachable frome either refs `refs/tags/*` or `refs/heads/*`

Make sure to `git fetch` `refs/{lesson@version}/step/*` refs first

> **note**
>
> In this example we're going to publish the lesson `markdown@0.0.0`

```shell
user@shell:~/git4ol$ git fetch origin refs/markdown@0.0.0/step/*:refs/markdown@0.0.0/step/*
```

Generate `git submodule`s from refs:

```shell
user@shell:~/git4ol$ for ref in $(git for-each-ref --format="%(refname)" refs/markdown@0.0.0/step); do git submodule add --force ./ $ref; done
```

Checkout correct ref for each `git submodule`:

> **note**
>
> This will try to `git checkout1 *all* `git submodule`s

```shell
user@shell:~/git4ol$ git submodule foreach 'git fetch origin $name && git checkout FETCH_HEAD'
```

Generate `txt` file with the commit message for each `git submodule`:

```shell
user@shell:~/git4ol$ git submodule foreach 'git log -1 --format="%B" > $toplevel/$path.txt'
```
