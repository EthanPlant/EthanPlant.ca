+++
title = "Bridger Is Building an OSINT Dossier in a Cute Font"
description = "A close-friends app should not ask users to disclose the raw material for impersonation, stalking, phishing, and breach fallout"
date = 2026-06-10

tags = ["indieweb", "platforms", "privacy", "security"]
+++

I was scrolling through Instagram, because unfortunately I am not immune to doomscrolling, when I came across a reel advertising [bridger.social](https://bridger.social/). Bridger claims, in its own words, to be "A close-friends social media built to help you actually connect." The antithesis of modern-day social media.

That pitch, at first glance, is exactly the sort of thing that should get me very excited. A close-friends social app. No endless scroll. No influencers. No AI slop. No follower-count brain poison. No ad-choked feeds where the people you actually care about are buried under a mountain of algorithmic sludge.

Just a social app, for actual friends to stay in touch. A product that says, in effect, "you were not supposed to spend your life scrolling". A social media platform that says, "this belongs to you, the user".

I'm exactly the target audience for that type of claim. I care about digital sovereignty. I care about user control, exit rights, local-first software, open systems, and the difference between using software and being used by it. This whole website essentially exists because I think people should have homes on the internet that aren't just rented rooms inside other people's platforms. I am, admittedly, very easy to bait with the phrase "user-owned".

Naturally, I had some skepticism going in. I wondered what “user-owned” actually meant. I wondered whether the code would be open source. I wondered whether users would have real exit rights. I wondered about data portability, federation, self-hosting, APIs, contributor rights, governance, ads, and the business model. All of the boring questions that make the infrastructure engineer in me happy.

Then, I opened the beta. And I slowly realized I was looking at the most horrifying thing I've seen on the internet in a while.

My initial response to Bridger may have been a healthy dose of architectural skepticism. My second reaction was incident response. Because, as I explored the beta, the problem wasn't merely that Bridger hadn't fully explained its governance model.

The problem was the product was asking users to build an extensive identity dossier. Not metaphorically. Not in some abstract "all data collection is bad" sense. I mean it was asking for exactly the kinds of details that privacy, security, and OSINT people spend their time warning about.

## The trust problem started immediately

Bridger's sign-up flow managed to be both casual and invasive in the worst possible combination. To start, I never received any kind of validation email. No confirmation link. No verification code. No proof that I controlled the email address I had entered. As far as I can tell, the beta let me proceed without completing even the most basic account-integrity step.

It did, however, ask for my date of birth for "age verification". The box explicitly says: "Used for age verification only. Never shown to others."

![Bridger Sign-Up Birthday Box](/images/bridger-birthday.png)

There's a really strange set of priorities here. The product made no attempt to verify my email, arguably the most basic fraud prevention step, but demanded my date of birth immediately.

And date of birth isn't a throwaway field. It's identity-adjacent. It's frequently used in account recovery, identity matching, eligibility checks, advertising systems, and data-broker enrichment. It can become sensitive very quickly when combined with a name, email address, friend graph, location context, profile preferences, daily posts, and social activity.

It is also not, by itself, "age verification". A user typing a birth date does not prove their age. It proves they can type a birth date. Maybe that is acceptable for a low-risk beta. Maybe it's a placeholder while regulatory compliance is figured out. Maybe the team plans something more serious later. But then call it what it is. Do not collect date of birth under the reassuring label of "age verification" while leaving the rest of the trust model underdeveloped.

The sign-up flow also immediately asked for my first and last name. I gave it only my first name, because what, exactly, had this product done to earn my full government name? A close-friends app may need a display name. It may need a handle. It may eventually need billing information if someone becomes a paying member. It may allow users to share their real names with people they already trust. But “first and last name” immediately at signup is a very different choice. It treats real identity as the default.

I admit this is nitpicking a beta website's sign-up flow, and it's not out of the ordinary for a social media platform. But if a platform's public messaging is "we care deeply about privacy", I feel I reserve the right to be deeply critical.

The privacy-preserving version of this flow would ask: "What should your friends call you?" It would separate display identity from legal identity. It would make full names optional. It would explain clearly who can see them. It would not require more trust than the product has earned.

## Then came the profile flow

The profile setup flow is when things went from "concerning but it's a beta", to horrific.

Immediately I clicked on "Setup your profile" and I'm greeted by a 29-step workflow and a screen asking me to select my hobbies. A bit long, but no red flags so far.

![Bridger profile flow, hobby selection](/images/bridger-profile.png)

I clicked through a bit more and got asked for my birthday... again. During signup, Bridger asked for my date of birth for “age verification” and reassured me that it would never be shown. Then, later in the profile flow, it asks: “When is your birthday?” with month, day, and year fields, followed by visibility options: Close Friends, Wider Friend Group, and All Acquaintances.

![Bridger profile flow, birthday](/images/bridger-profile-birthday.png)

Maybe these are technically separate fields. Maybe the signup date of birth is intended for age gating, while the profile birthday is intended as an optional social field. That can be a legitimate distinction. But the product doesn't make that distinction, if any, clear to the user.

From the user's perspective: the app says give us your birth date, we will never show it. Then later it says: when is your birthday, and who can see it? That's a really confusing privacy flow for an end user.

There's also a data-minimization problem. If the purpose of collecting date of birth is age verification, the product may not need to store or expose a full date of birth as a profile attribute. It might need only an age-gate result, or an age range, or a minimum-age confirmation, depending on the legal and safety model. If the purpose is social celebration, it likely does not need the year. Age verification data should be treated as safety or compliance data. Birthday data should be treated as optional profile data. Those should not feel like the same onboarding chore wearing two different fonts.

A later section in the flow made the data-use problem even clearer. It asks the user for their sun, moon, and rising signs, and then helpfully says: "Don't know yours? Add your birthday earlier to auto-fill the sun sign." That means the birthday isn't just being collected as an isolated social field, it's being used to directly derive more metadata about a user.

Maybe zodiac signs are silly. Maybe nobody cares. But the pattern matters. The product says one thing at signup, asks again later, then uses birthday data to auto-fill another profile field. This is exactly why purpose limitation matters. Users need to understand what data is being collected, why it is being collected, where it appears, what it derives, and how else it will be used. "We'll never show this" is easy to write. The hard part is ensuring the product does not later reuse the same category of data in ways that surprise users.

Then, the flow kept going.

It asked for nicknames.

It asked for middle name.

It asked where I am from.

It asked what town I live in now.

It asked where I went to high school.

It asked where I went to college.

It asked what I studied.

It asked for current job title.

It asked for dream job.

It asked what pets I have.

It asked for my pets' names.

It asked who is in my family: parents, siblings, kids, anyone close.

It asked where I fall in line among siblings.

It asked for food allergies.

It asked for dietary restrictions.

It asked for ethnicity or heritage.

It asked for relationship status.

It asked for sexuality.

It asked for pronouns.

It asked for religion or beliefs.

It asked for zodiac signs.

It asked for important dates in my life.

And near the end, it asked: "Anything else friends should remember?"

I think I got about halfway through before muttering, "holy shit" under my breath.

This isn't *really* a quirky whimsical profile setup. This is asking users to hand over a comprehensive identity dossier in a [Lisa Frank](https://en.wikipedia.org/wiki/Lisa_Frank) skin.

I don't say that lightly. I've followed the OSINT space for around a decade by this point. I know exactly how dangerous this data can be in the wrong hands. The problem isn't that any one of these fields is always catastrophic by itself. The problem is that the flow assembles them together into a single profile.

A malicious person with access to that data would not merely know “fun trivia” about you. They would have the raw material for impersonation, stalking, phishing, account recovery attacks, social engineering, harassment, doxxing, discrimination, and intimate coercion.

Middle names, hometowns, schools, pet names, family structure, sibling order, and important dates are classic identity-verification and account-recovery material. They are the cursed security questions of the 2000s rebuilt as a social profile wizard. We all know the ones:

“What was the name of your first pet?”

“What high school did you attend?”

“What city were you born in?”

“What is your oldest sibling’s name?”

The industry spent years learning that these are terrible secrets because they are often guessable, searchable, memorable, and socially exposed. The industry has spent the better part of a decade trying to teach people that these types of questions are actually terrible security practice. Bridger’s beta appears to ask users to type that same information into a profile flow and then decide who can see each field. That should give anyone with even a brief understanding of cybersecurity chills.

## Sensitive data in a friendship costume

Then, there are the extremely sensitive categories. Ethnicity. Religion. Sexuality. Relationship status. Pronouns. Food allergies. Dietary restrictions.

Some of these may be harmless for some users in some contexts. For other users, they may be sensitive, risky, or genuinely dangerous. Sexuality may not be safely public. Religion may not be safely public. Ethnicity may expose someone to harassment. Pronouns may be a political target in some environments. Dietary restrictions may reveal health, religion, disability, pregnancy, recovery, or eating-disorder context. Relationship status can expose vulnerability, queer relationships, family conflict, breakups, abuse risk, or unwanted attention.

The correct design posture around data like this, especially for a platform claiming to care about privacy, is not cute curiosity. It is unrelenting restraint. Don't ask users for sensitive data without a clear purpose. Don't expose by default. Don't make users evaluate disclosure field by field while trying to complete onboarding. Don't bury visibility decisions inside a long tap-through flow.

Don't treat "skip this one" as if it solves the consent problem.

Admittedly, every field is optional. There's no hard requirement to fill in any of these. That doesn't make this okay. Optional is not the same thing as privacy-preserving. A long onboarding flow can pressure users without explicitly forcing them. It can normalize disclosure. It can make skipping feel like incompletion. It can make privacy feel like a chore. It can train users to answer first and think later.

By screen 17 of 29, or 21 of 29, or 28 of 29, most people are not conducting a careful threat model. They're just trying to finish the onboarding wizard. This is privacy fatigue as interface design.

## "All Acquaintances" is not a safety model

The visibility model makes it worse. Almost every screen asks: “who can see this?” with options like Close Friends, Wider Friend Group, and All Acquaintances.

That may sound like user control, but it's not enough.

First, "All Acquaintances" is not reassuring. Acquaintances are precisely the people who may be dangerous enough to know you and not close enough to trust. Coworkers, old classmates, ex-friends, friends-of-friends, mutuals, people from school, people from events, people you met twice, people who know just enough context to misuse information. That category cannot be meaningfully safe. There's a reason the data on your private Instagram and on your LinkedIn page are very different.

Second, visibility controls don't answer the data-governance question. Who can see a field in the UI is only one part of privacy. The deeper questions are: is the data stored? Is it indexed? Is it searchable? Is it logged? Is it used for recommendations? Is it used for matching? Is it used for advertising? Is it available to admins? Is it available to moderators? Is it included in exports? Is it deleted when removed? Is it retained in backups? Is it protected differently if sensitive? Is it encrypted? Is it accessible if the service is breached?

The UI only asks users "who can see this?" The real question is who can access it, infer from it, process it, retain it, correlate it, and lose it. Because, that's the catastrophic breach scenario.

Imagine a breach of this dataset. Not just emails and hashed passwords, although that would be bad enough. Imagine a breach containing full names, birth dates, towns, hometowns, schools, colleges, job titles, family members, sibling order, pets, pet names, food allergies, dietary restrictions, ethnicity, religion, sexuality, relationship status, pronouns, important life dates, and open-ended notes written specifically as things friends should remember.

That's a map of people’s private lives. It would be useful to scammers. It would be useful to stalkers. It would be useful to abusive partners. It would be useful to harassers. It would be useful to doxxers. It would be useful to identity thieves. It would be useful to anyone trying to impersonate someone, guess account-recovery answers, craft convincing phishing messages, or apply pressure using personal facts.

This is the reason why privacy people are always super annoying and paranoid-sounding. Because the fun little profile field is never just a fun little profile field once it's in a database somewhere.

## The free-text trap

Then there is "Important dates in your life."

That is a deceptively dangerous prompt. It could collect anniversaries, bereavements, sobriety dates, immigration dates, religious dates, medical dates, trauma dates, breakup dates, family events, court dates, or anything else someone considers important. The prompt is emotionally open-ended. The data could be deeply personal.

And then the final open-ended field: "Anything else friends should remember?"

I fully understand this is supposed to be Bridger's version of the "Bio" field on most social media platforms. An area of free text to write whatever you want on your profile. But consider the framing here, this is explicitly framed as "stuff your friends should remember". People will put everything in there.

Allergies. Trauma. Pronunciation. Neurodivergence. Estranged family. "Do not mention this around my parents." "I am sober." "I am not out at work." "My dad died in March." "I panic when people yell." "I have a medical condition." "I use a different name around family." "Please do not post photos of me." "I'm a minor."

The app doesn't need to intend harm for this to become extremely sensitive extremely quickly. That's the core issue here. A privacy failure is not always a villain twirling a moustache and selling data to the highest bidder. Sometimes it is a team building a cute product that doesn't fully understand the blast radius of the fields it is asking for.

The product claims to be about trust, friendship, privacy, and escaping the worst parts of social media. But the onboarding flow asks users to construct a semi-public memory palace of identity, family, location, education, work, health-adjacent information, beliefs, sexuality, relationships, pets, allergies, dietary restrictions, life dates, and anything else their friends should remember. Bridger doesn't just ask "who are you?" It asks "what would someone need to know to identify, locate, infer, remember, or socially engineer you?"

## "Privacy will be the top priority" is not an answer

Before I dived into the beta, I took some time to read the comments on the reel I saw. I wanted to get a vibe for what people were saying. Most of the comments were generic hype.

One in particular however, caught my eye. Someone asked Bridger a straightforward question: "will it be under GDPR?" Bridger's reply was simply, "Privacy will be the top priority!"

![Instagram comment conversation between user and Bridger on GDPR](/images/bridger-gdpr.jpeg).

They answered a very serious question, with a marketing slogan. The question was not "do you care about privacy?" The question was whether Bridger would be subject to a specific data-protection regime. That is an important legal, operational, and architectural question. It asks about scope, jurisdiction, users, processing activities, rights, lawful bases, deletion, export, retention, consent, processors, transfers, and safeguards. "Privacy will be the top priority!" doesn't answer any of that.

On its own, this might be forgivable. It was a social-media reply. Maybe the person writing it wasn't prepared to answer a regulatory question in a comment thread. Maybe the correct internal answer is more serious than the public reply. Maybe the team simply had not yet worked through the details. No one should be expecting a beta social media platform to have a compliance lawyer on standby.

But then, Bridger is asking for birth date, full name, birthday, hometown, current town, high school, college, field of study, job title, family structure, sibling order, pet names, food allergies, dietary restrictions, ethnicity or heritage, relationship status, sexuality, pronouns, religion or beliefs, important life dates, and open-ended personal notes.

At that point, "privacy will be the top priority" stops being a harmlessly vague answer. It becomes a real problem. A product asking for identity-adjacent, health-adjacent, relationship, location, family, religion, ethnicity, sexuality, and open-ended intimate data needs a serious privacy model.

Not vibes. Not reassurances. Not a marketing slogan with an exclamation mark. A model.

What data is collected? Why is it collected? What's optional? What's public by default? Who can access it? Is it retained after deletion? Is it exported? Can users request full deletion? What happens if the user is a minor? What happens if the data leaks? What happens if someone enters sensitive information into the final free-text box?

These are all the natural questions created by the shape of the product. This is why the GDPR reply matters. The right answer didn't have to be a legal memo. It could have been simple and honest: "We are still assessing our obligations before launch, especially if we offer Bridger to users in Europe. We know this matters, and we will publish a proper privacy framework before collecting sensitive data."

That would have been reassuring. Instead, the public answer amounted to: trust us, privacy matters. But the beta then asks for a long list of deeply personal information. That's playing incredibly fast and loose with privacy. And when a product is collecting this much sensitive information, fast and loose is exactly what people should be afraid of.

## The friend quiz makes it worse

And then, there is the "Friend Quiz".

The Friends page features a "Friend Quiz" box with the text "Add friends with profile data to unlock the quiz." At first glance, this seems like a fairly harmless dark pattern. Just an easy way to drive users to invite their friends to the platform.

That line becomes much darker after going through the profile flow. Because what, exactly, is the quiz about? If the quiz only unlocks once you have friends that have filled in an entire intelligence briefing into their personal lives, the obvious implication is that at least some of this profile data becomes quiz material.

The app asks users to provide deeply identifying and sometimes sensitive information, then appears to use "profile data" as the substrate for a friend quiz. It turns deeply personal knowledge into a gamified feature. It makes intimacy measurable. It makes remembering your friends into an app mechanic.

Again, maybe the actual quiz is harmless. Maybe it only asks cute questions. Maybe it avoids sensitive fields. Maybe the product has guardrails that are not visible in the beta flow. It's entirely possible that the quiz explicitly excludes sensitive fields such as sexuality, religion, ethnicity, allergies, or family details.

But the UI makes no attempt to communicate that. What it communicates is: add friends with profile data to unlock the quiz. After the profile flow I had just seen, that sentence doesn't read as charming. It fires like seven alarms in the threat model part of my brain.

This is the danger of collecting everything "for friendship." Once the data exists, the product starts finding uses for it. A birthday becomes a zodiac sign. A preference becomes a prompt. A profile field becomes quiz content. A private detail becomes an interaction surface. This is how platforms eat away at privacy, not all at once immediately. One cute feature at a time.

## The app says what the data is for

The somewhat hidden "View My Data" page makes this even more alarming.

![Bridger "View My Data" page](/images/bridger-data-use.png)

"Your answers to profile questions helps us connect you to people who have similar interests. Coming soon."

This should set off immediate alarm bells. The profile answers aren't just decorative flavour. They're not merely notes for your existing friends. The app says, quite clearly, those answers are intended to help connect users to people with similar interests.

This data becomes matching logic. It becomes discovery logic. It becomes recommendation logic. Whatever word you prefer, it means the profile data is product fuel. It is, at minimum, designed to feed matching or discovery logic.

The same page says hobby tags will "let you find people who share specific interests." It also has a section for "Closeness Tier Assignments," which appear to control whose content users can see.

There's a clear product shape emerging here. Bridger asks users for structured personal information. Some of that information is identity-adjacent. Some is sensitive. Some is useful for social engineering. Some is health-adjacent. Some is relationship, family, location, school, belief, or sexuality-related. Then the product tells users that their answers will help connect them to people with similar interests.

Maybe the current beta does not yet implement the full matching system. The page itself says all of this is coming soon. That doesn't make the concern weaker. It makes the roadmap visible. Bridger isn't just collecting personal data because friends might enjoy reading it. It is planning to use that data to connect, sort, and discover people.

This is exactly why data minimization matters. Once the data exists, the product starts finding uses for it. It has to, the incentives are too great to avoid it. A birthday becomes a zodiac field. A profile answer becomes quiz material. A hobby becomes a discovery tag. A personal detail becomes a matching signal.

And a "close-friends app" quietly becomes a system for structuring, searching, and acting on intimate social data.

## This should have been stopped

A team with a mature privacy and safety posture might look at this flow and stop it before it reached a semi-public beta. A privacy review would ask why these fields exist. A security review would ask what happens if they leak. A trust and safety review would ask how they can be abused. A data-protection review would ask whether the collection is necessary, proportionate, purpose-limited, optional in a meaningful way, default-private, and explainable. A product review would ask whether friendship actually requires any of this. A regulatory compliance review would ask what data-privacy regulations apply.

Apparently, that didn't happen. Or, if it did, the flow survived anyway. This is what happens when no one with sufficient authority is saying no.

No, you do not need the user's middle name.

No, you do not need their pets' names.

No, you do not need their sibling order.

No, you do not need their high school.

No, you do not need their current town public.

No, you do not need their ethnicity in a friend app.

No, you do not need their sexuality in onboarding.

No, you do not need their religion or beliefs.

No, you do not need important life dates.

No, you do not need a free-text field where users can deposit trauma in a cute box.

At least, you do not need any of that by default. And if you believe you do need it, the burden is on you to justify it with extraordinary clarity, restraint, and protection.

Bridger does not appear to be doing that in any visible or convincing way. Instead, the beta gives users a familiar platform move: provide personal data now, sort out visibility later.

## The broader lesson

I don't think every cute profile question is evil. I don't think a platform to connect with friends has to be sterile. I don't think every early beta should have the complete privacy authority of a bank.

But I do think a close-friends app needs to understand deeply what kind of data it is asking for. Friendship is intimate. That's the point. A product that mediates friendship is not collecting random preferences in a vacuum. It is collecting context about relationships, identity, memory, location, history, family, habits, beliefs, vulnerability, and trust.

That data has a blast radius. A social app for friends should ask for less, not more. It should default to completely private, not public. It should separate display identity from legal identity. It should never ask for sensitive categories unless there is an extraordinary reason. It shouldn't make users decide visibility for dozens of fields while trying to complete onboarding. It should not collect a birth date under the promise to never share it and then ask for birthday visibility later. It should not use personal data as feature fuel without making the boundaries painfully clear.

It should *never* turn intimacy into a structured dataset and then call that connection.

The most dangerous data is often not the data that looks dangerous. It is the cute stuff. The memorable stuff. The "your friends already know this anyway" stuff. The hometown, the school, the sibling order, the birthday, the important date, the dietary restriction, the relationship status, the thing friends should remember.

That's precisely why people will happily volunteer it. That's precisely why it's useful. And that's precisely why a privacy-conscious product should be careful with it.

Bridger says it wants to build a better social app for friends.

Good. Godspeed.

Just please don't ask your users to build an OSINT dossier in a cute font.
