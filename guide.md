# Open Learning specification

The `open-learning` specification specifies conventions for manipulating `git` objects for the purpose of open and distributed learning.

> **note**
>
> As we have not yet made our first publich draft available you can expect breaking changes all the time, don't be angry.

## Introduction

The document is written as a guide that takes the reader through the various actions taken by our actors from the beginning when an author creates his initial work to when a student accepts a certificate for an assignment.

We've chosen this format for two reasons:

- To lower the technical barrier of entry to anyone who understand basic `git` commands
- To provide a step-by-step guide that encompasses all of the core aspects of the specification  

## Prerequisites

Before continuing we should go over some basic `git` commands. First let's decide if we're starting from an empty repository or a clone.

> **note**
>
> We've used the repository url `https://github.com/open-learning/logos.git` in these examples, substitute with your own.

### Initialize

If you are starting from an empty repository you'll first have to initialize the new repository using `git init`:

```shell
user@shell:~/$ git init logos
Initialized empty Git repository in /home/user/logos/.git/
user@shell:~/$ cd logos
user@shell:~/logos$ 
```

Once we have our initialized repository we should `git remote add` our `origin` repository url:

```shell
user@shell:~/logos$ git remote add origin https://github.com/open-learning/logos.git
```

### Clone

If we're starting from an existing repository we need clone it using `git clone`:

```shell
user@shell:~/$ git clone https://github.com/open-learning/logos.git
Cloning into 'logos'...
remote: Counting objects: 132, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 132 (delta 0), reused 0 (delta 0), pack-reused 129
Receiving objects: 100% (132/132), 14.31 KiB | 0 bytes/s, done.
Resolving deltas: 100% (48/48), done.
Checking connectivity... done
user@shell:~/$ cd logos/
user@shell:~/logos$ 
```

### References

By default `git` won't fetch non-standard references (refs) so we have to `git fetch` them ourselves. First let's fetch the lesson refs:

```shell
user@shell:~/logos$ git fetch origin refs/lessons/*:refs/lessons/*
From https://github.com/open-learning/logos
 * [new ref]         refs/lessons/markdown@0.0.0 -> refs/lessons/markdown@0.0.0
 * [new ref]         refs/lessons/markdown@0.0.1 -> refs/lessons/markdown@0.0.1
```

If you want to list lesson refs you can use `git for-each-ref` to do that:

```shell
user@shell:~/logos$ git for-each-ref refs/lessons/*
2a4396cad8752cfb315ab1d0443e96f12f7306af commit	refs/lessons/markdown@0.0.0
d6c00f1c5e9cc4ab08dd1b30b71e00d233418a5c commit	refs/lessons/markdown@0.0.1
```

Individual lesson refs can be fetched from `refs/lesson@version/*` like so:

```shell
user@shell:~/logos$ git fetch origin refs/markdown@0.0.1/*:refs/markdown@0.0.1/*
From https://github.com/open-learning/logos
 * [new ref]         refs/markdown@0.0.1/assignment/1 -> refs/markdown@0.0.1/assignment/1
 * [new ref]         refs/markdown@0.0.1/step/1 -> refs/markdown@0.0.1/step/1
 * [new ref]         refs/markdown@0.0.1/step/10 -> refs/markdown@0.0.1/step/10
 * [new ref]         refs/markdown@0.0.1/step/11 -> refs/markdown@0.0.1/step/11
 * [new ref]         refs/markdown@0.0.1/step/12 -> refs/markdown@0.0.1/step/12
 * [new ref]         refs/markdown@0.0.1/step/13 -> refs/markdown@0.0.1/step/13
 * [new ref]         refs/markdown@0.0.1/step/2 -> refs/markdown@0.0.1/step/2
 * [new ref]         refs/markdown@0.0.1/step/3 -> refs/markdown@0.0.1/step/3
 * [new ref]         refs/markdown@0.0.1/step/4 -> refs/markdown@0.0.1/step/4
 * [new ref]         refs/markdown@0.0.1/step/5 -> refs/markdown@0.0.1/step/5
 * [new ref]         refs/markdown@0.0.1/step/6 -> refs/markdown@0.0.1/step/6
 * [new ref]         refs/markdown@0.0.1/step/7 -> refs/markdown@0.0.1/step/7
 * [new ref]         refs/markdown@0.0.1/step/8 -> refs/markdown@0.0.1/step/8
 * [new ref]         refs/markdown@0.0.1/step/9 -> refs/markdown@0.0.1/step/9
```

