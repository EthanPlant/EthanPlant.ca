+++
title = "We Rebuilt the Mainframe"
description = "How the personal computer gave us control, and the cloud quietly took it back"
date = "2026-05-18"

[taxonomies]
tags = [
    "software-ownership",
    "personal-computing",
    "cloud-computing",
    "digital-ownership"
]
+++

The personal computer had a simple idea around it.

The computer was yours.

It sat on your desk. It ran programs from your disk. It saved files where you put them. You could install a weird utility from a shareware CD your uncle gave you. You could keep a folder of documents for twenty years and open them without logging into anything. If the modem was unplugged, the machine still worked.

That sounds ordinary. Increasingly it sounds radical.

Before personal computers, a lot of computing worked through terminals. A terminal had a keyboard and a screen, so it looked like the computer to the person using it. But the real computer was somewhere else. A university, a bank, a government office, a corporate machine room.

You typed commands into the terminal. The mainframe did the work. Someone else owned the machine. Someone else set the rules. Someone else decided what you were allowed to run.

The personal computer broke that arrangement.

An Apple II, a Commodore 64, an IBM PC, a Macintosh. These machines were limited by modern standards, but they moved authority into the room. You could boot them yourself. You could copy a program. You could open the case and replace parts. You could store your work on a floppy, then a hard drive, then a pile of slightly suspicious burned CDs.

It was messy. Drivers failed. Computers blue screened. Files got corrupted. People lost work because they had one copy on one disk and treated backups as a moral theory.

Still, your work was close to you.

A Word document lived in a folder. A Photoshop file lived on a drive. A folder of MP3s was just a folder of MP3s. Your computer didn't need to check with a billing server in Ohio before it let you write a letter.

I think about this whenever my internet goes out.

A while ago, during a storm, I opened an old ThinkPad to find a document I hadn't touched in years. The machine was slow. The battery was hanging on for dear life. The screen looked terrible by modern standards.

The file opened. Instantly.

No account. No sync error. No expired session. No browser tab trying to recover itself. Just a file on a disk, opened by a program that already knew what to do with it.

Then I went back to a newer machine and watched a Google Doc hang because the network was unstable.

None of this really felt like a loss at the time.

Gmail was easier than running mail locally. Google Docs was easier than emailing `essay-final-really-final (2).doc` back and forth. Dropbox solved a real problem. Spotify was easier than managing a music library. Figma made design collaboration less painful. Slack was easier to sell to a workplace than IRC. Notion made a pile of half-finished notes look like a productivity system.

Most of these tools were better than what they replaced.

That was the problem. The trade was easy to miss.

The computer itself kept getting stronger. A normal laptop can edit video, run containers, compile code, host a database, and keep thirty browser tabs alive while pretending everything is fine.

Yet somehow writing a paragraph, opening a note, loading a design, or finding an old photo can still depend on whether a remote service feels like answering.

Open Google Docs and the document lives in Google’s system. Open Spotify and the music is a catalogue you rent access to. Open iCloud Photos and your phone may only keep a smaller local version of your own pictures. Open Slack and your workplace memory is searchable until the retention policy or pricing plan says otherwise.

The browser became the terminal window.

The account became the computer.

This matters most when something goes wrong.

A local file is crude, but it has a stubborn honesty to it. You can copy it to a USB drive. You can zip it. You can email it. You can put it on an old hard drive and find it years later. You can open a plain text file from 1998 on a computer today without asking anyone.

A SaaS document is a different thing. It looks like a document, but often it's just a record inside a company’s database. The export may be incomplete. The app may change. The free plan may shrink. The company may get bought. The feature you used may disappear because someone decided it confused new users or did not help enterprise sales.

The smaller annoyances are almost worse because they feel normal now.

You open an app to write something down and it asks you to sign in again. You open a document and the page loads, then the editor loads, then the comments load, then some sidebar you didn't ask for appears. Then some AI assistant tries talking to you. You try to export your own work and get a zip file full of HTML, JSON, missing attachments, and folders with names only the app understands.

Nothing has broken exactly.

It just no longer feels like yours.

Rarely all at once. Usually through small decisions you cannot appeal.

The button moves. The plan changes. The API gets restricted. The old interface goes away. The app gets “simplified” and loses the thing that made it useful. The product starts as a tool for individuals, then slowly turns toward teams, managers, permissions, dashboards, and admin controls.

You still click the icon.

You have less say over what happens after that.

As a developer, I feel this most clearly in developer tools.

A local repo is understandable. Maybe not simple, but at least visible. The files are there. The history is there. You can run `git log`. You can `grep` the code. You can break things and fix them without asking a dashboard for permission.

Then the repo becomes one piece of a larger machine. GitHub Actions, cloud credentials, deployment environments, secrets, IAM policies, preview builds, status checks, billing limits, hosted logs, hosted metrics, hosted everything.

The project still looks like code on the surface.

Some days it feels more like paperwork with a compiler attached.

The work has become distant again.

This doesn't mean cloud computing was a mistake. It solved real problems. Most people do not want to run their own mail server. Most small teams shouldn't own physical servers. Backups matter. Sync matters. Collaboration matters. The cloud made all of this easier.

The problem is that convenience kept being traded for ownership, and that trade was rarely described honestly.

We kept buying faster machines. Better screens. More memory. More cores. More storage.

Then more of the actual work moved onto servers we did not own.

This is why local-first software feels interesting again. Same with plain text, RSS, personal websites, self-hosting, small web tools, and boring file formats. These things aren't just nostalgia for beige boxes and old icons. They're signs that people are tired of renting the basic shape of their digital lives.

A good tool should leave you with something.

A file. A folder. An export that actually works. A copy you can keep. A system that doesn't fall apart because a company changed its pricing page.

The mainframe came back with better fonts.

It came back as a web app, a subscription, a login screen, a sync status, a workspace, a cloud drive, an admin console.

The interface says personal computer.

The architecture says dumb terminal.
