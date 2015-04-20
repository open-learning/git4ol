# Open Learning specification

The `open-learning` specification specifies conventions for manipulating git objects for the purpose of open learning.

## Roles

- Author
- Student
- Teacher
- Certifier

## Learning objects

- "course" => git repository
- "lesson" => git tag
- "step" => git commit
- "instructions" => git commit message

> **note**
>
> - Steps can be of (multiple) arbitary type(s) ex: `assigmnet`.
> - The format of instructions is not covered in this spec (but the [`mock`](https://github.com/open-learning/mock) repository uses markdown)

Locating learning objects is done with git refs:

- Lessons are located using refs of the format `refs/tags/lesson-name@version` where
  - `lesson-name` is the name of the lesson
  - `version` is a valid semver

- Steps are located using refs of the format `refs/lesson-name@version/step/n` where
  - `lesson-name` is the name of the lesson
  - `version` is a valid semver
  - `n` is a positive number indicating the step number

- Assignments are located using refs of the format `refs/lesson-name@version/assignment/n` where
  - `lesson-name` is the name of the lesson
  - `version` is a valid semver
  - `n` is a positive number indicating the assignment number

## Authoring

> **role**
>
> This part of the spec assumes the actor is an ***author***

Creating lessons is as basic as adding commits to a branch. It's mostly when we need to modify a lesson it gets a little bit more complicated.

### Creating a lesson

To create the initial version of a lesson we need to first create an orphaned WIP branch:

```shell
author@shell:~/mock$ git checkout --orphan markdown
```

> **note**
>
> In this example `markdown` is the name of our WIP branch

Now it's time to add steps to our lesson. Incrementally add commits to this branch with the commit message following this format:

```
Step title as a one line

Instructions as multiple paragraphs
```

When all of the steps are added we need to create a tag containing the lesson name and version from the `HEAD` of the branch:

```shell
author@shell:~/mock$ git tag markdown@0.0.0
```
> **note**
>
> We use `0.0.0` as our initial version. After this the rule of thumb is:
>
> - addition of steps increments `patch` number
> - modification of steps increments `minor` number
> - at the moment we reserve updates to `major` number

After this it's safe to (force) remove our WIP branch (after switching another branch, in this case the `master` branch):

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

### Modifying a lesson

When modifying a lesson we start by checking out the `tag` of the lesson we're about to amend into a WIP branch:

```shell
author@shell:~/mock$ git checkout -b markdown markdown@0.0.0 
Switched to a new branch 'markdown'
```

In this ammendment we're going to fix some formatting error(s) in `refs/markdown@0.0.0/step/2` and in the steps onwards so we'll start an interactive `rebase` from that `ref`.

```shell
author@shell:~/mock$ git rebase --interactive markdown@0.0.0/step/2
```

> **note**
>
> This will only work if the `refs` for `markdown@0.0.0/*` are fetched first. You can do this using `git fetch`:
>
> ```shell
> author@shell:~/mock$ git fetch origin refs/markdown@0.0.0/*:refs/markdown@0.0.0/*
>```

Which let's us interactively choose what commits we need to edit via a text ui. Change the `pick` to `edit` on the commits that need editing and save.

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

This will drop us to back to the shell:

```shell
author@shell:~/mock$ git rebase --interactive markdown@0.0.0/step/3
Stopped at cf92883cb505c35b0760956bf5bffc1f8879af60... Phrase Emphasis
You can amend the commit now, with

        git commit --amend 

Once you are satisfied with your changes, run

        git rebase --continue
```

We're going to update `README.md` and fix the indentation error in the end of the file, save it, `git add` the changes and then `git rebase --continue`:

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

> **note**
>
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

Keep resolving conflicts and applying changes until the end of the rebase is reached.

Once the rebase is completed your new lesson is ready to be publieshed! From here it's the same as when authoring a new lesson, basically:

- `git tag markdown@0.1.0`
- `git checkout master` and `git branch -D markdown`
- `git update-ref refs/markdown@0.1.0/step/{step} {commit}`
- `git push origin refs/tags/markdown@0.1.0 refs/markdown@0.1.0/*`

> **note**
>
> Note that the new version is `0.1.0` since we are publishing a *modified* version of `0.0.0`.

## Studying

> **role**
>
> This part of the spec assumes the actor is a ***student***

Depending on the lesson studying can be done either entierly by the student alone (in the case of self assesed or machine assesed assigments) or with a teacher in the case of a teacher assesed assignments. The basic difference is the need to push your work to a remote repository for review.

### Initialization

Before we can study we have to set up and initialize some infrastructure. Let's start by cloning the [`mock`](https://github.com/open-learning/mock/) repository.

```shell
student@shell:~/$ git clone https://github.com/open-learning/mock.git
Cloning into 'mock'...
remote: Counting objects: 132, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 132 (delta 0), reused 0 (delta 0), pack-reused 129
Receiving objects: 100% (132/132), 14.31 KiB | 0 bytes/s, done.
Resolving deltas: 100% (48/48), done.
Checking connectivity... done
student@shell:~/$ cd mock/
student@shell:~/mock$ 
```

After this we can list the available lessons by using `git tag`:

```shell
student@shell:~/mock$ git tag
markdown@0.0.0
markdown@0.1.0
markdown@0.2.0
markdown@0.3.0
```

We want to learn some markdown, so we need to update our meta-information about this lesson using `git fetch`:

```shell
student@shell:~/mock$ git fetch origin refs/markdown@0.3.0/*:refs/markdown@0.3.0/*
From https://github.com/open-learning/mock
 * [new ref]         refs/markdown@0.3.0/assignment/1 -> refs/markdown@0.3.0/assignment/1
 * [new ref]         refs/markdown@0.3.0/assignment/2 -> refs/markdown@0.3.0/assignment/2
 * [new ref]         refs/markdown@0.3.0/assignment/3 -> refs/markdown@0.3.0/assignment/3
 * [new ref]         refs/markdown@0.3.0/step/1 -> refs/markdown@0.3.0/step/1
 * [new ref]         refs/markdown@0.3.0/step/10 -> refs/markdown@0.3.0/step/10
 * [new ref]         refs/markdown@0.3.0/step/11 -> refs/markdown@0.3.0/step/11
 * [new ref]         refs/markdown@0.3.0/step/12 -> refs/markdown@0.3.0/step/12
 * [new ref]         refs/markdown@0.3.0/step/13 -> refs/markdown@0.3.0/step/13
 * [new ref]         refs/markdown@0.3.0/step/14 -> refs/markdown@0.3.0/step/14
 * [new ref]         refs/markdown@0.3.0/step/15 -> refs/markdown@0.3.0/step/15
 * [new ref]         refs/markdown@0.3.0/step/16 -> refs/markdown@0.3.0/step/16
 * [new ref]         refs/markdown@0.3.0/step/2 -> refs/markdown@0.3.0/step/2
 * [new ref]         refs/markdown@0.3.0/step/3 -> refs/markdown@0.3.0/step/3
 * [new ref]         refs/markdown@0.3.0/step/4 -> refs/markdown@0.3.0/step/4
 * [new ref]         refs/markdown@0.3.0/step/5 -> refs/markdown@0.3.0/step/5
 * [new ref]         refs/markdown@0.3.0/step/6 -> refs/markdown@0.3.0/step/6
 * [new ref]         refs/markdown@0.3.0/step/7 -> refs/markdown@0.3.0/step/7
 * [new ref]         refs/markdown@0.3.0/step/8 -> refs/markdown@0.3.0/step/8
 * [new ref]         refs/markdown@0.3.0/step/9 -> refs/markdown@0.3.0/step/9
 ```

### Stepping

We study by steping through each lesson step by step:

```shell
student@shell:~/mock$ git checkout --force --detach markdown@0.3.0/step/1
Note: checking out 'markdown@0.3.0/step/1'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name

HEAD is now at e19c2e6... Our first markdown file
```

To display the instructions for this step we use `git log`:

```shell
student@shell:~/mock$ git log -1
commit e19c2e60b471dc271c0092a1ab750a3daadb0585
Author: Mikael Karon <mikael@karon.se>
Date:   Thu Apr 16 18:46:21 2015 +0800

    Our first markdown file
    
    [Markdown](http://daringfireball.net/projects/markdown/) is a text-to-HTML conversion tool for web writers. Markdown allows you to write using an easy-to-read, easy-to-write plain text form
    
    Let's start by adding a `README.md` file with some mock content.
```

> **note**
>
> For the purpose of this spec we'll just go ahead and ignore the actual contents of this lesson

Keep checking out each `markdown@0.3.0/step/{n}` to step through the lesson.

## Assignments

Assignments are just commits that contain assignment material and instructions.

We start an assignment by checking out the assignment ref to a detached `HEAD`:

```shell
student@shell:~/mock$ git checkout --detach markdown@0.3.0/assignment/1
Previous HEAD position was e19c2e6... Our first markdown file
HEAD is now at 5947ce8... Your first assignment
```

Let's look at the instuctions for this assignment using `git log`:

```shell
student@shell:~/mock$ git log -1
commit 5947ce87b20c0af4a6e3f4cb6611e8a15bda2ae3
Author: Mikael Karon <mikael@karon.se>
Date:   Mon Apr 20 11:08:29 2015 +0800

    Your first assignment
    
    Let's see what we've learned so far. We've created a file called `1.md` in the `assignment` folder that you should use to submit your answer.
    
    Add three headings from level one to three, pick whatever heading contents as you want.
```

Let's follow the instructions and add three lines of heading to `assignment/1.md` and then using `cat` to confirm our changes:

```shell
student@shell:~/mock$ echo "#1
##2
###3" > assignment/1.md 
student@shell:~/mock$ cat assignment/1.md 
#1
##2
###3
```

## Assesment

Assignments can be either self assesed, machine assesed or teacher assesed.

### Local assesment

In the case of self assesed tests the needed tests to validate an assignment (like unit tests) should be provided in the commit and assesment can be done localy on the client.

### Remote assesment

In the case of machine and teacher assesed assignments we think of the assignment in two parts:

- The "challenge" part of an assignment is a git commit containing instructions and learning materials
- The "response" part of an assignment is a pull request containing all the commits to be reviewed

### Advertisement

> **role**
>
> This part of the spec assumes the actor is a ***teacher***

Before an assignment assesment can be made someone has to advertise that they are available to review a speciffic assignment. This is done by publishing branches that pull-requests can be submitted against.

For example, if we wanted to advertise that we're able to review `refs/markdown@0.3.0/assignment/1` we create `refs/heads/markdown@0.3.0/assignment/1` from `refs/markdown@0.3.0/assignment/1`:

```shell
teacher@shell:~/mock$ git update-ref refs/heads/markdown@0.3.0/assignment/1 refs/markdown@0.3.0/assignment/1
```

After this we must publish our change to make it available to students using `git push`:

```shell
teacher@shell:~/mock$ git push origin refs/heads/markdown@0.3.0/assignment/*
Total 0 (delta 0), reused 0 (delta 0)
To https://github.com/open-learning/mock.git
 * [new branch]      markdown@0.3.0/assignment/1 -> markdown@0.3.0/assignment/1
```

> **note**
>
> The above command will push all the `heads` created for `markdown@0.3.0`

### Submission

> **role**
>
> This part of the spec assumes the actor is a ***student***

Before we submit this assignment we have to create a more permanent home fore it using `git checkout`:

```shell
git checkout -b markdown@0.3.0/assignment/1#first-attempt
M	assignment/1.md
Switched to a new branch 'markdown@0.3.0/assignment/1#first-attempt'
```

> **note**
>
> Here `first-attempt` is the name of our attempt (since it's our first attempt)

We want the last changes to be part of our assesment so let's add it to our commit using `git add`:

```shell
student@shell:~/mock$ git add assignment/1.md 
```

The assignment is now ready for "review". The review is initiated by senting a pull-request to whoever should review the assignment.

## Certification

A "certificate" is git commit containing a [mozilla open badge assertion](https://github.com/mozilla/openbadges-specification/blob/master/Assertion/latest.md) with evidence pointing to the original "assignment" commit.
