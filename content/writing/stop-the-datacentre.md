+++
title = "Canada's Anti-AI Movement Is Powered by an AI News Aggregator"
description = "Stop the Datacentre appears to double as a live deployment of Newscube's closed-source intelligence stack."
date = 2026-07-22

[taxonomies]
tags = ["canada", "ai", "policy"]

[extra.elsewhere]
excerpt = "Stop the Datacentre says it is powered by Wardroom. Wardroom points directly to Newscube, which builds systems for live editorial intelligence. The campaign’s own JSON feed shows an automated agent classifying posts. Canada’s anti-AI movement appears to be running on an AI startup’s product stack."

[extra.elsewhere.mastodon]
template = """
Canada’s anti-AI movement appears to be powered by an AI startup.

Stop the Datacentre says it runs on Wardroom. Wardroom points directly to Newscube, which builds "live editorial intelligence." The campaign’s own JSON feed exposes an automated agent classifying posts and generating rationales.

No AI. Powered by AI.

{url}

#AI #CdnPoli
"""
+++

[Stop the Datacentre](https://stopthedatacentre.ca/) is powered by AI.

Not metaphorically. Not in the tedious sense that somebody involved may have asked ChatGPT to rewrite a newsletter or produce a tasteful illustration of a server rack menacing a duck pond.

The campaign's public website is connected to a closed-source news intelligence system that ingests articles and social posts, interprets what they're about, assigns them an editorial category, and produces a written explanation for why they belong there.

## Stop the Datacentre

Stop the Datacentre presents itself as a national network of local campaigns opposing proposed AI datacentres across Canada. The central site lists campaigns in cities including Hamilton, Toronto, Mississauga, Vancouver, Calgary, Regina, and Saint John. It promises that organizers can launch a new local campaign quickly, using a shared collection of maps, forms, election trackers, updates, and organizing tools.

The local sites all have roughly the same structure. There is an explanation of the proposed project. There are campaign updates. There is a countdown timer to the next municipal election. There is a map. There are tools for contacting politicians. There is a form for becoming an organizer. There is a continuously updated stream of articles and social posts called the Live Monitor.

The campaign's central argument is not ridiculous. Municipalities have rules for apartment buildings, warehouses, factories, roads, power lines, and almost every other large physical object a private company might attempt to place beside somebody’s home. Hyperscale datacentres do not fit neatly into many of those existing categories. A proposed facility can consume hundreds of megawatts. It can require new generation and transmission infrastructure. It can produce persistent noise. It can consume water. It can reshape provincial electricity planning and lock communities into private agreements that will outlive the politicians who signed them.

The economic case is not automatically convincing either. A facility can be physically enormous, electrically ravenous, and employ fewer permanent workers than the Costco across the road. A community is allowed to look at a warehouse demanding the output of a respectable power plant and ask what, precisely, it is getting in return.

The problem is not that Stop the Datacentre has concerns about datacentres. The problem is that the campaign demanding transparency around AI infrastructure is itself quietly operating through opaque AI infrastructure.

## This is an anti-AI campaign

There is a possible defence here. Perhaps Stop the Datacentre is not anti-AI. Perhaps it opposes only specific industrial projects. Perhaps the campaign is narrowly concerned with zoning, water, electrical planning, noise, and municipal authority.

The public messaging makes this defence difficult. The organizer form asks supporters to choose their two largest concerns. The available categories include energy use, water and air pollution, lack of regulation, and:

> I just hate AI

This is not a planning concern.  It is not "I oppose hyperscale development in residential areas." It is not "I want a provincial environmental assessment." It is not "I am concerned about electricity prices." It's a generalized position toward the technology itself.

The public accounts surrounding the campaign are similarly unambiguous. They use names like No AI Vancouver and No AI Greed. Organizers have publicly called for bans on AI use in British Columbia schools. The campaign is attached to a broader political movement that does not merely want stricter zoning conditions for datacentres. It wants artificial intelligence constrained, rejected, or prohibited across substantial parts of public life.

That's all fine. People are allowed to oppose AI. There are very good reasons to oppose it, or want it regulated. But they should probably disclose when AI is operating the machinery of their own movement.

## Powered by Wardroom

At the bottom of each Stop the Datacentre site is a short declaration:

> Stop the Datacentre 2026 • powered by Wardroom

The sentence beneath it describes Wardroom as a civic-monitoring platform developed in Hamilton, Ontario to track local issues, discussions, and municipal decision-making.

This sounds explanatory until you visit Wardroom. [Wardroom](https://wardroom.site) is not a general-purpose civic movement platform. It's someone's closed-souce intelligence dashboard for Hamilton municipal politics. It monitors a specific city. It follows local news, political discussion, council activity, and public information concerning Hamilton. It is presented as a finished application, not as a reusable framework.

There is no public repository or installation guide. There is no documentation describing how to create a new tenant, define a political campaign, deploy a municipal microsite, manage supporters, publish election trackers, or generate maps of proposed industrial developments. It is not a general-purpose content management system or civic organizing framework.

Saying that Stop the Datacentre is "built on Wardroom" therefore makes very little technical sense on its face. You can build a campaign site with Drupal. You can build one on NationBuilder. You can build one using a general-purpose application framework or a hosted organizing platform. You do not normally build a national political campaign on top of a closed municipal intelligence dashboard for Hamilton.

Not unless the public dashboard is only one interface into a much larger private system. Either "powered by Wardroom" is being used so loosely that it means little more than "made by the same people," or Wardroom is not the underlying platform at all. It is one deployment of a broader ingestion, classification, publishing, and intelligence system that is not publicly documented.

The Stop the Datacentre sites strongly suggest the second explanation. They do much more than display Hamilton news. They operate across multiple cities. They collect supporter information. They publish local campaign material. They map development proposals. They track elections. They expose shared RSS and JSON feeds. They combine manually selected stories with automatically classified news and social posts.

That is not a municipal politics dashboard being repurposed, there's a platform here. The public Wardroom site simply is not the platform being described. Something sits underneath it.

## "Other Projects"

Wardroom has a small "other projects" link tucked away in the corner of the page. It goes directly to a site called [Newscube](https://newscube.ca/).

Newscube's homepage is sparse in the particular way early-stage technology companies believe conveys seriousness:

> Systems for live editorial intelligence

The site explains Newscube develops hardware and software for tracking and interpreting information environment. It lists two products. The first is "Radar":

> Live news intelligence surface focused on movement, continuity, and situational awareness.

The second is Newscube itself:

> A dedicated hardware interface for live editorial intelligence and one-click content distribution across social channels.

This is startup language. There's a product category, a software product, a proposed hardware interface. There is a carefully constructed phrase, "live editorial intelligence", describing the thing being sold.

Radar appears to remain in a closed preview. There is no working surface, pricing page, public documentation, or visible path for someone outside the project to begin using it. The startup page states, "Radar is slowly opening up to the public. If you already have an account, sign in. If you have a code, create one." and requires an invite code to sign up. 

That does not prove Newscube is incorporated, venture-backed, or currently charging customers. "Startup" here describes the visible shape of the operation: a closed-source product family, a named market category, a preview-stage flagship application, and a live deployment demonstrating what the technology can do.

That live deployment appears to be Stop the Datacentre.

## What Newscube sells

Newscube's copy does not describe a typical RSS reader or news aggregator. Newscube says it tracks and interprets public information environments. Interpretation is the product. The system is meant to take a large stream of news, social posts, political discussion, and public information, then reduce it into something operationally useful.

That is precisely what Wardroom does for Hamilton municipal politics. It watches the city, gathers local material, and turns that material into a clean intelligence dashboard. Stop the Datacentre then performs the same operation for a national political campaign. The campaign’s Live Monitor gathers stories and social posts about datacentres. Its feed divides material into pinned, social, and news sources. Some entries are selected manually. Others are processed by an automated classifier.

## The machine beneath the monitor

Each Stop the Datacentre site includes a Live Monitor. The visible interface looks simple enough. It is a stream of news stories and social posts related to datacentres. There are headlines, excerpts, images, publication names, and timestamps. Below the monitor are links to RSS and JSON feeds.

Naturally, I opened the [JSON](https://vancouver.stopthedatacentre.ca/feed.json).

The top level describes a "datacentre bucket", lists that the sources are three type of material: `["pins", "social", "news"]`, and reports seperate totals for each.

This already tells us more than the public interface. The site is not merely displaying a hand-maintained reading list of content about data centres. It has distinct systems for manually pinned items, social posts, and news stories.

The manually selected records are easy to identify. Their IDs begin with `pin:`, they have a `pin_id` field, and include `"pinned": true`.

Then there are the other records. One contains a [Bluesky post by Hamilton journalist Joey Coleman](https://bsky.app/profile/joeycoleman.ca/post/3mqsojisqk22i) discussing legal advice released by Hamilton City Council concerning an AI datacentre. The record includes the original post, its publication time, its source feed, the time it entered the system, an excerpt, and four additional fields:

```json
"bucket": "datacentre",
"rationale": "The post discusses legal advice related to an AI data centre and mentions municipal election coverage.",
"classifier": "agent",
"classified_at": "2026-07-17T02:25:03+00:00"
```

The post was published to Bluesky at 02:14 UTC. The feed reports it as arriving at 02:15. The agent classified it at 02:25. The timestamps imply an asynchronous job. The post was published, ingested by the system, and queued for a background classification job which finished 10 minutes later. The system took an unstructured social-media post, interpreted its meaning, assigned it to the datacentre category, and generated a plain-language explanation for the decision.

That is an automated semantic classification pipeline. It is AI in every reasonable sense of the word. 

## This is not a keyword filter

It would be possible to build a datacentre monitor without AI. A conventional program could subscribe to RSS feeds and search for words such as `datacentre`, `data centre`, `hyperscale`, `megawatt`, `GPU`, `cooling`, or the names of known developers. It would be extremely crude, but it would also work reasonably well.

The public feed shows the system doing more than that. A keyword filter can determine that a post contains a particular word. It does not ordinarily produce a sentence explaining that the post discusses legal advice related to an AI datacentre and municipal election coverage. 

The system is making a contextual judgment. It is interpreting unstructured language, assigning an editorial category, and creating new metadata explaining why the category applies.

The feed is careful not to identify a specific model, instead just using the generic term "agent". It does not name OpenAI, Anthropic, Google, Mistral, or any other provider. It does not expose the prompt. It does not say whether the classifier uses a large language model, a smaller local model, a conventional machine-learning classifier with a generation layer, or a custom arrangement.

The public evidence supports a precise claim: an automated agent reads incoming public material, determines whether it belongs in the datacentre category, and generates a natural-language rationale for the decision. In ordinary language, that is an AI news aggregator.

## Three names, one product family

The public evidence points toward three connected interfaces. Newscube is the broader product operation. Radar is its general live-news intelligence surface. Wardroom is the Hamilton municipal-politics dashboard. Stop the Datacentre is the political deployment.

The exact technical boundaries are not public. Wardroom could consume a Newscube API. Radar and Wardroom could share an ingestion service. Stop the Datacentre could be a separate application consuming a classified feed. All three could share one backend, or they could consist of several services deployed independently.

There is no public architecture diagram, but there doesn't need to be one for the basic relationship to be clear. Stop the Datacentre claims its built on top of Wardroom, Wardroom directly links to Newscube as "another project". Newscube describes the generalized product category. The Stop the Datacentre feed exposes the agent performing the core interpretation Newscube advertises.

There is also evidence in the DNS records for the three domain names. All three domains have the exact same pair of Cloudflare name servers:

```sh
dig @8.8.8.8 +short NS stopthedatacentre.ca

johnny.ns.cloudflare.com.
pat.ns.cloudflare.com.

dig @8.8.8.8 +short NS wardroom.site

pat.ns.cloudflare.com.
johnny.ns.cloudflare.com.

dig @8.8.8.8 +short NS newscube.ca

pat.ns.cloudflare.com.
johnny.ns.cloudflare.com.
```

This isn't some obscure piece of trivia. When a domain name is onboarded to Cloudflare to manage its DNS records, [Cloudflare automatically assigns two nameservers](https://developers.cloudflare.com/dns/nameservers/). [Cloudflare publicly states they run 101 nameservers](https://developers.cloudflare.com/dns/nameservers/), giving a total of 2550 possible combinations. The odds of any three domain names having the exact same nameserver combination is therefore, quite low. There is one caveat however:

> **The default assignment method is to use standard nameservers and favor consistent nameserver names across all zones within an account.** Nonetheless, in case there are conflicts, you may get different nameserver names, even for domains that are within the same account. (emphasis added)

Obviously nameservers are nowhere near authoritative proof of the domains' owner. It is entirely possible for three unrelated domains to have the same nameservers. However, placed beside the direct project links, product descriptions, shared functionality, and campaign attribution, it becomes difficult to explain the three systems as unrelated.

The conservative conclusion is that the same operator, or a closely connected group, controls Stop the Datacentre, Wardroom, and Newscube.

## Every city is apparently a suburb of Hamilton

The local feeds add another clue. Each local site maintains its own feed. As I live in Vancouver, naturally I investigated the Vancouver feed endpoint first. One might reasonably expect this to contain a Vancouver-oriented stream.

Instead, the feed is heavily weighted toward Hamilton. At the time I inspected it, the feed reported 50 pinned items, 128 classified social items, and five classified news items. Its leading entries included a Hamilton Spectator opinion piece, a Bluesky post about Hamilton City Council, and a post from r/Hamilton. The automated social records were labelled: `"feed_tag": "hamilton-socials"`. No corresponding `vancouver-socials` tag appeared in the returned material.

The Vancouver site is not operating a distinctly Vancouver intelligence system. It is displaying the same substantially Hamilton-shaped datacentre feed. Every other local feed I investigated appears to show the exact same dataset. 

This is less interesting as a question of editorial fairness than as a clue about how the product was built. It isn't inherently improper for a national campaign to use a common nation-wide dataset, it's fairly reasonable. But the heavy weight towards Hamilton is... interesting, to say the least. Hamilton, Ontario is, unsurprisingly, not necessarily a major source of national news.

Wardroom already monitors Hamilton, its creator is presumably from there. Stop the Datacentre needed a national datacentre monitor. The resulting system appears to retain Wardroom’s Hamilton source environment while adding a `datacentre` classification bucket and a new campaign interface. They scaled the frontend while the machinery still believes it lives in Hamilton.

That makes "powered by Wardroom" considerably easier to understand. Stop the Datacentre may not have been built on Wardroom in the conventional sense. It may be a new product deployment built from Wardroom’s existing intelligence pipeline. The campaign is therefore not merely using Wardroom. It appears to be demonstrating that the Wardroom and Newscube stack can be expanded from one city’s municipal politics into a national issue-monitoring system.

It's a case study.

## The anti-AI campaign is the product demo

This is the central contradiction. Stop the Datacentre is not simply a political movement that happens to use AI. It appears to be one of the most complete public demonstrations of an AI intelligence product. Newscube needs to show that its system can ingest a public-information environment, classify material, maintain continuity around an issue, and present useful intelligence through a dedicated interface.

Stop the Datacentre does exactly that. It provides a real political subject. It operates across multiple cities. It produces a live stream of news and social media posts about a topic. It combines human selections with automated classifications. It exposes rationales generated by an agent. It demonstrates that the underlying system can be pointed at something other than one city's municipal politics.

It is difficult to imagine a cleaner product demonstration. Wardroom shows the local use case. Radar shows the broad news use case. Stop the Datacentre shows the campaign use case. The anti-AI movement is not merely sitting beside the product. It is helping prove the product works.

## This is not incidental use

There is an obvious objection here. People can criticize technologies while using them. Climate organizers drive cars. Privacy advocates own smartphones. People opposed to Amazon occasionally buy something from Amazon because they need a replacement dishwasher part by Thursday and society has made several unfortunate architectural decisions. Purity testing is not a serious political standard. 

That isn't what's happening here. This isn't incidental use. It's not unavoidable use. It's not a volunteer using Gmail because everyone already has Gmail. The campaign chose to build its public infrastructure around Wardroom. Wardroom points to Newscube. Newscube appears to be developing and productizing AI-powered intelligence systems. The campaign’s feed exposes the AI component directly. The relationship is not hidden in a vendor dependency six layers down, it's right there in the footer.

There is a difference between an anti-AI organizer posting on Instagram and an anti-AI campaign acting as a deployment of an AI startup’s core product. The first is participation in a communications environment that is difficult to avoid. The second is a direct choice about what technology will power the movement.

## It is not merely hypocrisy by association

The contradiction is also stronger than "AI requires datacentres." Almost every modern website ultimately runs in a datacentre. That argument becomes useless very quickly. The Stop the Datacentre site could be hosted on a ten-year-old office computer in Hamilton and somebody would still be able to declare that the switch upstream probably lived in a datacentre.

That isn't the point. Newscube’s apparent product exists specifically because of the present AI boom. Its value proposition is that software can now ingest enormous streams of human communication, interpret what they mean, and turn them into usable intelligence. The classifier inside the Stop the Datacentre feed is not incidental infrastructure like TLS or a PostgreSQL database. It's the very thing Newscube appears to be productizing. The anti-AI movement is powered by a company whose reason for existing appears to be that AI can automate the interpretation of public information.

## The movement is helping build the thing it opposes

There is another layer. Stop the Datacentre does not merely receive the benefits of the system. Its use helps develop it. A real campaign produces real edge cases. It produces political terminology, ambiguous posts, local news, council documents, Reddit discussions, social commentary, duplicated stories, irrelevant mentions, and breaking developments. That is all incredibly useful product material.

Every classification provides evidence about whether the system works. Every missed post reveals a gap in source coverage. Every new city creates another potential deployment pattern. Every organizer relying on the monitor demonstrates a practical use for the intelligence surface.

I cannot prove from the public evidence whether Stop the Datacentre data is formally used to evaluate or improve Newscube's models. I cannot even prove who involved with the campaign is aware that their news feed is an AI classification pipeline. That would require internal documentation I don't have.

The broader product-development benefit does not require model training. A live deployment teaches its operator how the system behaves. The anti-AI campaign appears to be providing Newscube with a national political use case for its AI intelligence product. The movement may be helping build the sort of system it says should not be built.

## The startup-shaped object

It is worth being precise about the word "startup." Newscube’s public site does not provide corporate registration details. There's no announcement of a funding round. There's no list of investors, customers, employees, or pricing. It may be a personal project, an unincorporated operation, a tiny product studio, or a company in a very early stage.

The public presentation is nevertheless unmistakably startup-shaped. It defines a new product category. It has named products and a closed preview. It develops proprietary software and hardware. It describes a broad commercial capability rather than one narrow community tool. It appears to have multiple live deployments demonstrating that capability.

Calling Newscube an established company would go beyond the evidence. Calling it an apparent AI startup is a fair description of what it is presenting to the world.

The claim is not that a secret billion-dollar corporation has infiltrated a grassroots campaign. The claim is much funnier. A small anti-AI movement appears to be powered by a small AI startup. Everyone involved may fit around one table.

## The disclosure that says less than it appears to

Stop the Datacentre does technically acknowledge Wardroom. That is better than saying nothing. It is also almost useless to an ordinary visitor. "Powered by Wardroom" sounds like a software credit. It does not explain that Wardroom is a closed intelligence dashboard rather than a normal campaign framework. Most users will probably just see the "civic-monitoring platform" description and think nothing of it. 

It does not explain that Wardroom links directly to Newscube. It does not explain that Newscube develops systems for interpreting public information. It does not explain that the campaign feed contains records classified by an automated agent. It does not explain that the campaign appears to be a deployment of the broader Newscube product family. The disclosure gives the reader a name without telling them what the name means.

## What they could say

There's a straightforward way to resolve most of this. They could just explain the relationship plainly:

> Stop the Datacentre was created by the team behind Wardroom and Newscube. Our Live Monitor uses an automated AI classifier to identify relevant news and social posts.

Newscube could explain whether Stop the Datacentre is an official deployment, an affiliated project, a customer, a demonstration, or something else. The campaign could identify the model and provider. They could explain whether the classifier is a local model or a commercial API. It could describe what data gets sent to the model. It could publish the source code, or explain why the system remains closed. It could say whether campaign activity is being used to test or develop the Newscube product. It could explain the shared infrastructure and ownership.

The answers may be completely benign. I expect they probably are. The classifier may use a tiny local model. The campaign may have been created by the same person as Wardroom without any commercial relationship. Newscube could be one guy in Hamilton's hobby project. Stop the Datacentre may have paid nobody.

Just... say so. The problem is not that every unanswered question must conceal a scandal. The problem is that the existing public explanation conceals the obvious relationship.

## The defence cannot be that the AI is good

Perhaps this is a good use of AI. I'd argue it probably is. Municipal politics produces an obscene quantity of fragmented information. Council agendas arrive as PDFs built by people with an ancestral grievance against text extraction. Public notices hide on inconsistent websites. Local reporting is split across newspapers, newsletters, social accounts, Reddit threads, and the personal website of whichever journalist has accepted that somebody must maintain a public record.

A system that gathers this material and classifies it into useful issues could be genuinely valuable. I would probably use it. Hell I'd probably pay good money for it.

That does not rescue Stop the Datacentre from the contradiction. The movement is not arguing that only badly designed AI is objectionable. Its organizer form allows supporters to say they simply hate AI. Its associated organizations have called for banning AI in schools. Its public identity is not "responsible machine-learning deployment under carefully bounded conditions." It is "No AI". 

The answer therefore cannot suddenly become "this particular AI is useful" when the AI belongs to the movement. Everyone deploying AI thinks their use is useful. Every organization believes its own intentions are different. Every founder has an explanation for why this application is the sensible one. Stop the Datacentre may have accidentally discovered that the problem with generalized opposition to a technology is eventually finding your own exception.

## What the evidence does not prove

There are limits to the publicly available evidence, and I want to be clear about what's not said.

The feed does not identify a specific model provider. The Newscube site does not prove the project is incorporated or venture-funded. The DNS records do not conclusively reveal a shared Cloudflare account. The available material does not prove that Stop the Datacentre pays Newscube. It does not prove that campaign data trains a model. It does not prove that every local organizer knows how the feed works. It does not prove that Newscube was created primarily to serve political movements.

Those would be stronger claims. They are also completely unnecessary. The existing evidence is more than enough. Stop the Datacentre publicly identifies Wardroom as the system powering its campaign. Wardroom is a closed-source Hamilton intelligence dashboard, not a general-purpose campaign platform. Wardroom’s "Other projects" link goes directly to Newscube. Newscube says it builds systems for tracking and interpreting public information environments. Radar is presented as a live news-intelligence product. The Stop the Datacentre feed exposes an automated agent semantically classifying public posts and generating written rationales. The Vancouver campaign receives a feed still heavily weighted toward Wardroom’s Hamilton source environment. The DNS configuration strongly suggests common administration across the three domains.

## Powered by the thing it opposes

Stop the Datacentre is not merely a grassroots campaign that has touched AI. It appears to be the practical deployment for an AI startup. One interface monitors Hamilton municipal politics, another presents a general live-news product, and another interface powers a national campaign whose supports can formally declare they "just hate AI"

This is not a subtle contradiction. They aren't just using the technology they oppose, they appear to be helping launching it.

No AI, powered by AI.

## Addendum: The person behing the campaign also built the AI product

Since publishing this piece, I found the connection I had been circling around. It is one person.

Nick Tsergas appears to be the central technical organizer behind Stop the Datacentre. [The Breach](https://breachmedia.ca/inside-the-rising-country-wide-movement-against-ai-data-centres/) reports that the Hamilton campaign began after Tsergas found a blog post about the proposed development, contacted his councillor, and then built the website that became the campaign’s organizing infrastructure.

This would already make Tsergas more than a participant. He built the machine. His [personal website](https://nicktsergas.ca/) then supplies the missing half of the story:

> Nick is also the creator of Radar, a next-generation news intelligence platform that uses machine learning.

Radar is not an unrelated consulting project buried somewhere in his employment history. His biography describes it as a machine-learning system intended to help journalists, researchers, policymakers, and the public understand fast-moving information environments. Newscube lists Radar as one of its current systems. It describes the product as a "live news intelligence surface" and says Newscube develops systems for tracking and interpreting public information environments.

So the relationship is no longer an anti-AI campaign appears to use software connected to an AI startup. It is more accurately the person who built the national campaign website also created the machine-learning news intelligence product connected to the system powering it.

Stop the Datacentre is not an activist organization that unknowingly bought a software subscription from an outside vendor. It is not a coalition that discovered six months later that one of its WordPress plugins had added a machine-learning feature. The machine-learning product and the campaign infrastructure appear to come from the same builder. The remaining uncertainty concerns the boundaries between the applications, not the nature of the connection.

### This changes the hypocrisy argument

There is an important complication. Tsergas himself does not publicly claim to oppose AI. In fact, he told The Breach the opposite:

> I don’t have a problem with AI.

His stated position is that datacentres should not be allowed to proceed without regulation, consultation, and enforceable limits. That means it would be inaccurate to accuse Tsergas personally of pretending to hate AI while secretly building an AI company. He is not pretending. He appears to be fairly open, at least on his personal site, about building a machine-learning news product. His objection is to unregulated datacentre development, not to artificial intelligence as a category.

The contradiction belongs to the campaign he created. Stop the Datacentre has become infrastructure for groups whose politics go considerably beyond Tsergas' stated position. The campaign's own organizer form permits participants to select "I just hate AI" as one of their principal concerns. Its live monitor circulates material from explicitly anti-AI organizations. The broader movement has filled council chambers with chants of "Fuck AI."

This is not a hidden personal betrayal. It is an unresolved institutional contradiction sitting directly inside the project.

### The campaign is also a demonstration

There is another consequence. Stop the Datacentre now functions as a remarkably effective public demonstration of Radar’s underlying idea. A machine-learning news intelligence product needs to show that it can follow a fast-moving subject across fragmented sources, identify relevant developments, preserve continuity, and turn the results into a usable operational surface. Stop the Datacentre does exactly that.

I cannot establish from public evidence whether the campaign was deliberately created as a Radar case study, whether it is formally considered a Newscube deployment, or whether any campaign data is used to evaluate or improve the product. Those would require internal records. The practical result is visible regardless. The campaign built by Radar’s creator is demonstrating that his machine-learning intelligence system can be aimed at a national political issue. The anti-AI movement is helping prove the AI product works.

### The disclosure problem is now worse

"Powered by Wardroom" was already an incomplete explanation. It becomes more conspicuous once the personal connection is known. The campaign does not plainly say:

> Stop the Datacentre was built by Nick Tsergas, creator of the machine-learning news platform Radar.

It does not explain that Wardroom links into the same Newscube product family. It does not explain that the Live Monitor's automated classification is connected to technology built by the person operating the campaign platform. Instead, visitors receive the softest possible description: Wardroom is a civic-monitoring platform developed in Hamilton.

That is technically correct. It is also carefully much less informative than the facts. There may be no commercial arrangement. Newscube may not yet be a formal company. Stop the Datacentre may pay nothing for the software. The whole operation may be one person, several domains, and an alarming quantity of caffeine. None of that eliminates the need to explain the relationship.

The person collecting political organizer information, operating the campaign website, and developing the machine-learning intelligence product appears to be the same person. Readers should not have to reconstruct that through a personal biography, a product site, an "Other projects" link, public JSON, and DNS records.

### No AI, powered by its creator's AI

The original piece argued that Canada's anti-AI movement appeared to be powered by an AI startup. The evidence now supports a more precise formulation. Stop the Datacentre’s central website was built by Nick Tsergas. Nick Tsergas created Radar. Radar uses machine learning. Radar is presented by Newscube as a live news intelligence system. Stop the Datacentre says it is powered by Wardroom, while its own feed exposes an automated agent classifying public information.

Tsergas does not personally claim to hate AI. He did, however, build infrastructure for a movement that does. The campaign is not merely powered by the technology it opposes. It was apparently built by the person developing that technology.

That is considerably funnier. It is also the disclosure the footer leaves out.
