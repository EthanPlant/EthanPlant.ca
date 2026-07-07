+++
title = "My Website Has Become Test Data"
description = "When a 6,000-word Canadian policy essay becomes a row in someone else's operational readiness benchmark."
date = 2026-07-05

[taxonomies]
tags = ["web", "personal-sites", "indieweb"]
+++

My website has reached a new and terrible milestone.

It has not merely been read. It has not merely been indexed. It has not merely been hit by bots asking "are you Wordpress?" It has not merely been shamelessly scraped and rebublished on an AI-slop "news wire".

No. That would be too normal.

My website has now been ingested as part of an operational readiness benchmark for a Hacker News Telegram notifier.

This is, unfortunately, the kind of thing I find delightful.

Somewhere in a [GitHub repository](https://github.com/cbssmh/hackernews-telegram-notifier/), inside an `experiments` directory, there is a CSV file testing whether a deterministic article extraction pipeline can reliably process live Hacker News submissions. One of those rows is my essay, "[Bill C-34 Answers Child Safety with Identity Infrastructure.](/writing/bill-c-34)"

The result is deeply funny. `ethanplant.ca` returns HTTP 200. Fetch succeeds. Extraction succeeds. It finds 39,512 characters. It counts 6,073 words. It estimates 28 minutes of reading time. It finds a preview candidate. It finds a published date. It finds a meta description. It classifies the source type as `Article`. It finishes in 1.031 seconds.

This is the closest a personal website can get to receiving a performance review. I am proud to announce that my humble static website is operationally ready.

I wrote a long Canadian digital-policy essay because Parliament was doing something weird with identity infrastructure and child safety. I published it on my own website because that is what the website is for. Then I posted it to Hacker News, because I am evidently the kind of person who throws a 6,000-word Canadian privacy-law essay into the orange website and waits to see what happens. It hit the front-page briefly because apparently people on said orange site enjoy Canadian policy discourse At some later point, someone building a notifier used live Hacker News stories as a benchmark corpus, and my essay became one row in a CSV file measuring article extraction reliability.

This is the modern web in miniature. You write something. You publish it. People read it. Bots read it. Search engines read it. Feed readers read it. Link preview generators read it. Summarizers read it. Archival tools read it. Some stranger’s script reads it and decides whether the output starts with article context or whether code blocks dominate the page. At some point your personal website stops being only a place where you put writing and starts becoming an object in the world.

It has affordances. It has metadata. It has URLs that do not immediately break. It has HTML that can be parsed by something other than the specific browser tab you happened to test it in. It has an RSS feed. It has titles and dates and descriptions and canonical links. It can be fetched. It can be extracted. It can be archived. It can be cited. It can accidentally become part of someone else’s infrastructure.

This is one of the understated virtues of boring websites. A static HTML page with decent metadata is not glamorous. It does not feel like a product. It does not have a growth team. It does not need a framework to hydrate itself into existence. It is not the kind of thing that makes a front-end engineer weep with joy. It just sits there, mildly smug, returning 200s and letting the rest of the web do web things to it.

And then one day you discover that a 6,000-word policy essay about Canadian internet legislation has become benchmark data.

There is a temptation to make this sound dystopian. To be fair, parts of it are. The modern internet is a giant ingestion machine. Everything public is scraped, indexed, embedded, summarized, ranked, clustered, deduplicated, and shoved into somebody’s pipeline. It is not always benign. It is not always consensual in any meaningful sense. "Publicly accessible" has become a kind of ritual phrase people use to paper over the fact that almost nobody publishing something on the web is thinking, "I hope this becomes a row in a random operational readiness benchmark."

But I also cannot pretend I am especially offended here. This is partly because the use is so funny. It is also because this is, in a strange way, the web working correctly. The page was public. The URL was stable. The markup was readable. The metadata was there. A tool could fetch it, extract it, understand that it was an article, and produce useful information about it without asking me to install an app, log into a platform, or accept a modal. This is exactly the kind of thing I keep insisting matters.

The small web is not small because nothing connects to it. It is small because the unit of ownership is smaller. A personal website can still participate in the wider web. It can be linked, scraped, saved, syndicated, searched, and embedded into strange little tools maintained by people you have never met. The point is not to retreat into a private garden. The point is to have a home address.

I’ve argued before that platforms are the edge; the website is the home. Apparently, sometimes, the home is also where someone’s benchmark fixture lives.

Still, it is a little alarming to realize that my website now has object permanence. It exists not just as a place I update, but as a thing other systems have observed and recorded. Somewhere in a CSV file, my essay is no longer an essay. It is `extraction_success=True`. It is `word_count=6073`. It is `source_type=Article`. It is `elapsed_seconds=1.031`.

This is ridiculous. This is also exactly what I wanted. Not the CSV file specifically. I don't think any healthy person wakes up and says, "I hope my thoughts on Canadian privacy law are useful for measuring deterministic HN article extraction." But I did want the site to be legible to the web. I wanted it to be plain enough that other tools could understand it. I wanted the writing to live somewhere that was mine without being trapped there.
 
So there it is. My website has been read by humans, bots, feeds, search engines, link aggregators, and now a benchmark flow.

The machine has looked upon `ethanplant.ca` and declared:

HTTP 200 OK.

Extraction succeeded.

Article.