And can be listed like so:

```shell
user@shell:~/logos$ git for-each-ref refs/markdown@0.0.1/**/*
5ab1c7ef10bb12fb35671fe8fbacf95671759f3b commit	refs/markdown@0.0.1/assignment/1
e19c2e60b471dc271c0092a1ab750a3daadb0585 commit	refs/markdown@0.0.1/step/1
9bdab965b2f2676459245eab8d9caf51a534e250 commit	refs/markdown@0.0.1/step/10
5249e88d7ca8d60fae8bce5d1ff5b79df3e486b3 commit	refs/markdown@0.0.1/step/11
e57dec6a0827f9ec13c88f064c59c4b7528dc879 commit	refs/markdown@0.0.1/step/12
d6c00f1c5e9cc4ab08dd1b30b71e00d233418a5c commit	refs/markdown@0.0.1/step/13
9da9f3c15a7dc0413a74308e44823d5ce6772b18 commit	refs/markdown@0.0.1/step/2
103eaeabcf01e59bc937eebe1180433360bb54d5 commit	refs/markdown@0.0.1/step/3
144a7565cd2b0a4f992295659e77607a46a3ecb8 commit	refs/markdown@0.0.1/step/4
5727a517cd85408c589e2953e5c00888775d973f commit	refs/markdown@0.0.1/step/5
b37244c1cd11e62693d247ff09c66fe107829a20 commit	refs/markdown@0.0.1/step/6
4e3bf8e84c4e9a2c492a9864badeb3561cce998e commit	refs/markdown@0.0.1/step/7
5d5faf3f55269d1c1c6cae0303441cb15b78907f commit	refs/markdown@0.0.1/step/8
977287260218c5cff6cebb8e3c5030c0e0a85a57 commit	refs/markdown@0.0.1/step/9
```

## Actors

These are the actors and a quick summary of what their main functions are:

- `author`: Creates learning materials and instructions
- `student`: Studies learning materials and submits assignments for review
- `teacher`: Reviews student assignments and certifies them if passed

> **note**
>
> Later in the document you'll be able to see what role a command is issued as by either tracking backwards and checking what the last **actor** note was or simply check the format of the prompt:
>
> ```shell
> actor@shell:cwd$
>```

## Learning objects

Learning objects can conceptually be mapped to `git` objects in the following way:

