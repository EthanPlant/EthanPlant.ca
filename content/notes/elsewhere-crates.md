+++
title = "My Side Project Has Hit Crates.io"
description = "Elsewhere, my local POSSE CLI for static-site writers, is now on crates.io."
date = "2026-07-08"

[taxonomies]
tags = ["rust","indieweb", "personal-sites", "web"]

[extra.elsewhere.reddit]
subreddit = "rust"
kind = "link"
comment = """
I built a small Rust CLI for my own static-site workflow because I got tired of copying posts from my website into platform text boxes.

The idea was simple: my website stays canonical, and the tool renders publishing drafts for other places.

At first it was basically a funny little text transformer. Then it grew support for Zola metadata, per-post editorial overrides, readable plan output, JSON output, Reddit drafts, Markdown export, tests, CI, docs, a signed release tarball, and eventually crates.io.

So this is mostly a note about the weird emotional threshold where a side project stops being "a folder in my home directory" and becomes "software."
"""
+++

[Elsewhere](/projects/elsewhere/) is on [crates.io](https://crates.io/crates/elsewhere) now.

This is a strange sentence to write because Elsewhere began as a tiny little personal workflow gremlin.

The idea was simple: my website should be the canonical home for my writing. Other places should get rendered drafts, excerpts, links, or platform-specific versions. Mastodon gets one shape. Bluesky gets another. Reddit, because it is Reddit, gets a structured artifact with a title, destination community, submission kind, URL, and suggested first comment. Markdown gets a long-form draft.

My website is the home. Platforms are edges.

That was the whole idea.

Naturally, this somehow became a Rust CLI with a signed GitHub release, a GPL licence, documentation, examples, tests, CI, and now a crates.io package.

As one does.

You can now install it with:

```sh
cargo install elsewhere
```

This is the point where a side project stops being "a folder in my home directory" and becomes "software," which is a deeply alarming transition. One moment you are writing a parser because copying your own posts into social media boxes is annoying. The next moment you are looking at package metadata and thinking about release notes.

There is a particular emotional threshold when a side project becomes useful. Not finished. Not polished. Not important. Useful.

Elsewhere crossed that line when I ran it against my actual website and it produced a real Mastodon post from a real article with the correct canonical URL. It crossed it again when `plan` started showing every rendered draft before posting. It crossed it again when I added per-post editorial overrides, because sometimes the first paragraph is not the right excerpt and a tool should not pretend it knows better than the writer.

That last point matters more than it sounds.

One thing I didn't fully appreciate until I started using it is that per-post editorial overrides also archive the act of syndication. The Mastodon post, the Bluesky framing, and the Reddit title are no longer little bits of text lost to a clipboard or trapped inside a platform. They can live beside the canonical post as metadata. The website does not merely preserve the article; it preserves how I intended the article to travel elsewhere.

Elsewhere is not a social media management app. It is not a dashboard. It does not want your accounts. It does not do OAuth. It does not schedule posts. It does not track engagement. It does not optimize for reach. It does not spray content across platforms while wearing a little productivity hat.

It renders drafts.

That is all.

The restraint is the point.

The intended workflow is:

```text
plan
review
render
edit
post manually
```

This is slower than full automation. Good.

A tool that touches publishing should leave room for judgement. It should make the annoying part easier without turning the human into a rubber stamp. It should help me see what I am about to post, not quietly decide that every platform is an interchangeable bucket waiting to be filled.

That is especially true for Reddit. A Reddit renderer is not just a funny extra target. It is the thing that makes the whole project easier to explain. Mastodon and Bluesky are mostly text boxes with different limits and vibes. Reddit is not. Reddit has communities. Rules. Norms. Titles. Link posts. Self posts. First comments. A post can be technically valid and still not belong there.

Elsewhere can prepare the draft. It cannot read the room for me. It cannot turn syndication into permission.

That boundary is deliberate.

I know someone could use a tool like this badly. Someone can always wrap a CLI in a shell script and make everyone’s day worse. But I don't want the easiest path through Elsewhere to be spray-and-pray spam. I want the natural path to involve looking at the output before doing anything with it.

The funny thing is that, internally, Elsewhere is still not that complicated.

A post becomes a canonical model. A renderer turns that model into a target-specific publishing draft. Some renderers are plain text. Some are Markdown. Some are structured artifacts. The details are fiddly, but the shape is simple.

A lot of platforms are just templates with annoying vibes.

The more interesting part is the direction of dependence.

Elsewhere does not ask Mastodon, Bluesky, Reddit, Markdown, or anything else what the original is. The original is on my site. The platform drafts are derived from it. They may be useful. They may be necessary. They may be where people actually see the thing. But they are not the source of truth.

That is the part I care about.

Putting Elsewhere on crates.io is a small milestone. It is not a launch in the startup sense. There is no waitlist. No growth strategy. No conversion funnel. No "we’re excited to announce." God willing, no deck.

It is just a little free software tool that now installs like this:

```sh
cargo install elsewhere
```

Which is, admittedly, pretty satisfying.

I do not know how far I will take it.
There are obvious next steps: better documentation, more static-site sources, more renderers, better Markdown handling, maybe HTML export for editors that do not accept Markdown cleanly, maybe syndication tracking later. Hugo and Eleventy support would both make sense. So would more boring tests.

But for now, I am enjoying the very specific joy of a side project becoming real enough to install.

Elsewhere exists because I wanted my website to remain the canonical home of my writing while making it less annoying to participate elsewhere.
