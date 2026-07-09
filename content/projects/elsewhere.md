+++
title = "Elsewhere"
description = "A local POSSE CLI for static-site writers."
date = "2026-07-08"

[taxonomies]
tags = ["rust", "indieweb", "posse", "cli", "static-sites"]
+++

[Elsewhere](https://github.com/EthanPlant/Elsewhere) is a local POSSE CLI for static-site writers.

It treats your website as the canonical source of your writing and renders platform-specific publishing drafts for other places.

Your website is the home. Platforms are edges.

It is available on crates.io:

```sh
cargo install elsewhere
```

## What it is

Elsewhere reads a post from a static site and turns it into publishing drafts for other places.

It currently supports:

* Generic Markdown sites
* Zola sites
* Mastodon drafts
* Bluesky drafts
* Reddit drafts
* long-form Markdown drafts

The basic workflow is:

```sh
elsewhere plan content/writing/example-post.md
elsewhere render mastodon content/writing/example-post.md
elsewhere render all content/writing/example-post.md
```

The `plan` command shows what Elsewhere thinks it will render before you post anything.

That matters.

A tool that touches publishing should let you inspect the output before it goes anywhere.

## Why I built it

I built Elsewhere because copying posts from my website into platform text boxes is annoying.

Not hard. Annoying.

The kind of annoying that slowly teaches you bad habits.

A post starts on your website. Then you need a Mastodon version. Then a Bluesky version. Then maybe a Reddit link post with a suggested first comment. Then maybe a long-form Markdown draft for somewhere else. Each platform wants almost the same information, but not quite in the same shape.

Title. Description. Excerpt. URL. Tags. Body. Maybe hashtags. Maybe a character limit. Maybe a subreddit. Maybe a first comment. Maybe a canonical note.

At some point, the copying starts to feel less like publishing and more like clerical work.

Elsewhere is my attempt to make that clerical work less annoying without making platforms central.

The original still lives on my site.

Everything else is derived.

## What made it interesting

The first version looked like it would be a simple text transformer.

That was almost true.

Mastodon and Bluesky are mostly templates with character limits. They are not trivial, but the shape is obvious enough:

```text
{excerpt}

New post: {title}
{url}
```

Then the project got more interesting.

Zola stores tags as taxonomies, not as top-level tags. Canonical URLs need to be derived from the site configuration and content path. A post might have a custom slug. A draft should warn you. A first paragraph might be a bad excerpt. Markdown input and Markdown output are not the same thing, because source Markdown contains front matter, site metadata, taxonomies, and local assumptions.

Then I added Reddit.

Reddit is where the abstraction stopped being "text transformer" and became something more accurate.

A Reddit post is not just one text box. It has a subreddit, a title, a submission kind, a URL or body, and maybe a suggested first comment. It also has community rules and norms that a tool cannot magically understand.

That made the project clearer.

Elsewhere is not trying to turn every destination into a string.

It takes a canonical post and produces a publishing draft shaped for a target.

Some targets are plain text.

Some targets are Markdown.

Some targets are structured artifacts.

## Editorial control

The part I care about most is not the templating.

It is the control.

Elsewhere supports per-post editorial overrides because a tool should not pretend it knows better than the writer.

A first paragraph is often a good excerpt. Sometimes it isn't.

So a post can say:

```toml
[extra.elsewhere]
excerpt = "This is the version I actually want to send elsewhere."
```

And a specific platform can have its own version:

```toml
[extra.elsewhere.mastodon]
template = """
A custom version for Mastodon.

{excerpt}

{url}
"""
```

That feels like the right boundary.

The tool can prepare the draft. The writer still decides what the draft should say.

## What I learned

Elsewhere reminded me that small tools become useful when they fit an actual workflow.

Not an imagined workflow. Not a pitch-deck workflow. A real one.

The emotional threshold wasn't when the code compiled. It was when I ran Elsewhere against my actual website and got a real post draft with the correct canonical URL.

That is the moment a side project changes shape.

Before that, it's is an idea floating around in my head.

After that, it becomes a tool.

A slightly annoying tool, perhaps. A tool with bugs. A tool that immediately reveals that Zola metadata is not shaped the way you casually assumed at 11 p.m. But still a tool.

Useful software has obligations.

It needs documentation. It needs tests. It needs examples. It needs release notes. It needs a licence. It needs boring commands like:

```sh
cargo fmt
cargo clippy
cargo test
```

This is how a funny little side project becomes software.

Unfortunately.

## Why Rust

Rust was a good fit because Elsewhere is a command-line tool that mostly needs to be predictable, boring, and easy to distribute.

The work is not especially glamorous. Parse some config. Read a post. Build a canonical model. Render target-specific drafts. Warn about obvious problems. Exit cleanly.

Rust is good for that kind of tool.

It gives the project a single binary, a strong type system, decent error handling, and the ability to grow without turning every refactor into soup.

The nicest part is that the data model can become explicit.

A post is a post.

A target is a target.

A renderer renders.

A warning is not a panic.

That sounds obvious until a side project grows just enough that the obvious things start needing names.

## What it does not do

Elsewhere does not publish directly to any platform.

That is deliberate.

It has no accounts. No OAuth. No dashboard. No analytics. No scheduling. No engagement tracking. No hosted service. No growth tooling.

The intended workflow is:

```text
plan
review
render
edit
post manually
```

That friction is useful.

It keeps the human in the loop. It makes the writer look at the output before publishing. It leaves room for taste, context, community norms, and the simple question of whether something should be posted somewhere at all.

This matters especially for Reddit.

Elsewhere can prepare a Reddit draft. It cannot read the room for you. It cannot turn syndication into permission.

## Current state

Elsewhere is at `0.1.0`.

It is usable for my own site and available on crates.io.

It currently supports Generic Markdown and Zola sources, with renderers for Mastodon, Bluesky, Reddit, and Markdown.

It has documentation, examples, tests, GitHub Actions, a signed release tarball, and a GPL-3.0-or-later licence.

That is more infrastructure than I expected when I started building a small tool to avoid copying text into boxes.

Possible next steps include Hugo support, Eleventy support, better Markdown handling, HTML export for rich-text editors, syndication tracking, and maybe posting APIs someday.

But rendering should remain useful on its own.

The point is not to make platforms central.

The point is to make my website easier to keep central.
