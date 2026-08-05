+++
title = "I Forked Linux for the Green Square"
description = "The patch went through email. The fork exists because GitHub needs paperwork."
date  = 2026-08-05

[taxonomies]
tags = ["linux"]
+++

I do not need a fork of Linux.

I'm not maintaining some exciting kernel tree. I have zero expectation that Linus Torvalds is going to discover my GitHub account and think, finally, the missing piece.

I forked `torvalds/linux` because GitHub has rules.

I recently sent a [two-line patch](https://lore.kernel.org/rust-for-linux/20260804-inline-wrappers-v1-1-16916db867e5@gmail.com/) to the Linux kernel. It already has a review. Assuming nothing strange happens, it will eventually make its way through the normal kernel process and, perhaps after being carried through enough maintainer trees, end up in Linus's tree.

If it gets accepted, Git will do what Git has been doing perfectly well for decades: preserve the author metadata while the commit moves through repositories maintained by people who have mostly managed to avoid turning the Linux kernel into a GitHub workflow.

GitHub is not involved in this process.

It does, however, mirror `torvalds/linux`.

This creates a very stupid secondary problem.

Because my commit email is associated with my GitHub account, GitHub should be able to look at the mirrored commit and work out that I wrote it. Fine. Git commits have authors. GitHub knows email addresses. Nobody has been harmed yet.

The contribution graph is different. For GitHub to count the commit toward the little collection of green squares on my profile, it also wants evidence that I have some relationship with the repository.

I am obviously not a collaborator on `torvalds/linux`. Issues and pull requests are turned off for very good reasons.

That leaves the fork.

So I forked Linux.

There is now [`EthanPlant/linux`](https://github.com/EthanPlant/linux) happily sitting on GitHub.

I have no reason to ever use it.

I didn't need it to write the patch. I didn't need it to submit the patch to the mailing list. I won't need it if a maintainer applies the patch. Linus does not need to care about it. `git.kernel.org` doesn't need it. The kernel mailing lists will continue operating with their usual complete indifference to its existence.

The fork has one job.

It tells GitHub that my email-based contribution to Linux is sufficiently related to GitHub to deserve a green square.

This is ridiculous.

It is also a wonderful little collision between two different models of how software development works.

GitHub's model is coherent enough on its own terms. You fork a repository, make a branch, open a pull request, discuss it in a web interface, merge it, and GitHub records the entire event because the entire event happened inside GitHub.

Linux works very differently.

Linux uses Git as a distributed version control system in the unusually literal sense. A patch can begin as an email, get reviewed on a mailing list, be applied by a maintainer, move into a subsystem tree, get pulled into another tree, and eventually arrive upstream.

GitHub may mirror the result later, but it is watching the train arrive at the station. It did not build the railway.

The Linux contribution path is basically:

```text
git format-patch
    ↓
  email
    ↓
mailing list
    ↓
 maintainer
    ↓
subsystem tree
    ↓
   Linus
    ↓
kernel.org
```

And then, standing several metres away from the actual process:

```text
GitHub
  ↓
"I know that email address!"
  ↓
green square
```

Except GitHub apparently also wants me to establish standing in the proceedings.

So there is now a fork.

This is where the absurdity stops being merely funny and becomes a useful illustration of what platforms do to the systems underneath them.

The commit does not become more real because GitHub recognizes it. The contribution does not become more legitimate because my avatar appears next to it. The kernel does not care about my contribution graph. Git does not have a concept of green squares.

The green square is a platform interpretation layered over the actual software history.

Usually this does not matter very much. GitHub is good at making Git repositories legible to normal humans, and there is nothing inherently wrong with a website turning repository activity into a convenient profile.

The funny part is what happens when the underlying development process was never designed around the platform.

I did not fork Linux so I could contribute to Linux. There is no reason to fork the GitHub mirror because you want to submit a patch.

I contributed to Linux and then forked Linux so GitHub could understand that I contributed to Linux.

If the patch lands, GitHub may eventually show a commit by me in `torvalds/linux`, complete with my profile attached and a green square somewhere on the calendar.

The actual event represented by that square will have been me writing two lines of code and sending a plain-text email to a mailing list.

Then Microsoft's website will observe the completed process, inspect the metadata, notice that I had previously clicked **Fork**, and decide that yes, this qualifies as GitHub activity.

There is also a pleasing scale problem here.

Linux is not a small repository.

My fork contains the history of one of the most important software projects on Earth. Decades of kernel development. Tens of thousands of contributors. An unreasonable amount of human effort devoted to everything from scheduler internals to obscure hardware that somebody in Finland presumably still owns.

My contribution is two lines to reduce the binary size by a handful of bytes.

Purpose of fork:

green square.

And no, the green square is not the interesting part.

If the patch lands, being able to look through the actual Linux history and find my name attached to a tiny piece of the kernel is much cooler than anything GitHub can put on my profile.

I can run:

```sh
git log torvalds/master \
    --author='Ethan Plant' \
    --oneline
```

and see:

```
dc01dfb37b34 rust: pci: Mark Device refcount methods inline
```

just sitting there in one of the most important pieces of software ever written.

That is the record.

The contribution exists in the Git history. The GitHub square is pointless decoration.

But if a platform is going to maintain an elaborate parallel system for deciding whether my participation in a distributed software project counts as participation, I am apparently willing to meet it halfway by maintaining an entirely useless Linux fork.
