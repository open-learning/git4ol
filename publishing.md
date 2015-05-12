## `git4ol` Publishing

> ***This document is under heavy development as we're currently working on [open learning `read`](//github.com/open-learning/read) and that has impact on what you see here***

If you are publishing a website from a `git` repo and you have a `bash` shell around there are some tricks you can use to publish `git4ol` lessons.

Make sure to `git fetch` refs first:

> **note**
>
> In this example we're going to publish `activity` blocks from the lesson `markdown@0.0.0` from the remote `origin`

```shell
user@shell:~/git4ol$ git fetch origin refs/markdown@0.0.0/*:refs/markdown@0.0.0/*
```

Generate `git subtree`s from refs:

> **note**
>
> In this example we're using the current repository `./` to reuse our current `origin`.

```shell
user@shell:~/git4ol$ for ref in $(git for-each-ref --format="%(refname)" refs/markdown@0.0.0); do git subtree add --prefix $ref ./ $ref; done
```

Generate `.txt` file with the commit message from refs:

```shell
user@shell:~/git4ol$ for ref in $(git for-each-ref --format="%(refname)" refs/markdown@0.0.0); do git log -1 --format="---%nsubject: %s%nsha: %H%n%b" $ref -- > $ref.txt; done
```

Now all that is left is to `git add` and `git commit` our changes before finally doing a `git push` - but you can do that on your own. 
