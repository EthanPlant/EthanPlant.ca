+++
title = "Bill C-22 Is a Mess of the Government’s Own Making"
description = "The government’s lawful access bill is no longer merely controversial. It is badly designed, badly explained, badly consulted, and now being rushed anyway."  
date = "2026-05-27"

[taxonomies]  
tags = ["canada", "privacy", "policy", "digital-sovereignty"]  
+++

["A mess of the government's own making."](https://x.com/mgeist/status/2059393967255355498)

Those were the words used by Canadian digital policy expert Michael Geist following Tuesday's committee meeting regarding Bill C-22. And it's honestly the most generous way to describe what's happening.

The government introduced a sweeping lawful access bill. Experts warned that it was overly broad, vague, and technically risky. Major technology companies warned it could undermine encryption and secure systems. Civil liberties groups warned about surveillance powers. Privacy lawyers warned about the rule of law. A House of Commons petition calling for the bill's withdrawal surged into thousands of signatures almost immediately. Committee hearings became chaotic. Opposition MPs argued they didn't have enough information. The Privacy Commissioner's recommendations were apparently not distributed in advance. The government then accused critics of spreading misinformation, while simultaneously making misleading comments that needed to be walked back within hours.

This is not how a serious government should be handling a serious bill.

The government's basic defence has been that people are misunderstanding Bill C-22. We are told it doesn't require backdoors. We're told it does not create new lawful access authorities. We're told it's about modernization, public safety, and ensuring police and intelligence agencies can obtain information under existing legal authorities.

That might be more convincing if the people objecting were confused.

But they are not.

The coalition objecting to this bill includes privacy experts, civil liberties groups, digital rights organizations, major technology companies, VPN providers, legal scholars, software developers, and ordinary Canadians who have taken the time to read what the bill actually says.

At some point, the problem is not that everyone is misunderstanding the bill.

The problem is the bill.

## The government keeps answering the wrong question

[The government's major defence is that Bill C-22 doesn't require backdoors](https://www.canada.ca/en/services/policing/police/crime-and-crime-prevention/lawful-access.html#a4).

> Bill C-22 would not create "backdoors" and weakening of cybersecurity

> The Canadian Centre for Cyber Security defines a "back door" as a hidden mechanism that bypasses security controls. Bill C-22 does not require ESPs to create "backdoors" to their systems or the weaken electronic protections, including encryption.

> Bill C-22 does not alter the existing responsibility of ESPs to protect their networks from hacking or other unauthorized access. The Government of Canada will be required, by law, to consult impacted ESPs, both in the making of regulations and the issuance of Ministerial Orders, and take into account the potential impact on cost, cybersecurity and privacy protections.

But that doesn't answer the concern.

The concern is not only whether the bill uses the term "backdoor" or explicitly orders a company to bypass encryption. The concern raised by tech providers is whether the bill creates legal pressure for companies to retain data, preserve access capability, avoid deploying stronger encryption, build technical interfaces, comply with ministerial orders, or redesign systems so future access remains possible. That is the heart of this debate.

Modern secure systems are increasingly designed such that even the provider cannot access user content, encryption keys, logs, or other sensitive data. End-to-end encryption, zero-knowledge storage, and no-logs services are not loopholes designed to protect criminals. They're the basic foundation modern secure systems are built on. If a provider does not have access to data, it cannot leak it, misuse it, hand it over by mistake, expose it to insiders, or lose it in a breach.

A government can say they're not asking for a backdoor. But, if the practical effect of the law is to make providers preserve access capability that would not otherwise exist, the architecture still resembles a backdoor. 

## Apple's stark warning

In front of the Standing Committee on Public Safety and National Security, Apple gave the government a warning it should not be able to ignore.

> "As you know, this may be one of the last times we're permitted to discuss the consequences of this legislation publicly."

That line should hang over this entire debate.

Apple continued,

> "That's because of the bill's secrecy provisions which forbid companies like Apple from even discussing the orders we receive with our users or the public."

That is an extraordinary, and unsettling thing for a company to have to say to Parliament.

The government wants Canadians to trust that Bill C-22 will not be used to undermine encryption or secure systems. But, if companies can receive technical access orders and then be forbidden from telling users or the public about those orders, the government's reassurance becomes impossible to verify. It is one thing for the government to say, today, in public, that it does not intend to require backdoors or systemic vulnerabilities. It is another thing entirely to pass a law that may later prevent the affected companies from publicly explaining what they've been ordered to do. If the government's answer is "trust us" and the bill's secrecy provisions make it nearly impossible to check whether that trust has been earned, the bill has a serious democratic legitimacy problem.

It also matters who delivered that warning.

Apple's representative, Erik Neuenschwander is Apple's Senior Director of User Privacy and Child Safety. He is also a former software engineer. Erik is not just a PR spokesperson sent to repeat corporate talking points. He understands this reality deeply. He is the exact sort of person Parliament should listen to on a bill that touches encryption, privacy architecture, and technical access obligations. Parliament should have a very difficult time dismissing a senior privacy engineer telling them that this may be the last time his company is allowed to speak publicly on the consequences of legislation.

## The government's bizarre response

In response, the government seemed to decide that one of its best strategies was to press Apple on whether it has ever supported lawful access legislation elsewhere:

> "Has Apple ever gone before a Parliamentary committee or submitted a parliamentary brief a submission to a national parliament on a lawful access regime that Apple actually supported?" - Anthony Housefather

Michael Geist took to [X and summarized it well](https://x.com/mgeist/status/2059449267056513390):

> When government thinks its best approach is to target Apple - have you ever supported a lawful access bill anywhere? - you know they’ve lost the plot. Opportunity for real questions about privacy risks for millions of Canadians under Bill C-22 lost with strange line of questions.

Apple's position is really not difficult to understand. They'll happily comply with lawful requests for information they actually have. They will never support legislation that requires them to weaken security, preserve access they do not have, or redesign systems around government access. The government seems to want to collapse every objection into a refusal to support lawful access, but the serious critics aren't saying police should never obtain digital evidence. They're arguing lawful access must not require insecure architecture, suspicionless metadata retention, secret orders, or compelled redesign of systems where provider access doesn't exist.

Pressing Apple on whether it has supported lawful access laws elsewhere misses the point so badly that it becomes almost clarifying.

## Canada cannot normalize bulk metadata retention

Despite claims by the government to the contrary, metadata retention is not a routine feature of lawful access. The United States does not have a general mandatory data-retention law. The European Union's Data Retention Directive was struck down by the courts in 2014, with later European cases continuing to reject general and indiscriminate retention of communications data.

Canada needs to be especially cautious because Canadian law has already recognized that this information can be private. In *R. v. Spencer*, the Supreme Court of Canada recognized a privacy interest in subscriber information. *R. v. Bykovets* later affirmed this view as the Court held that an IP address can attract a reasonable expectation of privacy under section 8 of the Charter.

The government cannot wave away metadata retention by arguing it's "not content". It can't make the argument metadata is ["just phone book information"](https://www.michaelgeist.ca/2026/05/more-misinformation-on-bill-c-22-as-the-government-struggles-to-defend-its-lawful-access-plan/). Canadian constitutional law already moved past this idea. Metadata can be the key that links a person to their online activity.

## Clarifying metadata doesn't fix the problem

The government now appears to be looking for amendments. According to [CBC News](https://www.cbc.ca/news/politics/bill-c-22-encryption-cybersecurity-9.7213776), the public safety minister has said the government will propose changes "to ensure there's clarity on what encryption is", and to better define metadata in the legislation.

The problem with Bill C-22 is not merely that the words "encryption", "metadata", or "systemic vulnerability" need to be better defined. The problem is that the bill creates a power to require broad metadata retention in the first place. If the government wants to protect encryption, it should not merely define encryption. It should explicitly prohibit compelled weakening, bypassing, redesign, removal, or non-deployment of encryption and other privacy-preserving protections. If the government wishes to address metadata concerns, it should not merely define metadata more precisely. It should remove the suspicionless metadata retention power. At minimum, any preservation obligation should be targeted to a specific person, account, device, identifier, or investigation; based on individualized suspicion; authorized by a judge; time-limited; no broader than necessary; and subject to deletion once no longer required.

## The consultation problem is now part of the story

The government's position looks even weaker now that the consultation story is beginning to unravel.

[The National Post reported](https://nationalpost.com/news/politics/government-never-consulted-widely-on-contentious-part-of-police-search-powers-bill) that the government did not widely consult on the metadata retention aspect. The article's headline was deliberately blunt:

> Government never consulted widely on contentious part of police search powers bill

The article continues with a quote from Murray Rankin, former chair of the National Security and Intelligence Review Agency and a lead consultant for the government on the bill,

> "You know this business about the metadata, it never came up in our conversations. In my work, it never came up."

That is an incredibly damning quote. The metadata retention powers are one of the central problems with the bill, and if they were not properly tested with privacy experts, technical experts, civil liberties groups, providers, and the Privacy Commissioner, then something has gone seriously wrong with the process. And when the process is bad on a bill this technically sensitive and consequential, people are right to be alarmed. The government cannot accuse everyone else of misunderstanding a framework it apparently failed to properly explain, consult on, or stress-test before trying to legislate it.

## The committee problem itself is unraveling

[Geist summarized Tuesday's meeting of the committee bluntly](https://x.com/mgeist/status/2059442646678970469):

> What an embarrassment at Bill C-22 SECU hearing. Despite Liberal MPs admitting confusion, government trying to rush the bill through. CPC MPs call for more meetings and ability to hear from officials before submitting amendments. Meeting runs out of time before decision is made.

This is not a healthy committee process. If Liberal MPs are acknowledging confusion about the bill’s application, and Conservative MPs are asking how amendments can be submitted before hearing properly from officials, the answer should not be to rush ahead. The answer should be to slow down, to hear from officials, to ensure members actually understand the bill before they're expected to amend it. That's the only responsible choice, it's basic legislative competence.

[The Privacy Commissioner’s recommendations were apparently not distributed in advance](https://x.com/mgeist/status/2059371851546050957). Witnesses did not have enough time to be questioned properly. Members are being asked to think about amendments without having heard enough from officials. Even government MPs appear unsure about the bill’s application.

[Geist also pointed to another revealing moment](https://x.com/mgeist/status/2059393967255355498):

> When even Liberal MPs acknowledge the Bill C-22 confusion about its application given how vague it is, maybe there is a need to pull Part 2 until everyone figures it out? Extend the committee study to get it right? What a mess of the government's own making.

The government keeps insisting that the bill is being misunderstood. But the committee itself seems confused about the bill. Not because MPs are lazy, or because the critics are spreading hysterical misinformation, but because the bill is broad, vague, technical, and consequential.

This is not how Parliament should handle a law that touches encryption, metadata retention, ministerial orders, secrecy, provider obligations, and the architecture of secure systems. The government's attempt to press ahead is making their critics' argument for them.

## The defenders of the bill are not helping themselves

The committee hearing also exposed another problem for the government: some of the bill’s defenders appear to be making the critics’ case for them.

[Privacy lawyer David T.S. Fraser summarized what he saw at the meeting bluntly](https://x.com/privacylawyer/status/2059474721708667388):

> The main cheerleader of the lawful access (the Canadian Association of Chiefs of Police) wants to bypass encryption, and they appreciate that the Bill will do this.

The government keeps saying Bill C-22 doesn't require backdoors and doesn't threaten encryption. But if law enforcement witnesses understand the bill as helping them bypass encryption, then either the government’s assurances are wrong, or the bill is vague enough that major stakeholders are reading it in fundamentally different ways. Neither option is particularly reassuring.

Fraser continued:

> Apple and Google know how this actually plays out in practice, and how dangerous unclear terminology can be.

This is the divide at the heart of this debate in one sentence. The government is talking about lawful authority. Meanwhile Apple, Meta, Google, privacy lawyers, and security experts are talking about implementation. A statute can say an access path is lawful. That does not make the access path safe. A statute can say a provider must not introduce a systemic vulnerability. That does not answer whether an authorized exceptional access mechanism is itself a security risk. A statute can say an order is secret. That does not make the order democratically accountable.

Fraser was even more blunt about the law enforcement testimony:

> The police know shockingly little about the supreme law of our land, and don’t like how much paperwork and bureaucracy it takes to get a judge to permit them to intrude into people’s private lives.

> The police who are trotted out to defend the Bill don’t actually understand it.

Harsh? Absolutely. But also the kind of criticism that should make Parliament pause. If the government is relying on law enforcement witnesses to justify a technically complex lawful access framework, those witnesses need to be able to explain what the bill actually does, how it interacts with existing *Criminal Code* powers, and why its new powers are necessary. Instead, Fraser argued:

> The police haven’t actually noticed the lawful access provisions they’ve successfully lobbied to have added to our Criminal Code in the last 25 years.

Bill C-22 is being sold as necessary modernization, and perhaps parts of it are. But if existing lawful access tools are being ignored, misunderstood, or underused, Parliament should be very cautious about creating a new and broader framework layered on top of them.

Fraser's last comment sums up the hearing:

> The government is doing everything they can to stymie critique and amendments to the Bill.

This is not just a fight over privacy in the abstract sense. It has now become a fight over whether Parliament is being given the time, information, and seriousness needed to study a bill that affects millions of Canadians. If the government’s own MPs are confused, if opposition MPs are asking for more time with officials, if the Privacy Commissioner’s recommendations were not properly available in advance, if Apple is warning that it may soon be barred from discussing the consequences publicly, and if privacy lawyers are leaving the hearing saying the defenders of the bill do not understand it, then there is no responsible argument for rushing ahead.

## The broad, uncomfortable coalition

On one side, the government and law enforcement witnesses are insisting the bill is necessary, while struggling to explain its technical and legal implications clearly.

On the other side, Meta, Google, Apple, the Electronic Frontier Foundation, OpenMedia, civil liberties organizations, privacy experts, VPN providers, the Barreau du Québec, and Canadian internet law scholars are all pointing at similar problems.

The contrast here is hard to miss. These groups do not have the same incentives. Meta and the EFF are effectively sworn enemies. Google and civil liberties groups usually don't arrive at the same conclusions. VPN providers and law professors don't share a single political lane. But, on Bill C-22, they're converging.

That should make Ottawa incredibly nervous. When only one group objects, it's easy to categorize the opposition. Privacy activists oppose surveillance. Big tech companies oppose regulation. Opposition MPs oppose the government. But, when all of the voices begin reaching the same conclusion from different starting points, and begin saying the same thing in different languages, the government should stop assuming the criticism is unserious.

The shared concern between all these groups is the same. Bill C-22, as written, is too broad, too vague, and too technically risky. Its safeguards don't do enough. The metadata retention power is a gross violation of privacy. The ministerial order framework should be narrowed or removed. Encryption and privacy-preserving architecture needs to be explicitly protected. Core privacy and cybersecurity terms should not be left open to later redefinition by regulation.

## The petition is moving fast

[There is now a House of Commons petition calling for the withdrawal of Bill C-22](https://www.ourcommons.ca/petitions/en/Petition/Details?Petition=e-7416). In less than a day it reached the necessary 500 signatures for an official response. At the time of writing, only a few days after the petition went live, there are over 7000 signatures, from every province and territory.

Now, a petition itself isn't going to stop a bill alone. Bill C-21 passed despite having one of the largest petitions in Canadian history against it. It is, however, a formal public record. It tells MPs and the government that public concern is not confined to committee witnesses or niche policy circles. It shows, quite clearly, that the government cannot credibly treat this as a tiny local objection or narrow technical complaint. There is a national concern coming from experts, companies, civil liberties groups, and ordinary Canadians. And the government is required by law to give a response.

## Digital sovereignty requires trust

This is also why Bill C-22 remains nearly impossible to reconcile with Canada's stated digital sovereignty ambitions. The Canadian government has said repeatedly it wants trusted, domestic digital infrastructure. If that's the case, then it should not create legal uncertainty around encryption, metadata retention, compelled technical capability, secrecy, and provider access.

Digital sovereignty means more than having a data centre in Vancouver, or a server rack in Ottawa. It's about whether people can trust the systems built and operated under Canadian law. A Canadian cloud provider doesn't become trustworthy merely because its servers are in Canada. A Canadian messaging service doesn't become trustworthy merely because the company has a maple leaf in its logo. A Canadian digital system doesn't become trustworthy because a policy document uses the word "sovereignty".

If Canadian law makes secure-by-design systems harder to build, Canada will not become more digitally sovereign. It will become more dependent on the largest incumbents that can absorb compliance costs, negotiate with government, and build special-purpose compliance systems.

We are already seeing the impacts of this. Signal has warned it would rather leave Canada than break its privacy promises to users. NordVPN has said it would consider leaving Canada if the bill requires it to compromise its no-logs architecture or encryption protections. DuckDuckGo has announced it would withdraw its VPN service. Windscribe, a Canadian VPN provider headquartered in Toronto, has warned it may relocate if the bill passes in its current form. Tailscale, another Canadian-founded secure infrastructure company, has warned that Bill C-22 risks turning data minimization from a security virtue into a compliance problem. These are exactly the kinds of companies Canada should want to keep if it is serious about digital sovereignty.

## Ottawa should listen while it still can

The government can still pass Bill C-22 if it wants to. The Liberals have a majority after all. But, having the votes is not the same as having a good bill. Right now, the government appears to have under-consulted on metadata retention, failed to reassure the experts, failed to clearly protect encryption, failed to explain the technical implications, and failed to manage the committee process cleanly. That's a lot of failures for a bill being sold as a necessary modernization.

A government that is still clarifying what its bill means shouldn't be racing to make it law before summer. It should be slowing down, addressing the concerns, and ensuring MPs have the information needed to meaningfully make amendments.

One thing is clear though, Bill C-22 cannot proceed in its current form. In the best case, Part 2, the proposed *Supporting Authorized Access to Information Act* should be split out and studied on its own. Part 2 is a broad legal framework that cannot be meaningfully discussed when it's considered a footnote to procedural amendments to the *Criminal Code*. If the government refuses to do that, it should at least amend the bill to explicitly protect strong encryption, remove suspicionless metadata retention, limit ministerial orders, strengthen independent review, and protect systems where provider access does not exist by design.

Ottawa should not wait for a Charter challenge, a provider exit, a cybersecurity incident, or an international trust problem to discover the critics were not confused. But that the critics were trying to warn them.

The government still has the time to turn this into a serious bill. But first, it needs to admit the problem isn't public misunderstanding or big tech spreading misinformation.

The problem is the bill.
