# GIT-Tutor

GIT-Tutor is a format for packaging and distributing learning material using git.

## Learning objects

- The analogy of a "course" is a git repository
- The analogy of a "lesson" is a git tag
- The analogy of a "step" is a git commit
- The analogy of a "stimulus" is a git commit message

Locating learning objects is done with git refs:

- Lessons are located using refs of the format `refs/tags/lesson-name@version` where
  - `lesson-name` is the name of the lesson
  - `version` is a valid semver

- Steps are located using refs of the format `refs/lesson-name@version/step/n` where
  - `lesson-name` is the name of the lesson
  - `version` is a valid semver
  - `n` is a positive number indicating the step number

## Authoring

When authoring lessons we are either authoring the first version or an amended version.

### Authoring the first version of a lesson

To create a first version of a lesson we need to first create an orphaned branch:

```shell
author@shell:~/mock$ git checkout --orphan markdown
```

> In this example `markdown` is the name of our lesson

Now it's time to add steps to our lesson. Incrementally add commits to this branch with the commit message following this format:

```
Step title as a one line

Stimulus as multiple paragraphs
```

When all of the steps are added we need to create a tag containing the lesson name and version linked to the last commit in the lesson:

```shell
author@shell:~/mock$ git tag markdown@0.0.0
```
> We use `0.0.0` as our initial version. After this the rule of thumb is:
>
> - addition of steps increments `patch` number
> - modification of steps increments `minor` number
> - at the moment we reserve updates to `major` number

After this it's safe to (force) remove our WIP branch (after switching to the master branch):

```shell
author@shell:~/mock$ git checkout master
author@shell:~/mock$ git branch -D markdown
```

Now it's time to add step refs. Let's list the (imaginary) commits we just added using `git log`:

```shell
author@shell:~/mock$ git log --oneline --reverse markdown@0.0.0
e19c2e6 Our first markdown file
9da9f3c Paragraphs, Headers, Blockquotes
cf92883 Phrase Emphasis
```

To add corresponding step refs we use `git update-ref`:

```shell
author@shell:~/mock$ git update-ref refs/markdown@0.0.0/step/1 e19c2e6
author@shell:~/mock$ git update-ref refs/markdown@0.0.0/step/2 9da9f3c
author@shell:~/mock$ git update-ref refs/markdown@0.0.0/step/3 cf92883
```

After all the step refs are added you can confirm that it's all good using `git log` again:

```shell
author@shell:~/mock$ git log --oneline --reverse --decorate=full markdown@0.0.0
e19c2e6 (refs/markdown@0.0.0/step/1) Our first markdown file
9da9f3c (refs/markdown@0.0.0/step/2) Paragraphs, Headers, Blockquotes
cf92883 (tag: refs/tags/markdown@0.0.0, refs/markdown@0.0.0/step/3) Phrase Emphasis
```

The last thing to do is to make all of this available to the world:

```shell
author@shell:~/mock$ git push origin refs/tags/markdown@0.0.0 refs/markdown@0.0.0/*
```

### Authoring an amended version of a lesson

When authoring an amended version of a lesson we start by checking out the version of the lesson we're about to amend into a WIP branch:

```shell
author@shell:~/mock$ git checkout -b markdown markdown@0.0.0 
Switched to a new branch 'markdown'
```

In this ammendment we're going to fix some formatting error(s) in `refs/markdown@0.0.0/step/2` and onwards so we'll start an interactive rebase from there.

```shell
author@shell:~/mock$ git rebase --interactive markdown@0.0.0/step/2
```

> This will only work if the refs for `markdown@0.0.0/*` are fetched first. You can do this using `git fetch`:
>
> ```shell
> author@shell:~/mock$ git fetch origin refs/markdown@0.0.0/*:refs/markdown@0.0.0/*
>```

Which let's us interactively choose what commits we need to edit via a text ui. Change the `pick` to `edit` on the faulty commits and save.

```
edit cf92883 Phrase Emphasis
edit ae23fe0 Unordered  Lists
edit c09b157 Ordered  Lists
edit 764dfbc Complex lists
edit ffa19d5 Inline Links
edit ea8ac14 Inline Links with a Title
edit b330a00 Reference Links
edit 09b686c Reference Links with options
edit 79c24c8 Images
edit 2f264bc Inline Code
edit 2a4396c Block Code

# Rebase 9da9f3c..2a4396c onto 9da9f3c (11 command(s))
```

This will drop us to the command line with this message:

```shell
author@shell:~/mock$ git rebase --interactive markdown@0.0.0/step/3
Stopped at cf92883cb505c35b0760956bf5bffc1f8879af60... Phrase Emphasis
You can amend the commit now, with

        git commit --amend 

Once you are satisfied with your changes, run

        git rebase --continue
```

We're going to update `README.md` and fix the indentation error in the end of the file, save it, `git add` the changes and then `git rebase --continue`

```shell
author@shell:~/mock$ git add -A
author@shell:~/mock$ git rebase --continue 
[detached HEAD a962c8a] Phrase Emphasis
 Date: Thu Apr 16 18:50:48 2015 +0800
 1 file changed, 6 insertions(+)
error: could not apply ae23fe0... Unordered  Lists

When you have resolved this problem, run "git rebase --continue".
If you prefer to skip this patch, run "git rebase --skip" instead.
To check out the original branch and stop rebasing, run "git rebase --abort".
Could not apply ae23fe00f8e0645eda0c42f015d026b98b25b047... Unordered  Lists
```

Since the changes we've done to `README.md` collide with later changes we'll have to solve the conflict.

```shell
author@shell:~/mock$ git mergetool
```

> [`git mergetool`](http://www.git-scm.com/docs/git-mergetool) is not the only way conflicts can be solved, but it is usually the easiest one (if you have a `mergetool` configured)

Solve the conflict, apply the needed changes (indentation) then continue the rebase using `git rebase --continue`:

```shell
author@shell:~/mock$ git rebase --continue 
[detached HEAD 44298db] Unordered  Lists
 1 file changed, 12 insertions(+)
error: could not apply c09b157... Ordered  Lists

When you have resolved this problem, run "git rebase --continue".
If you prefer to skip this patch, run "git rebase --skip" instead.
To check out the original branch and stop rebasing, run "git rebase --abort".
Could not apply c09b157b990773d898e77d9f79b5ff84bd06cc43... Ordered  Lists
```

> Keep resolving conflicts and applying changes until the end of the rebase is reached.

Once the rebase is completed your new lesson is done! From here it's the same as authoring a new version, basically add refs for `refs/tags/markdown@0.1.0`, `refs/markdown@0.1.0/step/*`, `git rm` WIP branch and `git push` to publish the new lesson.

> Note that the new version is `0.1.0` since we published a *modified* lesson.

## Assignments

Assignments are managed using git pull-requests.

The "challenge" part of an assignment is a git commit containing stimulus and potential artifacts needed to validate an assignment (like unit tests). A user starts an assignment by branching from the "challenge" commit and then start making changes to the code.

When all the unit tests pass the assignment is ready for "review". The review is initiated by senting a pull-request to whoever should review the code.

## Certification

A "certificate" is git commit containing a [mozilla open badge assertion](https://github.com/mozilla/openbadges-specification/blob/master/Assertion/latest.md) with evidence pointing to the original "assignment" commit.
