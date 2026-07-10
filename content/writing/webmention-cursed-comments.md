+++
title = "Abusing Webmention to Build a Comments Section"
description = "A small architecture document about dynamically manufacturing the independent web"
date = 2026-07-09

[taxonomies]
tags = ["indieweb", "personal-sites", "web"]
+++
[Webmention](https://indieweb.org/Webmention) has one job.

A page links to another page. The first site tells the second site. The second site fetches the first page, confirms the link exists, and records the mention.

That is the entire protocol.

It does not ask why the page exists. It does not ask who wrote it. It does not ask whether it was lovingly published from a personal website, emitted by a static-site generator, or generated four milliseconds ago by the same server receiving the Webmention.

This leaves open a cursed but entirely workable architecture.

A reader types a comment into a conventional comment form. The server stores the comment, invents a source URL for it, and serves a tiny HTML page at that URL containing almost nothing except a link back to the article.

The server then sends itself a Webmention.

The receiver fetches the invented page. The link is there. The Webmention is valid.

Congratulations. We have built a comments section by dynamically fabricating evidence that every comment originated elsewhere on the web.

## The basic trick

Suppose an article lives here:

```
https://ethanplant.ca/writing/webmention-cursed-comments/
```

A reader submits this comment:

```
This is deranged. Ship it.
```

The server creates a comment record with an identifier:

```
01JABCDEF123456789
```

It then assigns the comment a source URL:

```
https://example.com/comments/source/01JABCDEF123456789
```

That URL returns:

```html
<!doctype html>
<html lang="en-CA">
  <head>
    <meta charset="utf-8">
    <title>Comment source</title>
    <meta name="robots" content="noindex, nofollow">
  </head>
  <body>
    <a href="https://ethanplant.ca/writing/webmention-cursed-comments/">
      In reply to this article
    </a>
  </body>
</html>
```

There does not need to be anything else.

The comment itself can remain in the database. The generated page exists only to satisfy the protocol’s verification rule: the source URL must contain a link to the target URL.

The application then sends:

```http
POST /webmention
Content-Type: application/x-www-form-urlencoded

source=https%3A%2F%2Fethanplant.ca%2Fcomments%2Fsource%2F01JABCDEF123456789
&target=https%3A%2F%2Fethanpplant.ca%2Fwriting%2Fwebmention-cursed-comments%2F
```

The receiving server fetches the source URL, finds the backlink, and accepts the Webmention.

The source page exists.

It links to the target.

Every individual statement in the transaction is true.

The story they collectively tell is complete bullshit.

## Goals

The system should:

- provide a normal comment form beneath an article;
- represent submitted comments as Webmentions;
- run local comments and external Webmentions through the same moderation pipeline;
- assign every comment a stable source URL;
- support editing and deletion;
- preserve the difference between an on-site comment and an external reply;
- avoid becoming an open Webmention-spam service;
- remain technically compliant enough that the protocol has no formal basis on which to complain.

The system should not require commenters to own a website.

That is the practical reason for doing any of this. Webmention is lovely when everyone involved has a personal website and knows what `rel="webmention"` means. Most people do not. They have a browser, an opinion, and perhaps five seconds of patience.

A comment form handles that problem.

Everything after the form is where the architecture develops a fever.

## System overview

The design has six parts:

```
┌─────────────────────┐
│ Article page        │
│                     │
│ Article content     │
│ Comment form        │
└──────────┬──────────┘
           │
           │ POST /comments
           ▼
┌─────────────────────┐
│ Comment service     │
│                     │
│ Validate submission │
│ Store comment       │
│ Assign source URL   │
└──────────┬──────────┘
           │
           │ Queue source + target
           ▼
┌─────────────────────┐
│ Webmention sender   │
└──────────┬──────────┘
           │
           │ POST source + target
           ▼
┌─────────────────────┐
│ Webmention receiver │
└──────────┬──────────┘
           │
           │ Fetch source
           ▼
┌─────────────────────┐
│ Generated source    │
│                     │
│ Tiny HTML page      │
│ Backlink to article │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Moderation and      │
│ comment rendering   │
└─────────────────────┘
```

The sender and receiver can run in the same process.

The service therefore accepts a comment, creates a fake external source for it, and makes an HTTP request to itself to announce what it just did.

This is the kind of architecture that would receive praise in a sufficiently expensive enterprise diagram.

## Comment submission

The article contains an ordinary form:

```html
<form method="post" action="/comments">
  <input
    type="hidden"
    name="target"
    value="https://ethanplant.ca/writing/webmention-cursed-comments/"
  >

  <label>
    Name
    <input name="author_name" required>
  </label>

  <label>
    Website
    <input name="author_url" type="url">
  </label>

  <label>
    Comment
    <textarea name="content" required></textarea>
  </label>

  <button type="submit">Post comment</button>
</form>
```

The browser submits:

```http
POST /comments
Content-Type: application/x-www-form-urlencoded

target=https%3A%2F%2Fethanplant.ca%2Fwriting%2Fwebmention-cursed-comments%2F
&author_name=Alice
&author_url=https%3A%2F%2Falice.example%2F
&content=This+is+deranged.+Ship+it.
```

The server must not trust the supplied target.

The target should be resolved against the site’s own content index and converted to a canonical local URL. A hidden form field is a convenience for the browser, not a declaration from God.

The resulting database record might look like this:

```json
{
  "id": "01JABCDEF123456789",
  "target_url": "https://ethanplant.ca/writing/webmention-cursed-comments//",
  "source_url": "https://ethanplant.ca/comments/source/01JABCDEF123456789",
  "author_name": "Alice",
  "author_url": "https://alice.example/",
  "content": "This is deranged. Ship it.",
  "created_at": "2026-07-09T21:14:00-07:00",
  "updated_at": "2026-07-09T21:14:00-07:00",
  "moderation_status": "pending",
  "delivery_status": "not_sent"
}
```

The source URL is derived from the immutable comment identifier.

The target is stored with the comment.

Neither should be reconstructed later from arbitrary query parameters, because that is how a funny architecture document becomes an incident report.

## The manufactured source

The core endpoint is:

```
GET /comments/source/{comment_id}
```

The handler:

1. loads the comment record;
2. confirms that the comment is active;
3. reads the stored canonical target;
4. emits an HTML document containing a link to that target.

The target does not come from the request.

The endpoint should not look like this:

```
/comments/source?target=https://somewhere.example/post
```

That design allows anyone to manufacture a page on your domain linking to any URL they choose.

Add a public Webmention sender and you have built a distributed spam cannon with excellent standards compliance.

The source URL should instead be opaque:

```
/comments/source/01JABCDEF123456789
```

The server decides what that source links to based on the persisted comment record.

## Empty source mode

The purest version returns only the backlink:

```html
<!doctype html>
<html lang="en-CA">
  <head>
    <meta charset="utf-8">
    <title>Comment source</title>
    <meta name="robots" content="noindex, nofollow">
  </head>
  <body>
    <a href="https://ethanplant.ca/writing/webmention-cursed-comments/">
      Article
    </a>
  </body>
</html>
```

The Webmention receiver recognizes that the URL belongs to the local comment-source namespace and retrieves the comment text from the database.

This means the public source contains no useful representation of the comment.

It is a URL-shaped receipt.

A person visiting it learns only that the source links to the article. An archive preserving it captures an empty room with a single door. A generic Webmention parser sees an ordinary mention with no meaningful content.

This is the most cursed implementation and therefore the reference design.

## Self-describing source mode

A slightly more respectable implementation renders the comment as microformatted HTML:

```html
<!doctype html>
<html lang="en-CA">
  <head>
    <meta charset="utf-8">
    <title>Comment by Alice</title>
    <meta name="robots" content="noindex">
  </head>
  <body>
    <article class="h-entry">
      <a
        class="u-in-reply-to"
        href="https://ethanplant.ca/writing/webmention-cursed-comments/"
      >
        In reply to this article
      </a>

      <div class="e-content">
        <p>This is deranged. Ship it.</p>
      </div>

      <footer>
        <span class="p-author h-card">
          <a class="u-url p-name" href="https://alice.example/">
            Alice
          </a>
        </span>

        <time
          class="dt-published"
          datetime="2026-07-09T21:14:00-07:00"
        >
          9 July 2026
        </time>
      </footer>
    </article>
  </body>
</html>
```

This has real advantages.

Generic Webmention parsers can understand it. The source URL becomes a usable permalink. The page can be archived. The comment exists somewhere other than a database row known only to the application.

Unfortunately, this starts resembling a legitimate publishing system.

At some point the fake source becomes a real page, and the entire exercise collapses into the normal idea that a reply can have its own URL.

That would be too sensible.

## Receiving the Webmention

The receiver handles external and manufactured Webmentions through the same entry point:

```http
POST /webmention
```

The normal verification flow remains:

1. validate `source` and `target`;
2. confirm that the target belongs to the local site;
3. fetch the source;
4. follow redirects within policy;
5. confirm that the source links to the target;
6. classify the mention;
7. place it into moderation or update an existing record.

Local source URLs receive additional handling.

```rust
fn process_webmention(
    source: Url,
    target: Url,
) -> Result<Mention> {
    verify_local_target(&target)?;

    let document = fetch_source(&source)?;
    verify_backlink(&document, &target)?;

    if let Some(comment_id) = local_comment_id(&source) {
        let comment = comments.load(comment_id)?;

        ensure!(comment.source_url == source);
        ensure!(comment.target_url == target);

        return Ok(Mention::LocalComment(comment));
    }

    let external = parse_external_mention(
        source,
        target,
        document,
    )?;

    Ok(Mention::External(external))
}
```

The receiver could skip the fetch for known local sources. It already has the database record. It knows what the source endpoint will return because it owns the source endpoint.

It should fetch it anyway.

Otherwise the Webmention layer becomes purely decorative, and we did not come this far to abandon the ceremony at the final checkpoint.

## Data model

Comments and Webmentions should remain separate records.

A comment is the user submission:

```
Comment
-------
id
target_url
source_url
author_name
author_url
author_email_hash
content
created_at
updated_at
moderation_status
spam_score
submission_ip_hash
```

A Webmention is the delivery and verification record:

```
Webmention
----------
id
source_url
target_url
received_at
last_verified_at
mention_type
processing_status
moderation_status
comment_id nullable
parsed_metadata
```

The nullable `comment_id` associates a manufactured Webmention with its local comment.

This permits several useful states:

- the comment exists but has not yet produced a Webmention;
- delivery failed and needs to be retried;
- the same Webmention was received again after an edit;
- the comment was deleted but the delivery audit remains;
- an external Webmention has no local comment record;
- moderation can operate across both systems without pretending they are identical.

A unique constraint should exist on:

```
(source_url, target_url)
```

Repeated submissions should update the existing mention.

A Webmention sender retrying after a timeout should not produce seven copies of the same comment beneath the article. Everyone involved has suffered enough.

## Moderation

The simplest moderation flow is:

```
submitted
    ↓
pending moderation
    ↓
approved
    ↓
source endpoint activated
    ↓
Webmention sent
    ↓
mention rendered
```

Rejected comments never become public source pages and never generate Webmentions.

This is cleaner than creating the source immediately and relying on the Webmention receiver’s moderation queue. External Webmentions exist independently of the receiving site’s approval. Local comments do not. Pretending otherwise gains nothing except more public URLs containing slurs.

The moderation system should support:

```
pending
approved
rejected
spam
hidden
deleted
```

The distinction between `rejected`, `hidden`, and `deleted` is operationally useful.

A rejected comment never becomes public.

A hidden comment was once visible but has been removed from display.

A deleted comment has had its public source and backlink withdrawn.

These states carry different expectations for records, audit trails, and reprocessing.

## Editing

Editing a comment keeps the source URL stable.

The application updates the comment record and sends the same Webmention again:

```
source=https://ethanplant.ca/comments/source/01JABCDEF123456789
target=https://https://ethanplant.ca/writing/webmention-cursed-comments/
```

The receiver interprets this as an update and reprocesses the source.

In self-describing mode, the fetched document contains the new content.

In empty-source mode, the fetched document looks exactly the same. The receiver notices that the source belongs to a local comment, loads the database record, and obtains the edited text from there.

The protocol exchange is now ceremonial twice over.

This is acceptable. We have standards.

An optional revision table can preserve edit history:

```
CommentRevision
---------------
comment_id
revision
content
edited_at
editor
```

The public interface only needs to show that the comment was edited. It does not need to expose every typo unless that is an explicit site policy.

## Deletion

Deleting a comment must remove the backlink.

One option is for the source endpoint to return:

```http
HTTP/1.1 410 Gone
```

Another is to leave a tombstone:

```html
<!doctype html>
<html lang="en-CA">
  <body>
    <p>This comment has been deleted.</p>
  </body>
</html>
```

The tombstone must not link to the target.

After changing the source, the application sends the Webmention again. The receiver fetches it, discovers that the backlink has disappeared, and removes or tombstones the received mention.

This follows the ordinary Webmention deletion pattern: the source no longer supports the claimed relationship.

The receiver should not delete a mention after one failed fetch. Networks fail. DNS has moods. The server may be restarting because somebody changed a TOML file and discovered consequences.

Permanent deletion should require either:

- an explicit `404` or `410`;
- a successful fetch with no backlink;
- repeated failures over a configured period.
    

## Identity

A manufactured source does not prove that the commenter owns anything.

The form may collect a name and website URL:

```
Alice
https://alice.example/
```

Unless the application verifies the relationship, those are user-supplied fields.

The generated page must not imply that Alice owns the comment system’s domain. It should also not imply that the comment originated on `alice.example`.

The renderer should say:

```
Alice commented through this site
```

not:

```
Alice replied from alice.example
```

External Webmentions can be rendered differently:

```
Bob replied from bob.example
```

These interactions share infrastructure. They do not share provenance.

That is the line the protocol itself cannot preserve, because it only sees URLs and links. The application has more information and should not throw it away for aesthetic consistency.

## Rendering

The article page queries approved mentions associated with its canonical URL.

Local comments and external Webmentions can appear in one response section:

```
Responses

Alice commented through this site

    This is deranged. Ship it.

Bob replied from bob.example

    I hate that this works.

Carol mentioned this article from carol.example
```

The internal mention types should remain explicit:

```
local_comment
external_reply
external_like
external_repost
external_mention
```

A local comment should not acquire fake IndieWeb prestige merely because the application forced it through a Webmention endpoint.

The point of sharing a pipeline is to reduce duplicated moderation and rendering logic.

The point is not to erase where the response came from.

## Likes, Because Apparently the Comments Were Not Cursed Enough

The same mechanism can build a like button.

This is worse.

A comment at least contains a sentence. There is some plausible argument that a reply deserves a stable URL, even if the application generated that URL on the commenter’s behalf.

A like is a boolean with aspirations.

The article displays a button:

```html
<form method="post" action="/likes">
  <input
    type="hidden"
    name="target"
    value="https://ethanplant.ca/writing/webmention-cursed-comments/"
  >

  <button type="submit">Like</button>
</form>
```

When a reader clicks it, the server:

1. validates the local target;
2. creates a like record;
3. assigns the like an immutable source URL;
4. activates a generated HTML page;
5. sends a Webmention from that page to the article;
6. receives the Webmention;
7. fetches the generated page;
8. confirms that the page claims to like the article;
9. increments a number beside a heart.

The source URL might be:

```
https://ethanplant.ca/likes/source/01JLIKE123456789
```

The generated page can be almost offensively small:

```html
<!doctype html>
<html lang="en-CA">
  <head>
    <meta charset="utf-8">
    <title>Like</title>
    <meta name="robots" content="noindex, nofollow">
  </head>
  <body>
    <article class="h-entry">
      <a
        class="u-like-of"
        href="https://ethanplant.ca/writing/webmention-cursed-comments/"
      >
        Liked this article
      </a>
    </article>
  </body>
</html>
```

The backlink satisfies Webmention verification.

The `u-like-of` property tells the receiver how to classify the interaction.

The page does not need a paragraph. It does not need an argument. It does not need to be useful to a person in any way. Its entire public purpose is to declare that one database row has positive feelings about another URL.

This is a single-purpose website created because someone clicked a heart.

### Data model

A like should remain distinct from the Webmention generated on its behalf:

```
Like
----
id
target_url
source_url
actor_id
created_at
status
```

The associated Webmention record uses the same source and target pair:

```
Webmention
----------
source_url
target_url
mention_type = local_like
like_id
processing_status
```

The application should enforce a uniqueness constraint on:

```
(actor_id, target_url)
```

Without this, repeated clicks can create multiple source pages for the same actor and target.

A sufficiently enthusiastic reader could otherwise generate a small subdivision of websites, all independently announcing their admiration for the same article.

The renderer should preserve provenance:

```
12 people liked this through this site
```

This should not be presented as twelve independent websites liking the article merely because the system created twelve independent-looking URLs.

Again, the application knows more than the protocol. It should behave accordingly.

### Unlike

Removing a like requires withdrawing the source-target relationship.

The system can return:

```http
HTTP/1.1 410 Gone
```

or leave a tombstone without the `u-like-of` link:

```html
<!doctype html>
<html lang="en-CA">
  <body>
    <p>This like has been withdrawn.</p>
  </body>
</html>
```

It then sends the Webmention again.

The receiver fetches the source, discovers that the like relationship no longer exists, and removes the reaction.

A person has changed their mind. The application must now generate updated public evidence of that emotional reversal, formally deliver it to itself, inspect the evidence, and adjust the counter.

We have recreated clicking a heart using the procedural mechanics of withdrawing a diplomatic communiqué.

### Actor identity

The like must be associated with some local identity.

That might be:

- an authenticated account;
- an IndieAuth identity;
- a signed browser token;
- a verified email address;
- an anonymous session.

An anonymous session can prevent accidental duplicate likes, but it cannot establish durable identity across browsers or devices.

An account provides stronger uniqueness, but it also turns the site into an account system, which is traditionally where a tiny feature begins demanding password resets, abuse tooling, privacy policies, data exports, and an opinion about passkeys.

There is no free heart.

Whatever identity model is chosen, the generated source must not falsely imply that the actor owns the site’s domain or independently published the reaction.

A local like is still a local like.

The fake source URL is protocol machinery, not provenance.

### Other reactions

Once the application can manufacture one kind of source page, it can manufacture all of them:

```html
<a class="u-like-of" href="...">Liked</a>
<a class="u-repost-of" href="...">Reposted</a>
<a class="u-bookmark-of" href="...">Bookmarked</a>
<a class="u-in-reply-to" href="...">Replied</a>
```

This creates the possibility of a complete local interaction system built on generated Webmention sources:

```
comment
like
repost
bookmark
RSVP
follow-shaped mistake
```

Each button creates a tiny page.

Each tiny page announces a relationship.

Each relationship is delivered through Webmention.

Each Webmention is fetched and verified by the same application that created it.

The website stops being a site with reactions and becomes a company town of Potemkin personal websites, each established for the sole purpose of filing one emotional declaration with city hall.

### Security invariants

The same protections required for comments apply here:

```
source host == configured local host
source path == registered like-source path
source id exists in the database
source target == stored local target
actor is authorized to create or remove the like
one active like exists per actor and target
delivery originates from the internal outbox
```

The client must not be able to choose an arbitrary target.

The public must not be able to create arbitrary source pages.

The sender must not accept unrestricted source-target pairs.

Without those constraints, the like button is no longer merely cursed. It becomes a mechanism for making the site’s domain endorse arbitrary pages across the web.

That is less a reaction feature and more a reputational denial-of-service attack.

### Final assessment

Comments make this architecture look complicated. Likes reveal what it actually is.

The system creates public documents whose only purpose is to satisfy the verification requirements of another subsystem on the same server. The documents carry almost no content. They exist because the protocol understands relationships between URLs, not rows in a local database.

So the application gives every row a URL.

Then it makes the URL testify.

Then it cross-examines the testimony.

Then it updates the heart count.

It works.

That is the problem.

## The Database Is Optional

The most cursed version does not even have a database.

The earlier design assumes that a comment or like exists as an ordinary application record. The generated source URL is merely a public representation of that record.

That is responsible engineering, slightly deranged engineering, but competent engineering.

We can unfortunately do worse.

Instead of storing the interaction, the application can encode the entire thing into a signed URL:

```
https://ethanplant.ca/interactions/{payload}.{signature}
```

The payload contains everything required to reconstruct the source page:

```json
{
  "version": 1,
  "kind": "comment",
  "target": "https://ethanplant.ca/writing/webmention-cursed-comments/",
  "author": {
    "name": "Alice",
    "url": "https://alice.example/"
  },
  "content": "This is deranged. Ship it.",
  "published_at": "2026-07-09T22:14:00-07:00",
  "nonce": "01JABCDEF123456789"
}
```

The server serializes the payload, signs it, and places both values in the URL.

When the source URL is requested, the server:

1. extracts the payload;
2. verifies the signature;
3. checks the payload version;
4. validates the target against the permitted local origin;
5. reconstructs the source document;
6. returns the resulting HTML.

There is no comment row.

There is no interaction table.

There is no lookup beyond the signing key and application configuration.

The permalink is the database.

### Generated comments

A signed comment URL might look like:

```
https://ethanplant.ca/interactions/eyJ2ZXJzaW9uIjoxLCJraW5kIjoiY29tbWVudCIs...
```

Requesting it returns:

```html
<!doctype html>
<html lang="en-CA">
  <head>
    <meta charset="utf-8">
    <title>Comment by Alice</title>
    <meta name="robots" content="noindex">
  </head>
  <body>
    <article class="h-entry">
      <a
        class="u-in-reply-to"
        href="https://ethanplant.ca/writing/webmention-cursed-comments/"
      >
        In reply to this post
      </a>

      <div class="e-content">
        <p>This is deranged. Ship it.</p>
      </div>

      <footer>
        <span class="p-author h-card">
          <a class="u-url p-name" href="https://alice.example/">
            Alice
          </a>
        </span>

        <time
          class="dt-published"
          datetime="2026-07-09T22:14:00-07:00"
        >
          9 July 2026
        </time>
      </footer>
    </article>
  </body>
</html>
```

The application then sends a Webmention using that URL as the source.

The receiever fetches it, verifies the backlink, parses the interaction, and stores or exports the received mention through its ordinary moderation pipeline.

The source URL is authoritative for publication. The Webmention receiever is authoritative for acceptance and presentation.

The originating application is authoritative for nothing except the signing key.

This is event sourcing for people who looked at an append-only log and decided it contained too much conventional storage.

### Generated likes

Likes require even less information:

```json
{
  "version": 1,
  "kind": "like",
  "target": "https://ethanplant.ca/writing/webmention-cursed-comments/",
  "actor": {
    "id": "alice",
    "name": "Alice"
  },
  "published_at": "2026-07-09T22:18:00-07:00",
  "nonce": "01JLIKE123456789"
}
```

The corresponding source page contains little more than:

```html
<!doctype html>
<html lang="en-CA">
  <body>
    <article class="h-entry">
      <span class="p-author">Alice</span>

      <a
        class="u-like-of"
        href="https://ethanplant.ca/writing/webmention-cursed-comments/"
      >
        Liked this post
      </a>
    </article>
  </body>
</html>
```

The entire like exists because a cryptographically valid URL continues to exist.

No mutable record says that Alice likes the article.

No table contains a boolean.

There is only a document which, when summoned, presents signed evidence that Alice once clicked a heart.

The server sends that evidence to itself, verifies it, and increments the count.

### The source URL as a capability

A signed interaction URL is not merely an identifier.

It is a bearer capability.

Anyone who possesses the URL can retrieve the interaction. They may also be able to resubmit it as a Webmention, share it, archive it, or cause it to appear in logs and referrer headers.

The payload therefore must not contain secrets.

Do not encode:

* email addresses;
* IP addresses;
* authentication tokens;
* moderation notes;
* private account identifiers;
* anything the site would be uncomfortable placing in a public HTML document.

Signing prevents modification. It does provide any meaningful confidentiality.

A signed payload says: The application created or accepted this interaction.

It does not say: Only the intended recipient can read it.

The architecture should use authenticated encryption if confidentiality is genuinely required.

It should also probably not use this architecture if confidentiality is genuinely required.

### Payload size

Comments can be long. URLs should not be.

Browsers, proxies, CDNs, access logs, analytics systems, web servers, and various pieces of infrastructure all have opinions about acceptable URL length. These opinions are not coordinated.

Encoding a complete comment into the path also expands it. JSON becomes bytes. Bytes become base64 or another URL-safe representation. Signatures add more bytes. Unicode content becomes less charming.

A short comment may produce an ugly but workable URL.

A long comment may produce a URL that looks like a certificate chain suffered a nervous breakdown.

The stateless design therefore needs one or more limits:

```
maximum comment bytes
maximum author-name bytes
maximum author-URL bytes
maximum encoded payload length
maximum final request-target length
```

The application should reject a comment before issuing a source URL that some layer of infrastructure will truncate, reject, or log only partially.

A system where the URL is the database must take care not to exceed the database’s maximum row size.

Unfortunately for everyone involved, the database is nginx.

### Deterministic versus unique URLs

The application could generate the source URL deterministically from the interaction content.

The same actor liking the same target would then produce the same URL.

That simplifies deduplication:

```
source = sign(actor, target, kind)
```

It also means that changing any field creates a different source.

Alternatively, every interaction can include a random nonce:

```
source = sign(actor, target, kind, nonce)
```

This permits multiple interactions with otherwise identical content but makes duplicate prevention an application concern.

Without a database, duplicate prevention becomes interesting.

The application can derive deterministic identifiers, rely on a receiever to deduplicate source-target pairs, issue actor-scoped tokens, or simply permit duplicate likes and allow one enthusiastic browser to establish an entire housing development of identical source pages.

The recommended design uses a deterministic interaction identifier derived from:

```
site
actor
target
interaction kind
```

A separate nonce may still prevent payload guessing without changing the logical identity.

This is already becoming a key-management and canonicalization system so that a heart cannot be clicked twice.

The architecture remains on course.

### Canonicalization

Deterministic interaction identifiers require deterministic inputs.

These URLs are not necessarily equivalent as strings:

```
https://ethanplant.ca/writing/webmention-cursed-comments
https://ethanplant.ca/writing/webmention-cursed-comments/
https://ethanplant.ca:443/writing/webmention-cursed-comments/
https://ETHANPLANT.ca/writing/webmention-cursed-comments/
```

The application must canonicalize the target before signing it.

It should define:

* scheme handling;
* hostname casing;
* default ports;
* trailing-slash behaviour;
* fragment removal;
* percent encoding;
* redirect resolution;
* canonical aliases.

The signed payload should contain the exact canonical target used by the Webmention receiver.

Otherwise the source may be valid, the target may be local, and the backlink may still fail because two components disagree about whether the last slash is part of the constitutional order.

### Editing without mutation

A signed URL cannot be edited.

Changing the payload changes the signature and therefore changes the URL.

That means editing a comment creates a new source:

```
comment-created
    ↓
comment-revised
```

The revised payload should identify the source it supersedes:

```json
{
  "version": 1,
  "kind": "comment-revision",
  "target": "https://ethanplant.ca/writing/webmention-cursed-comments/",
  "supersedes": "https://ethanplant.ca/interactions/original-payload.signature",
  "content": "This is deranged. Unfortunately, ship it.",
  "published_at": "2026-07-09T22:30:00-07:00"
}
```

The generated HTML can expose that relationship:

```html
<article class="h-entry">
  <a
    class="u-in-reply-to"
    href="https://ethanplant.ca/writing/webmention-cursed-comments/"
  >
    In reply to this post
  </a>

  <a
    rel="prev"
    href="https://ethanplant.ca/interactions/original-payload.signature"
  >
    Previous revision
  </a>

  <div class="e-content">
    <p>This is deranged. Unfortunately, ship it.</p>
  </div>
</article>
```

The server receives the revised source, follows the supersession relationship, and projects the new version as the current comment.

No update occurred.

A new immutable document entered the record and caused the current interpretation to change.

The website does not have mutable state.

### Unlike without deletion

Likes expose the central problem with eliminating storage.

If you want to remove a database-backed like, you just have to update or delete the record.

A signed URL cannot be revoked merely by wishing it away. The old source remains cryptographically valid and continues to generate a page claiming that the like exists.

There are several possible approaches.

#### Maintain a revocation list

The application stores the hashes of revoked interaction URLs.

This works.

It is also effectively a database.

It may be a small database, a flat file, a key-value store, or a tasteful directory of tombstones, but the architecture has begun rebuilding the thing it removed.

#### Rotate the signing key

Changing the key invalidates every existing interaction.

This is operationally simple and socially deranged.

Alice unlikes one article. Every comment and reaction ever issued by the system disappears.

This gives the like button approximately the same blast radius as rotating a certificate authority.

#### Expire the source

The signed payload includes an expiry time.

After expiry, the source returns `410 Gone` unless renewed.

This avoids permanent revocation state but turns every comment and like into a lease.

The site must periodically renew its emotional declarations or watch them disappear.

Nothing says durable personal web like a heart with a TTL.

#### Materialize a tombstone file

The application writes a file keyed by the source hash:

```
revoked/
└── sha256-8d5f...e03
```

The source handler checks whether the file exists before generating the page.

This is technically storage.

It is, however, filesystem-shaped storage, which makes it feel more morally acceptable.

#### Publish a withdrawal

The purest design creates a new signed source representing withdrawal:

```json
{
  "version": 1,
  "kind": "withdrawal",
  "withdraws": "https://ethanplant.ca/interactions/original-like.signature",
  "target": "https://ethanplant.ca/writing/webmention-cursed-comments/",
  "actor": {
    "id": "alice"
  },
  "published_at": "2026-07-09T22:41:00-07:00"
}
```

The server sends another Webmention.

The receiever receives the withdrawal and marks the earlier like as no longer active.

The original source remains valid. It still says that Alice liked the article at the recorded time.

The later source says that Alice subsequently withdrew the like.

The projected state changes, but history does not.

### An append-only social system

With revisions and withdrawals, the architecture becomes an append-only interaction log:

```
comment-created
comment-revised
comment-withdrawn

like-created
like-withdrawn

bookmark-created
bookmark-withdrawn
```

Every state transition is a new source URL.

Every source URL is an immutable signed document.

Every document sends a Webmention.

The receiever consumes the documents and projects the current state.

This is event sourcing, except the events are websites.

The state of the comments section is not stored in the originating application. It is derived from the the receiever received over time.

The source service knows how to prove that an event document is valid.

The receiever knows how to interpret the sequence.

The static site knows how to render the latest projection.

Nobody has a conventional database, provided nobody looks too closely at the inbox.

### Static materialization

The source pages don't have to remain dynamically generated.

After creating the signed payload, the submission handler can render it into a static file:

```
interactions/
├── 01JCOMMENT.html
├── 01JCOMMENT-REVISION.html
├── 01JLIKE.html
├── 01JUNLIKE.html
└── 01JREGRET.html
```

The form handler writes the file, sends the Webmention, and exits.

The web server serves the directory like any other static content.

The receiever receives the Webmention, verifies the file, places the interaction into moderation, and exports approved responses for the next site build.

The complete system becomes:

```
HTML form
    ↓
signed interaction document
    ↓
static HTML file
    ↓
Webmention
    ↓
Moderation
    ↓
static response export
    ↓
site rebuild
```

No public database service.

No client-side comment embed.

No generic engagement API.

No platform account.

No persistent application process beyond whatever accepts the form.

Just files, signatures, Webmentions, and rebuilds.

A social network whose storage engine is "documents exist."

### Key rotation

A stateless source service depends on its signing key.

Losing the key means losing the ability to issue new interactions that belong to the same trust domain.

Changing the key may invalidate every existing source unless the application retains old verification keys.

The source handler therefore needs a keyring:

```
interaction-signing-key-2026-01
interaction-signing-key-2027-01
interaction-signing-key-2028-01
```

Each payload includes a key identifier:

```json
{
  "key_id": "interaction-signing-key-2026-01",
  "version": 1,
  "kind": "like"
}
```

Old keys remain available for verification but not signing.

Compromised keys require revocation policy.

At this point the no-database comment system has acquired:

* cryptographic key rotation;
* versioned payload schemas;
* canonical URL rules;
* immutable event semantics;
* supersession chains;
* withdrawal documents;
* projection logic;
* replay protection;
* a moderation consumer.

All to avoid storing:

```
liked = true
```

Architectural purity has costs.

### Replay and duplicate delivery

Anyone who knows a valid source URL can submit its Webmention again.

That does not create a new interaction if the receiever treats the source-target pair idempotently.

The receiver should enforce uniqueness on:

```
(source_url, target_url)
```

A repeated delivery triggers reverification, not duplication.

For interactions with logical identity beyond the source URL, the receiever may also derive an interaction key:

```
(actor, target, kind)
```

This permits a revised source to update the same projected interaction while preserving the immutable source history.

The receiver must not trust a supersession claim merely because one payload names another. It should verify that both interactions belong to the same actor and target under the configured provenance rules.

Otherwise Alice could publish a document claiming to supersede Bob’s comment.

The URL may be the database, but authorization remains annoyingly real.

### Moderation

The immutable source exists before moderation.

The receiever may reject it, hide it, or classify it as spam, but the source URL remains retrievable unless the originating service has a separate revocation mechanism.

This is similar to external Webmentions. A receiving site can refuse to display someone else’s page, but it cannot erase the page from the Internet.

For locally submitted interactions, this changes the moderation model.

The site is hosting the source while also pretending to be merely receiving it.

A rejected comment may therefore remain publicly accessible at its signed URL even though it never appears beneath the article.

Possible responses include:

* generate source pages only after approval;
* require a pending token that is exchanged for a permanent signed URL;
* encrypt pending payloads;
* place pending sources behind authentication;
* accept that rejected sources remain obscure but public;
* add a revocation list and quietly admit storage has returned.

The cleanest design signs and activates the permanent source only after moderation.

The purest design publishes first and treats moderation as a projection concern.

The purest design is worse.

### Security invariants

The stateless design must preserve these rules:

```
payload signature is valid
payload schema version is supported
payload target is a canonical permitted local URL
payload kind is recognized
payload contains no confidential information
encoded URL remains within configured length limits
actor is authorized for the claimed interaction
supersession preserves actor and target identity
withdrawal preserves actor and target identity
repeated delivery is idempotent
old verification keys remain controlled
```

The source service must never sign a payload containing an arbitrary external target merely because the client submitted one.

A signed arbitrary backlink is more dangerous than an unsigned arbitrary backlink because it carries the site’s explicit cryptographic endorsement.

The site would not merely be hosting forged interaction pages.

It would be notarizing them.

### Final assessment

Removing the database does not simplify the system in any meaningful way.

It changes where the state lives.

The interaction becomes an immutable signed URL. Edits become new documents. Deletions become withdrawals. Current state becomes a projection over an append-only stream of tiny webpages. The receiever becomes the institution responsible for reading the archive and deciding what is presently in force.

The website no longer stores social activity.

It issues instruments.

A comment is a document. A like is a document. An unlike is a subsequent document.

The filesystem is sufficient.


## The spam cannon hiding inside the joke

The dangerous form of this architecture exposes an endpoint like:

```
GET /comments/source?target={arbitrary_url}
```

The server returns:

```html
<a href="{arbitrary_url}">Target</a>
```

Anyone can now create a valid source page on your domain linking to any target on the internet.

Pair it with this:

```http
POST /send-webmention

source=https://ethanplant.ca/comments/source?target=https://victim.example/post
target=https://victim.example/post
```

and the system becomes an open Webmention relay.

An attacker can:

1. select a target;
2. generate a source page linking to it;
3. ask the service to send the Webmention;
4. pass ordinary backlink verification;
5. repeat until every receiver on the small web hates your domain.
6. you get a polite from firm email from your registrar that they no longer wish to do business with you.
    

The generated source could contain spam, abuse, fraudulent attribution, or nothing except the backlink. Even an empty mention consumes receiver resources and fills moderation queues.

Webmention proves a link exists at verification time.

It does not prove that the source arose naturally from someone publishing a page.

Once a service can generate arbitrary source pages and send arbitrary Webmentions, it can manufacture the exact condition the protocol verifies.

The safe implementation must enforce:

```
source host == configured site host
source path == registered comment-source path
source id exists in the database
source target == stored comment target
target host == configured site host
comment status == approved
delivery request originated internally
```

The public must never be allowed to choose both ends of the relationship.

## Target binding

The target must be bound to the comment when the comment is created.

A source URL should never derive its target from mutable query parameters.

Bad:

```
/comments/source/123?target=https://ethanplant.ca/notes/en-ca/
```

Worse:

```
/comments/source?comment=123&target=https://anywhere.example/
```

Acceptable:

```
/comments/source/01JABCDEF123456789
```

The server loads the comment and discovers the target from persistent state.

Changing a comment’s target should require an explicit migration:

1. deactivate the old source relationship;
2. notify the old target that the backlink is gone;
3. update the stored target;
4. regenerate the source;
5. notify the new target.
    
In practice, comments should rarely move between articles. A moderator correcting a target association is different from a user being permitted to repoint an existing source URL at will.

Stable URLs are infrastructure. Even when those URLs exist primarily to deceive a protocol in a technically accurate manner.

## SSRF

A Webmention receiver fetches user-supplied URLs. That is server-side request forgery wearing an IndieWeb pin.

The external fetcher must reject:

- loopback addresses;
- private network ranges;
- link-local addresses;
- cloud metadata endpoints;
- non-HTTP schemes;
- redirects into prohibited networks;
- excessive redirect chains;
- oversized responses;
- slow responses;
- decompression bombs.

The cursed comments design complicates this because the receiver is expected to fetch a URL on its own host.

There are two reasonable solutions.

### Internal verification

Recognized local sources bypass the network fetcher:

```rust
match classify_source(&source) {
    Source::LocalComment(id) => {
        verify_local_comment(id, &target)
    }
    Source::External(url) => {
        fetch_and_verify(url, &target)
    }
}
```

This is the safer design.

It also admits that the HTTP round trip was unnecessary.

### Restricted local fetch

The receiver permits only the configured public origin and a specific source-path prefix:

```
https://example.com/comments/source/*
```

All other local and private addresses remain prohibited.

This preserves the ritual of having the server fetch its own fake page.

The internal verifier is better engineering. The restricted self-fetch has stronger artistic integrity.

## Transactional delivery

The comment form should not send the Webmention directly during the request.

The database transaction should create:

1. the comment;
2. the source binding;
3. an outbound Webmention event.

```
OutboundWebmention
------------------
id
source_url
target_url
status
attempt_count
next_attempt_at
created_at
last_error
```

A worker delivers pending events:

```
pending
   ↓
sending
   ↓
delivered
```

Failures transition to:

```
retry
permanently_failed
```

This prevents a successful Webmention from escaping after the comment transaction rolls back.

It also supports retries, metrics, and operational recovery.

Yes, the system now needs a durable outbox so it can reliably inform itself about the comment it just stored itself.

Boring infrastructure carries more weight than glamorous interfaces. Sometimes it carries a tiny fake website across the room so another part of the same process can formally acknowledge receipt.

## Rate limits and abuse controls

The comment form still needs normal comment-system protections:

- per-IP rate limits;
- per-target rate limits;
- body-size limits;
- link-count limits;
- duplicate-content detection;
- honeypot fields;
- minimum completion times;
- spam scoring;
- manual approval for first-time commenters;
- domain and phrase blocklists;
- optional account or IndieAuth requirements.
    
The source endpoint also needs protection against enumeration and scraping.

Opaque identifiers help, but they are not access control. Approved source pages are public by design. Pending and rejected comments should not produce accessible pages.

The sender must only accept jobs created internally from persisted comment records.

There should be no public endpoint where a client can supply:

```
source
target
```

and receive a cheerful `202 Accepted`.

That interface would be convenient.

Convenience often hides a transfer of authority. In this case, the authority being transferred is the ability to make your domain claim it linked to arbitrary strangers.

## Observability

The system should record:

- comment submissions;
- moderation decisions;
- source activations;
- outbound Webmention attempts;
- receiver verification results;
- retries;
- edits;
- deletions;
- source-target mismatches;
- rejected external fetches;
- rate-limit decisions.

Useful metrics include:

```
comments_submitted_total
comments_approved_total
comments_rejected_total
webmentions_sent_total
webmentions_delivery_failed_total
webmentions_received_total
local_comment_verification_failed_total
source_target_mismatch_total
```

The most important alarm is probably:

```
local_comment_verification_failed_total > 0
```

A locally generated source failing to link to its locally stored target means the service has lost an argument with itself.

That is rarely a sign of a calm afternoon.

## Failure modes

### The source endpoint is unavailable

The receiver cannot verify the Webmention.

The outbound event remains pending and retries later.

### The source exists but lacks the backlink

The receiver rejects the mention.

For an existing comment, this may represent deletion or corruption.

### The source and target records disagree

The receiver rejects the mention and raises an operational error.

This should never be treated as ordinary user input failure. Both values came from the application.

### The comment is approved but delivery fails

The comment remains approved but undisplayed until delivery succeeds, unless the renderer is designed to read local comments directly.

Reading directly from the comments table would be operationally simpler.

It would also reveal that the entire Webmention pipeline is optional.

We do not discuss this at architecture review.

### Delivery succeeds but the database transaction fails

The transactional outbox prevents this.

Without it, the receiver may observe a source whose comment record never committed.

### The target article moves

The site should retain redirects from old canonical URLs and maintain a target alias map.

Existing comment sources may continue linking to the old URL while the receiver associates it with the current article.

Canonical URLs should not change casually. A URL is not decorative metadata. It is the address the rest of the web was told to remember.

### A search engine indexes the source pages

The pages should contain `noindex` and should not appear in sitemaps.

They remain public URLs. Search engines are not parties to your intentions.

## Security invariants

The implementation should preserve these rules:

1. Every manufactured source belongs to one comment.
2. Every manufactured source has one stored target.
3. The client cannot choose an arbitrary external target.
4. The client cannot trigger delivery from an arbitrary source.
5. A source identifier cannot be reassigned.
6. Rejected comments never activate source pages.
7. Deleting a comment removes its backlink.
8. Repeated Webmentions are idempotent.
9. External fetching remains protected against SSRF.
10. Local comments remain visibly distinct from external replies.
11. Editing does not change the source URL.
12. The sender only processes internally created delivery records.

Remove any one of these and the design becomes less funny.

Remove several and it becomes a service-abuse report.

## Why the abuse works

Webmention verifies a relationship between resources.

It can establish:

```
Source S currently links to target T.
```

It cannot establish:

```
A person independently published source S on a site they control,
intended it to be a meaningful public document,
and then chose to notify target T.
```

The wider web normally supplies that context.

A source URL tends to represent a real post. The domain tends to belong to the publisher. The page tends to contain the reply being submitted. These are conventions around the protocol, not facts proven by it.

This architecture manufactures the convention’s outward shape.

There is a URL. The URL returns HTML. The HTML links to the target. The sender submits the correct pair. The receiver confirms the link. The machinery works because the machinery was never designed to determine whether the source is spiritually independent.

Protocols are often like this. They prove one narrow technical claim, while people quietly build larger social assumptions around it.

Usually that is fine.

Then someone writes `/comments/source/{id}`.

## Alternatives

### Store comments normally

The application could store comments in a database and render them beneath the article.

This is simple, legible, boring, and what everyone has done for decades.

It is therefore the strongest alternative.

### Publish each comment as a real page

Each comment could become a genuine local page with its content, author information, timestamp, and permalink.

That page could send a legitimate Webmention to the article.

At that point the source is no longer fake. The site has simply adopted the model that replies are first-class web resources.

This is probably the best IndieWeb-shaped implementation.

It also ruins the central joke.

### Accept only external Webmentions

Readers must reply from their own sites or use a third-party service.

This preserves the decentralized model but excludes everyone who does not maintain a website.

A personal website should be allowed to have a front porch. It should not require every visitor to construct a neighbouring house before speaking.

### Use a hosted reply intermediary

A third-party service could host a reply page and send the Webmention on the commenter’s behalf.

This is the same architecture with the fake source placed on someone else’s domain and surrounded by a pricing page.

## Recommended design

The least dangerous implementation is:

- store comments normally;
- assign every approved comment an immutable source URL;
- bind the source to a canonical local target;
- generate either a tiny backlink page or a self-describing microformatted page;
- moderate before activating the source;
- deliver through a transactional outbox;
- recognize local sources explicitly in the receiver;
- preserve normal SSRF protections for external sources;
- resend the Webmention after edits;
- remove the backlink after deletion;
- render local comments as local comments;
- never expose arbitrary source or target generation.

The implementation should document all of this prominently.

A future maintainer should not discover during an outage that the comments section works by dynamically constructing tiny websites and making the application send diplomatic notes between them.

## Conclusion

This architecture is ridiculous. It is also unfortunately coherent and abides by [the spec](https://www.w3.org/TR/webmention/#webmention-verification).

Webmention asks whether one URL links to another. The system creates a URL, puts the link there, and submits the result. The receiver performs its duty and accepts the mention.

The protocol hasn't been bypassed. It has simply been obeyed with such precision that the assumption behind it collapses.

A comment entered directly beneath an article becomes a supposedly external web resource. The server generates the evidence, presents the evidence to itself, verifies the evidence, and records that an independent mention has occurred.

The source page is a route on the same application wearing a fake moustache.

So yes. You can build a comments section by abusing Webmention.

The question is not whether it works.

The question is whether, after explaining the architecture to another human being, you can maintain eye contact.
