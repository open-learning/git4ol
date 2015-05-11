## `git4ol` Publishing

---

> **note**
>
> This document is under heavy development as we're currently working on [open learning `read`](//github.com/open-learning/read) and that has impact on what you see here

---

If you are publishing a website from a `git` repo and you have a `bash` shell around there are some tricks you can use to publish `git4ol` lessons.

> **note**
>
> All refs that will be added as `git submodule`s have to be reachable from either refs `refs/tags/*` or `refs/heads/*` if you want a regular `git submodule update --init` to work

Make sure to `git fetch` refs first:

> **note**
>
> In this example we're going to publish the lesson `markdown@0.0.0` from the remote `origin`

```shell
user@shell:~/git4ol$ git fetch origin refs/markdown@0.0.0/step/*:refs/markdown@0.0.0/step/*
```

Generate `git submodule`s from refs:

> **note**
>
> In this example we're using the current repository `./` to reuse our current `origin`. Replace if this is not what you want

```shell
user@shell:~/git4ol$ for ref in $(git for-each-ref --format="%(refname)" refs/markdown@0.0.0/step); do git submodule add --force ./ $ref; done
```

Checkout correct ref for each `git submodule`:

```shell
user@shell:~/git4ol$ git submodule foreach 'if [[ "$name" == refs/markdown@0.0.0/step/* ]]; then git fetch origin $name && git checkout FETCH_HEAD; fi'
```

Generate `txt` file with the commit message for each `git submodule`:

```shell
user@shell:~/git4ol$ git submodule foreach 'if [[ "$name" == refs/markdown@0.0.0/step/* ]]; then git log -1 --format=%B > $toplevel/$path.txt; fi'
```

Now all that is left is to `git add` and `git commit` our changes before finally doing a `git push` - but you can do that on your own. 
