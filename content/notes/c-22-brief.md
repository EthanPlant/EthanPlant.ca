+++
title = "The Committee Report Has My Fingerprints on It"
description = "Parliament doesn't have git blame, but several amendmnets closely track recommendations in my brief"
date = 2026-07-19

[taxonomies]
tags = ["canada", "policy", "privacy"]
+++

[The C-22 report](https://www.ourcommons.ca/DocumentViewer/en/45-1/SECU/report-5/) looks familiar.

On May 26, [I submitted a brief](/files/Ethan-Plant-Brief-Bill-C-22-SECU.pdf/) to the House of Commons Standing Committee on Public Safety and National Security. In it, I argued that part 2, the proposed *Supporting Authorized Access to Information Act*, should be removed from the bill and studied seperately.

I submitted this fully aware it would almost certainly not happen. It would have required admitting that encryption, metadata retention, cybersecurity, ministerial orders, inspection powers and digital sovereignty might deserve more than whatever time remained before everyone fled Ottawa for the summer. So, I also proposed a handful minimum amendments. 

On June 18, the committee reported Bill C-22 back to the House with amendments. Several of them are remarkably similar to what I proposed.

This is where I need to be careful about causality. I cannot prove that my brief caused any particular amendment. Committees receive submissions from academics, civil-liberties groups, industry associations, lawyers, security researchers and assorted people with personal websites who have decided that reading federal legislation is now their problem. Different people are capable of looking at the same bad provision and arrive at the same repair mechanism.

Legislation doesn't have a commit history. There's no `git blame`. The report doesn't contain a helpful comment reading:
```
// Added after some infrastructure engineer in Vancouver became annoying by email.
```

And yet. The overlap is real.

## The decryption amendment

The strongest example is encryption. My brief argued that Bill C-22 should explicitly prohibit the government from requiring a provider to decrypt information it doesn't already possess in plaintext. I also identified several ways a government could achieve essentially the same result without ever issuing an order that contained the word *backdoor*: requiring key retention, introducing key escrow, preserving plaintext, disabling end-to-end encryption or redesigning a service so that the provider retains access in the future.

The principle was simple. Lawful access can reach information a provider already has. It should not force the provider to manufacture an ability to access information its own system was designed not to possess. 

The committee added a provision saying that the Act cannot compel a provider to decrypt information, or ensure that an authorized person can decrypt it, unless the provider supplied the encryption and possesses what is needed to decrypt it.

That isn't my exact wording, it is, however, almost exactly the boundary I described.

This is important because the political argument around encryption is usually flattened into whether the government supports or opposes "backdoors," as though a backdoor is a standardized appliance available from Home Depot. Real systems are weakened through design requirements. A service can be required to retain a key. A provider can be prevented from deleting a log. An access path can be kept alive after engineers would otherwise remove it. A privacy-preserving architecture can become illegal because it cannot satisfy an obligation written by someone who assumes every database has an administrator standing beside it with a large red **DECRYPT** button.

The committee amendment at least recognizes that access the provider does not possess is different from access it is refusing to provide.

## Repairing vulnerabilities

The report also adds protections against systemic vulnerabilities.

My brief said Bill C-22 should not permit the government to require providers to weaken, bypass, remove or redesign encryption and other security protections. I specifically included preventing the deployment, improvement or repair of those protections.

The repair part is easy to miss. A law doesn't necessarily need to order a company to create a vulnerability. It can instead require the company to preserve one. 

That is a distinction with a great deal of operational weight. Security teams spend an unreasonable portion of their lives finding access paths that exist for historical reasons, discovering that nobody knows whether they are still necessary, and then trying to remove them before someone on the internet discovers them first.

The committee amended both the regulation-making provisions and the compliance-order provisions so they cannot require a provider to introduce a systemic vulnerability or prevent the provider from rectifying one. "Preventing the provider from rectifying" is unusually close to the concern in my brief, it is also good language. Security is not a fixed property applied to a system when it leaves the factory. Systems change. Threats evolve. Cryptography ages. Dependencies develop opinions. An access mechanism considered acceptably narrow in 2026 may become a catastrophic liability after a protocol break, implementation flaw or clever teenager in Mississauga. A provider must remain allowed to repair its own systems.

## Section 47(1)(c)

Then, there is section 47(1)(c). This is what led me to writing this note.

The original provision allowed regulations respecting the meaning of any term or expression used in the Act.

> (c) respecting the meaning of any term or expression for the purposes of this Act;

My brief identified that paragraph by number and argued that regulations should not be able to redefine the bill’s core privacy and cybersecurity concepts.

> Amend section 47(1)(c), which allows regulations respecting the meaning of any term or expression for the purposes of the Act. Regulations should not be able to define, redefine, narrow, or expand core privacy and cybersecurity terms, including encryption, electronic protection, systemic vulnerability, electronic service, electronic service provider, core provider, metadata, transmission data, or authorized access. Those terms should be defined by Parliament.

The committee amended the provision so that this regulation-making power applies only to terms and expressions other than those already defined in the Act.

> (c) respecting the meaning of any term or expression for the purposes of this Act, other than the terms and expressions defined in it;

Again, this does not adopt everything I wanted. Undefined terms can still carry enormous consequences, and regulations remain capable of doing a lot of work beneath the pleasant administrative hum of the *Canada Gazette*. But it closes the most ridiculous possibility: Parliament defining a safeguard in legislation and then allowing the government to redefine that safeguard later through regulation.

## The partial victories

Other amendments move in the same direction without going nearly as far. I recommended deleting the generalized metadata-retention power. The committee kept it, but reduced the maximum retention period from one year to six months. It also required the Governor in Council to consider the category of metadata, and every element within it, essential to timely criminal or national-security investigations.

Six months is better than one year. It is still generalized retention. Targeted preservation says investigators have a reason to preserve particular information associated with a person, account, device or investigation. Generalized retention says providers should accumulate information about everyone because the government may find some of it useful later.

Those are not two points on the same administrative slider. Information that does not exist cannot be breached, stolen, misused or demanded. Once the government requires providers to retain it, the information becomes infrastructure. It needs storage, access controls, auditing, deletion machinery, incident response and lawyers. Eventually someone will discover that the supposedly narrow category can reveal far more than the comforting word metadata suggests.

The committee also strengthened review of ministerial orders. I proposed prior Federal Court authorization and a role for the Privacy Commissioner. The report instead makes Intelligence Commissioner approval mandatory and requires information to be provided to the National Security and Intelligence Review Agency.

That isn't necessarily the model I proposed, but it does recognize the same underlying problem: a minister should not be able to quietly impose requirements capable of changing the architecture of communications systems without meaningful independent scrutiny.

The report also narrowed subscriber-information production orders by requiring the judge to specify which categories of information must be produced and what information or transmission data they relate to. My brief had argued that these orders should be as specific as possible rather than operating as demands for every available piece of subscriber information.

Partial victories are still partial.

They are also victories.

## The report does not fix the bill

None of this means the committee report solved Bill C-22. Part 2 remains attached to the bill. Generalized metadata retention remains. The amended definition of systemic vulnerability still excludes certain risks involving information associated with people already subject to lawful authorization. That preserves the bill's strange habit of treating an access mechanism as safe because the government intends to use it only against an authorized target.

A privileged access path created for one legitimate use remains a privileged access path. It can be misused, expanded, copied, compromised or discovered. Legal authorization can determine whether someone is allowed to use a mechanism. It cannot determine whether the mechanism is technically capable of failing. That remains one of the bill's central conceptual failures.

Still, the report is materially better than the version the committee received. It contains an explicit decryption protection. It recognizes that preventing the repair of a vulnerability can be as dangerous as introducing one. It narrows a regulation-making power I identified by its exact paragraph. It shortens metadata retention, strengthens oversight and makes subscriber-information orders more specific.

I cannot honestly say that the committee adopted my amendments. I can say that I submitted a technical brief identifying specific problems, named the provisions that needed to change, proposed concrete safeguards and then opened the committee report to find several very similar changes staring back at me.

Public consultation rarely provides authorship credit. That's not really the point. The point is to put a defensible argument into the machinery while the machinery is still moving. Sometimes the machinery appears to listen.

I'll take it.