> **note**
>
> - `git` commits can be of arbitrary type ex: `step` or `assignment`
> - The format of instructions is not covered in this spec (but the [`logos`](https://github.com/open-learning/logos) repository uses markdown)

- "course" ⇔ `git` repository
- "lesson" ⇔ `git` orphaned branch
- "step" and "assignment" ⇔ `git` commit
  - "materials" ⇔ `git` commit contents
  - "instructions" ⇔ `git` commit message

Learning objects are managed with `git` refs:

- Lessons are located using refs of the format `refs/lessons/{lesson}@{version}` where
  - `lesson` is the name of the lesson
  - `version` is a valid semver

- Steps are located using refs of the format `refs/{lesson}@{version}/step/{n}` where
  - `lesson` is the name of the lesson
  - `version` is a valid semver
  - `n` is a positive number indicating the step number

- Assignments are located using refs of the format `refs/{lesson}@{version}/assignment/{n}` where
  - `lesson` is the name of the lesson
  - `version` is a valid semver
  - `n` is a positive number indicating the assignment number

## Authoring

> **actor: author**
>
> This part of the spec assumes the actor is an ***author***

Creating a lesson is as basic as adding commits to a branch. It's mostly when we need to modify one that it can get a little bit more complicated.

### Creating a lesson

To create the initial version of a lesson we need to first create a new directory and initialize an empty repository:

```shell
author@shell:~/$ mkdir logos
author@shell:~/$ cd logos
author@shell:~/logos$ git init
Initialized empty Git repository in /home/author/logos/.git/
```

Second step is to create an orphaned WIP branch:

> **note**
>
> In this example `markdown` is the name of our WIP branch

```shell
author@shell:~/logos$ git checkout --orphan markdown
```

Now it's time to add steps to our lesson. Incrementally add commits to this branch with the commit message following this format:

```
Step title as a one line

Instructions as multiple paragraphs
```

When all of the steps are added we need to create a new ref containing the lesson name and version pointing to the `HEAD` of the branch:

> **note**
>
> We use `0.0.0` as our initial version. After this we follow [semver 2.0.0](http://semver.org/spec/v2.0.0.html):
>
>> Given a version number MAJOR.MINOR.PATCH, increment the:
>>
>> 1. MAJOR version when you make incompatible API changes,
>> 1. MINOR version when you add functionality in a backwards-compatible manner, and
>> 1. PATCH version when you make backwards-compatible bug fixes.

```shell
author@shell:~/logos$ git update-ref refs/lessons/markdown@0.0.0 HEAD
```

Now it's time to add step refs. Let's list the (imaginary) commits we just added using `git log`:

```shell
author@shell:~/logos$ git log --oneline --reverse
e19c2e6 Our first markdown file
9da9f3c Paragraphs, Headers, Blockquotes
cf92883 Phrase Emphasis
ae23fe0 Unordered  Lists
c09b157 Ordered  Lists
764dfbc Complex lists
ffa19d5 Inline Links
ea8ac14 Inline Links with a Title
b330a00 Reference Links
09b686c Reference Links with options
79c24c8 Images
2f264bc Inline Code
2a4396c Block Code
```

To add corresponding step refs we use `git update-ref`:

```shell
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/1 e19c2e6
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/2 9da9f3c
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/3 cf92883
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/4 ae23fe0
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/5 c09b157
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/6 764dfbc
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/7 ffa19d5
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/8 ea8ac14
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/9 b330a00
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/10 09b686c
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/11 79c24c8
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/12 2f264bc
author@shell:~/logos$ git update-ref refs/markdown@0.0.0/step/13 2a4396c
```

After all the step refs are added you can confirm that it's all good using `git log` again:

```shell
author@shell:~/logos$ git log --oneline --reverse --decorate=full
e19c2e6 (refs/markdown@0.0.0/step/1) Our first markdown file
9da9f3c (refs/markdown@0.0.0/step/2) Paragraphs, Headers, Blockquotes
cf92883 (refs/markdown@0.0.0/step/3) Phrase Emphasis
ae23fe0 (refs/markdown@0.0.0/step/4) Unordered  Lists
c09b157 (refs/markdown@0.0.0/step/5) Ordered  Lists
764dfbc (refs/markdown@0.0.0/step/6) Complex lists
ffa19d5 (refs/markdown@0.0.0/step/7) Inline Links
ea8ac14 (refs/markdown@0.0.0/step/8) Inline Links with a Title
b330a00 (refs/markdown@0.0.0/step/9) Reference Links
09b686c (refs/markdown@0.0.0/step/10) Reference Links with options
79c24c8 (refs/markdown@0.0.0/step/11) Images
2f264bc (refs/markdown@0.0.0/step/12) Inline Code
2a4396c (HEAD, refs/markdown@0.0.0/step/13, refs/lessons/markdown@0.0.0) Block Code
```

After this it's safe to (force) remove our WIP branch (after switching another branch, in this case the `master` branch):

```shell
author@shell:~/logos$ git checkout master
author@shell:~/logos$ git branch -D markdown
```

The last thing to do is to make all of this available to the world:

```shell
author@shell:~/logos$ git push origin refs/lessons/markdown@0.0.0 refs/markdown@0.0.0/*
```

### Updating a lesson

When updating a lesson we start by checking out the ref of the lesson we're about to update into a detached `HEAD`:

```shell
author@shell:~/logos$ git checkout --detach lessons/markdown@0.0.0
Note: checking out 'lessons/markdown@0.0.0'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name

HEAD is now at 2a4396c... Block Code
```

In the following commits we're going to fix some formatting error(s) in `refs/markdown@0.0.0/step/2` and in the steps onwards, so let's start an interactive `git rebase` from `refs/markdown@0.0.0/step/2`:

```shell
author@shell:~/logos$ git rebase --interactive markdown@0.0.0/step/2
```

> **note**
>
> This will only work if the refs for `markdown@0.0.0/*` are fetched first. You can do this using `git fetch`:
>
> ```shell
> author@shell:~/logos$ git fetch origin refs/markdown@0.0.0/*:refs/markdown@0.0.0/*
>```

This let's us interactively choose what commits we need to edit. Change the word `pick` to `edit` on the commits that need editing and save:

```shell
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
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

This will drop us to back to the shell:

```shell
author@shell:~/logos$ git rebase --interactive markdown@0.0.0/step/2
Stopped at cf92883cb505c35b0760956bf5bffc1f8879af60... Phrase Emphasis
You can amend the commit now, with

        git commit --amend 

Once you are satisfied with your changes, run

        git rebase --continue
```

We're going to update `README.md` and fix the indentation error in the end of the file, save it, `git add` the changes and then `git rebase --continue` and save:

```shell
author@shell:~/logos$ git add README.md
author@shell:~/logos$ git rebase --continue 
[detached HEAD a962c8a] Phrase Emphasis
 Date: Thu Apr 16 18:50:48 2015 +0800
 1 file changed, 6 insertions(+)
error: could not apply ae23fe0... Unordered  Lists

When you have resolved this problem, run "git rebase --continue".
If you prefer to skip this patch, run "git rebase --skip" instead.
To check out the original branch and stop rebasing, run "git rebase --abort".
Could not apply ae23fe00f8e0645eda0c42f015d026b98b25b047... Unordered  Lists
```

Since the changes we've done to `README.md` collide with later changes we'll have to solve the conflict. We're going to use [`git mergetool`](http://www.git-scm.com/docs/git-mergetool):

> **note**
>
> [`git mergetool`](http://www.git-scm.com/docs/git-mergetool) is not the only way conflicts can be solved, but it is usually the easiest one to use

```shell
author@shell:~/logos$ git mergetool
```

Solve the conflict, apply the needed changes (indentation) then continue the rebase using `git rebase --continue`:

```shell
author@shell:~/logos$ git rebase --continue 
[detached HEAD 44298db] Unordered  Lists
 1 file changed, 12 insertions(+)
error: could not apply c09b157... Ordered  Lists

When you have resolved this problem, run "git rebase --continue".
If you prefer to skip this patch, run "git rebase --skip" instead.
To check out the original branch and stop rebasing, run "git rebase --abort".
Could not apply c09b157b990773d898e77d9f79b5ff84bd06cc43... Ordered  Lists
```

Keep resolving conflicts and applying changes until the end of the rebase is reached.

```shell
author@shell:~/logos$ git rebase --continue 
[detached HEAD 41df2e7] Block Code
 1 file changed, 7 insertions(+)
Successfully rebased and updated detached HEAD.
```

Once the rebase is completed your new lesson is ready to be published! From here it's the same as when authoring a new lesson:

> **note**
>
> Note that the new version is `0.0.1` since we are publishing an *updated* version of `0.0.0`

- `git update-ref refs/lessons/markdown@0.0.1 HEAD`
- `git update-ref refs/markdown@0.0.1/step/{n} {commit}`
- `git push origin refs/lessons/markdown@0.0.1 refs/markdown@0.0.1/*`

### Adding assignments

When adding assignments we start by checking out the ref of the step we're about to add an assignment for into a detached `HEAD`:

```shell
author@shell:~/logos$ git checkout --detach lessons/markdown@0.0.1/step/2 
Note: checking out 'lessons/markdown@0.0.1/step/2'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name

HEAD is now at 9da9f3c... Paragraphs, Headers, Blockquotes
```

Let's add an empty file where we're expecting our student to provide feedback and then `git add` it:

```shell
author@shell:~/logos$ touch assignment.md
author@shell:~/logos$ git add assignment.md
```

Now we want to add this file as part of a new commit using `git commit`:

> **note**
>
> You can just omit the `--message` switch to launch an editor for the commit message

```shell
author@shell:~/logos$ git commit --message="Your first assignment

Let's see what we've learned so far. We've created a file called \`assignment.md\` that you should use to submit your answer.

Add three headings from level one to three, pick whatever heading contents as you want."
[detached HEAD 5ab1c7e] Your first assignment
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 assignment.md
```

Now that we've added this assignment we need to add a ref to it using `git update-ref`:

```shell
author@shell:~/logos$ git update-ref refs/markdown@0.0.1/assignment/1 HEAD
```

The last thing to do is to make all of this available to the world using `git push`:

```shell
author@shell:~/logos$ git push origin refs/markdown@0.0.1/*
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 413 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 1 (delta 0)
To https://github.com/open-learning/logos.git
 * [new branch]      refs/markdown@0.0.1/assignment/1 -> refs/markdown@0.0.1/assignment/1
```

## Studying

> **actor: student**
>
> This part of the spec assumes the actor is a ***student***

Depending on the lesson studying can be done either entirely by the student alone (in the case of self assessed or machine assessed assignments) or together with someone as in the case of teacher or peer assessed assignments.

### Stepping

We study by steping through each lesson step by step using `git checkout`:

```shell
student@shell:~/logos$ git checkout markdown@0.0.1/step/1
Note: checking out 'markdown@0.0.1/step/1'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name

HEAD is now at e19c2e6... Our first markdown file
```

To display the instructions for this step we use `git log`:

> **note**
>
> For the purpose of this spec we'll just go ahead and ignore the actual contents of this lesson

```shell
student@shell:~/logos$ git log -1
commit e19c2e60b471dc271c0092a1ab750a3daadb0585
Author: Mikael Karon <mikael@karon.se>
Date:   Thu Apr 16 18:46:21 2015 +0800

    Our first markdown file
    
    [Markdown](http://daringfireball.net/projects/markdown/) is a text-to-HTML conversion tool for web writers. Markdown allows you to write using an easy-to-read, easy-to-write plain text form
    
    Let's start by adding a `README.md` file with some logos content.
```

Keep checking out each `markdown@0.0.1/step/{n}` to step through the lesson.

## Assignments

Assignments are just commits that contain assignment material. The student can reach an assignment by enumerating `refs/lesson@version/assignment/{n}`.

In this example we'll start an assignment by checking out the assignment ref to a detached `HEAD`:

```shell
student@shell:~/logos$ git checkout markdown@0.0.1/assignment/1
Previous HEAD position was e19c2e6... Our first markdown file
HEAD is now at 5ab1c7e... Your first assignment
```

Let's look at the instuctions for this assignment using `git log`:

```shell
student@shell:~/logos$ git log -1
commit 5ab1c7ef10bb12fb35671fe8fbacf95671759f3b
Author: Mikael Karon <mikael@karon.se>
Date:   Tue May 5 01:32:52 2015 +0800

    Your first assignment
    
    Let's see what we've learned so far. We've created a file called `assignment.md` that you should use to submit your answer.
    
    Add three headings from level one to three, pick whatever heading contents as you want.
```

Let's follow the instructions and add three lines of heading to `assignment.md`:

```shell
student@shell:~/logos$ echo "#1
##2
###3" > assignment.md
```

To make sure our change was ok let's use `cat` to confirm our changes:

```shell
student@shell:~/logos$ cat assignment.md 
#1
##2
###3
```

## Assesment

Assignments can either be localy or remotely assesed.

In the case of local assesment the tests needed to validate an assignment (like unit tests) should be provided in the commit. Local assesment can then be done by simply running the tests in a test runner.

In the case of remote assesed assignments we think of the assignment in two parts:

- The "challenge" part of an assignment is a git commit containing instructions and learning materials
- The "response" part of an assignment is a pull request containing all the commits to be reviewed

### Preparation

Before we submit this assignment we have to create a more permanent assignment branch using `git checkout`:

> **note**
>
> Here `first-attempt` is the name of our attempt (since it's our first attempt)

```shell
student@shell:~/logos$ git checkout -b markdown@0.0.1/assignment/1#first-attempt
M	assignment.md
Switched to a new branch 'markdown@0.0.1/assignment/1#first-attempt'
```

We want the last changes to be part of our submission so let's add it to our commit using `git add`:

```shell
student@shell:~/logos$ git add assignment.md 
```

Now all we have to do is to commit our changes using `git commit`:

```shell
student@shell:~/logos$ git commit --message="Update assignment.md"
[markdown@0.0.1/assignment/1#first-attempt 86ef6e8] Update assignment.md
 1 file changed, 3 insertions(+)
 ```

The assignment is now ready for "review" but before that can start we have to make our changes available to the world by pushing our assignment branch with `git push`:

> **note**
>
> We've used the repository url `https://github.com/open-learning/logos.git` in these examples, substitute with your own.

```shell
student@shell:~/logos$ git push origin markdown@0.0.1/assignment/1#first-attempt
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 306 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/open-learning/logos.git
 * [new branch]      markdown@0.0.1/assignment/1#first-attempt -> markdown@0.0.1/assignment/1#first-attempt
```

### Submission

Assignment submissions are handled using git pull-requests from the student's assignment branch to the teacher's review branch.

How the pull-request workflow looks will differ from teacher to teacher. In this example we're simply going to go bare-bones-old-school and use `git request-pull` to generate a pull-request text that can be used as a starting point for whatever flavour of workflow.

```shell
student@shell:~/logos$ git request-pull markdown@0.0.1/assignment/1 https://github.com/open-learning/logos.git
The following changes since commit 5ab1c7ef10bb12fb35671fe8fbacf95671759f3b:

  Your first assignment (2015-05-05 01:32:52 +0800)

are available in the git repository at:

  https://github.com/open-learning/logos.git markdown@0.0.1/assignment/1#first-attempt

for you to fetch changes up to 66f5075907ee1f2e19071bee5ff30dd19c900e1f:

  Update assignment.md (2015-05-05 01:57:00 +0800)

----------------------------------------------------------------
student (1):
      Update assignment.md

 assignment.md | 3 +++
 1 file changed, 3 insertions(+)
```

### Advertisement

> **actor: teacher**
>
> This part of the spec assumes the actor is a ***teacher***

Before an assignment submission can be made someone has to advertise that they are available to review a speciffic assignment. This is done by publishing "review" branches that pull-requests can be submitted against.

For example, if we wanted to advertise that we're able to review `refs/markdown@0.0.1/assignment/1` we create `refs/heads/markdown@0.0.1/assignment/1` from `refs/markdown@0.0.1/assignment/1`:

```shell
teacher@shell:~/logos$ git update-ref refs/heads/markdown@0.0.1/assignment/1 refs/markdown@0.0.1/assignment/1
```

After this we must publish our change to make it available to students using `git push`:

> **note**
>
> The above command will push all the `heads` created for `markdown@0.0.1`

```shell
teacher@shell:~/logos$ git push origin refs/heads/markdown@0.0.1/assignment/*
Total 0 (delta 0), reused 0 (delta 0)
To https://github.com/open-learning/logos.git
 * [new branch]      markdown@0.0.1/assignment/1 -> markdown@0.0.1/assignment/1
```

### Review

> **note**
>
> The whole review workflow is based on the `git` pull-request model but adjusted to of whatever flavour and toolset the teacher may favour and as such is not defined in this specification.

There are a bunch of tools and workflows out there to choose from for doing reviews:

- Plain old [email](http://git-scm.com/book/en/v2/Git-Commands-Email) is the most basic and only reqires access to a mail account (this is how `git` itself is maintained)
- GitHub [pull-requests](https://help.github.com/articles/using-pull-requests/) are great for public submissions and reviews and is probably your best option to get started quickly
- [Gerrit](http://code.google.com/p/gerrit/) is probably a bit more than you are looking for, but there's [a lot of organizations](https://code.google.com/p/gerrit/wiki/ShowCases) (like google) that use it on an enterprise level

Each workflow has their own advantages and disadvantages but in the end of the day we end up with a branch in the students repository with a reviewed solution to an assignment.

Normally a reviewed pull-request results in the reviewer accepting the changes and merging them into the targeted branch, but in this case we're not looking to review and merge, but to review and certify.

### Certification

A "certificate" is `git` commit containing a [Mozilla open badge assertion](https://github.com/mozilla/openbadges-specification/blob/master/Assertion/latest.md) with evidence pointing to the original "assignment" commit. Certificate submissions are handled using git pull-requests from the teachers's certificate branch to the students's assignment branch.

For the purpose of this document we've generated our certificate using the [Mozilla badge lab](http://badgelab.herokuapp.com/) but in a production environment the acrediting organization should probably host an instance of the [Mozilla badge kit](https://github.com/mozilla/openbadges-badgekit) and use that to issue certificates.

> **note**
>
> This document won't cover the details of creating assertions. Asume we've correctly generated our assertion JSON by now and are ready to use it.

Before we can issue a certificate we have to have a local copy of the student's assignment branch. Assuming we already have a local clone of the [logos](https://github.com/open-learning/logos/) repository all we have to do is to `git remote add` the student repository and then `git checkout`:

> **note**
>
> We've used the repository url `https://github.com/open-learning/logos.git` in these examples, substitute with your own.

```shell
teacher@shell:~/logos$ git remote add student https://github.com/open-learning/logos.git
```

Once we have the remote, let's `git fetch` available branches from our `student` remote:

```shell
teacher@shell:~/logos$ git fetch student
remote: Counting objects: 17, done.
remote: Total 17 (delta 0), reused 0 (delta 0), pack-reused 17
Unpacking objects: 100% (17/17), done.
https://github.com/open-learning/logos
 * [new branch]      markdown@0.0.1/assignment/1#first-attempt -> student/markdown@0.0.1/assignment/1#first-attempt
 * [new branch]      master     -> student/master
```

Lastly let's `git checkout` the student assignment branch:

```shell
teacher@shell:~/logos$ git checkout student/markdown@0.0.1/assignment/1#first-attempt
Note: checking out 'student/markdown@0.0.1/assignment/1#first-attempt'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name

HEAD is now at 66f5075... Update assignment.md
```

Now that we have a working copy of the student assignment we want to copy the certificate, `git add` it and lastly `git commit` it:

> **note**
>
> In this example we've already generated and stored our certificate at `~/certificate.json`.

```shell
teacher@shell:~/logos$ cp ~/certificate.json certificate.json
teacher@shell:~/logos$ git add certificate.json
teacher@shell:~/logos$  git commit -m "Create certificate.json"
[detached HEAD 1ea97fb] Create certificate.json
 1 file changed, 3 insertions(+)
 create mode 100644 certificate.json
```

Before we submit this certificate we have to create a more permanent certificate branch using `git checkout`:

> **note**
>
> Note that we've prefixed the branch name with `student/` in order to group submissions from the same user together.

```shell
teacher@shell:~/logos$ git checkout -b student/markdown@0.0.1/assignment/1#first-attempt
Switched to a new branch 'student/markdown@0.0.1/assignment/1#first-attempt'
```

Now we need to make this available to the world using `git push`:

```shell
teacher@shell:~/logos$ git push origin student/markdown@0.0.1/assignment/1#first-attempt
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 352 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/open-learning/logos.git
 * [new branch]      student/markdown@0.0.1/assignment/1#first-attempt -> student/markdown@0.0.1/assignment/1#first-attempt
```

And lastly we wan't to generate a pull-request based on the ref `remotes/student/markdown@0.0.1/assignment/1#first-attempt`:

```shell
teacher@shell:~/logos$ git request-pull remotes/student/markdown@0.0.1/assignment/1#first-attempt https://github.com/open-learning/logos.git
The following changes since commit 66f5075907ee1f2e19071bee5ff30dd19c900e1f:

  Update assignment.md (2015-05-05 01:57:00 +0800)

are available in the git repository at:

  https://github.com/open-learning/logos.git student/markdown@0.0.1/assignment/1#first-attempt

for you to fetch changes up to 1ea97fba1be4027ee006be444aeb482f63075df6:

  Create certificate.json (2015-05-05 02:27:48 +0800)

----------------------------------------------------------------
teacher (1):
      Create certificate.json

 certificate.json | 3 +++
 1 file changed, 3 insertions(+)
 create mode 100644 certificate.json
```

### Commencement

> **actor: student**
>
> This part of the spec assumes the actor is a ***student***

Let's start by making sure we're on the correct assignment branch using `git checkout`:

```shell
student@shell:~/logos$ git checkout markdown@0.0.1/assignment/1#first-attempt 
Switched to branch 'markdown@0.0.1/assignment/1#first-attempt'
```

Before we can accept a certificate we have have to add have a `teacher` remote set up. Assuming we already have a local clone of the [logos](https://github.com/open-learning/logos/) repository all we have to do is to `git remote add` the teacher repository.

> **note**
>
> We've used the repository url `https://github.com/open-learning/logos.git` in these examples, substitute with your own.

```shell
student@shell:~/logos$ git remote add teacher https://github.com/open-learning/logos.git
```

Once we have the remote, let's `git fetch` available branches from our `teacher` remote:

```shell
student@shell:~/logos$ git fetch teacher
remote: Counting objects: 17, done.
remote: Total 17 (delta 0), reused 0 (delta 0), pack-reused 17
Unpacking objects: 100% (17/17), done.
https://github.com/open-learning/logos
 * [new branch]      student/markdown@0.0.1/assignment/1#first-attempt -> teacher/student/markdown@0.0.1/assignment/1#first-attempt
 * [new branch]      master     -> teacher/master
```

Now all we have to do is to `git merge` from the `teacher` remote:

```shell
student@shell:~/logos$ git merge --no-ff teacher/student/markdown@0.0.1/assignment/1#first-attempt 
Merge made by the 'recursive' strategy.
 certificate.json | 3 +++
 1 file changed, 3 insertions(+)
```

And finally let's make this available to the world using `git push`:

```shell
student@shell:~/logos$ git push origin markdown@0.0.1/assignment/1#first-attempt
Counting objects: 1, done.
Writing objects: 100% (1/1), 274 bytes | 0 bytes/s, done.
Total 1 (delta 0), reused 0 (delta 0)
To https://github.com/open-learning/logos.git
   66f5075..29a67ab  markdown@0.0.1/assignment/1#first-attempt -> markdown@0.0.1/assignment/1#first-attempt
```
