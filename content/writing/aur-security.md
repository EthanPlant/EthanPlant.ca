+++
title = "The AUR Is Not an App Store"
description = "Malware, package adoption, and why \"read the PKGBUILD\" is no longer enough"
date = 2026-08-03

[taxonomies]
tags = ["security", "linux", "operating-systems"]
+++

The Arch User Repository has a malware problem.

On July 30, Arch Linux disabled package adoption in the AUR. The reason was fairly direct, there had been an influx of malicious package adoptions followed by malicious commits. People were taking over orhpaned packages and using the existing identity to push malware.

Two days later, on August 1, [it went further](https://lists.archlinux.org/archives/list/aur-general@lists.archlinux.org/message/YPJ3FQYJTJXXY3RUXCYLMHUKHLIUNVFF/).

> Hi everyone,

> We have now disabled pushes altogether as well for the moment, while we 
> handle the situation. Sorry for the inconvenience.

This would be concerning enough if it were a new problem. It isn't.

In May, people started finding orphaned AUR packages adopted by newly created accounts and modified to run things like `npm install crypto-javascript` from new install scripts. Arch suspended the accounts and reverted the changes.

Then June happened. On June 12, Arch published a warning saying it was experiencing a high volume of malicious package adoptions and updates. The project was trying to track down existing malicious commits while preventing additional ones from being pushed. The advice to users was familiar: Review all `PKGBUILD` and install-script changes before updating. Three days later, Arch disabled new AUR registrations while it dealt with another influx of malicious packages. Registration eventually reopened on July 13 with disposable email addresses blocked and email verification made mandatory.

Then, less than three weeks later, attackers were abusing package adoption again. Arch disabled adoptions, and then pushes. 

There is a traditional answer to all of this. "Read the `PKGBUILD`". And, irritatingly, this is correct, but it is no longer enough.

## The AUR is a weird thing

The Arch User Repository has a slightly unfortunate name. It sounds like a package repository. You have the official Arch repositories, and then over there is the user repository. The interface reinforces this idea. Search for a package, install it, update it, remove it. All normal package-manager things.

Underneath, the AUR does something substantially stranger. The AUR primarily contains package descriptions: `PKGBUILD`s and the associated files necessary to turn upstream software into an Arch package. These files are user-produced, completely unofficial, and not thoroughly vetted. [Arch's documentation](https://wiki.archlinux.org/title/Arch_User_Repository) explicitly tells users to acquire the build files, verify that the `PKGBUILD` and accompanying files are not malicious, run `makepkg`, and only then install the resulting package with `pacman`.

A `PKGBUILD` is, at the end of the day, a Bash script. This is the part worth keeping in your head. It's not a static manifest saying:
```
name = firefox 
version = 141
source = Mozilla
```

It's a shell script and can do ordinary shell script things. It can download things. It can run things. It can invoke a compiler. It can invoke another package manager. It can patch upstream source code. It can execute arbitrary upstream build systems written by people with interesting ideas about what `make install` should be allowed to do.

This is an unusually powerful mechanism. It is also, from a security perspective, a folder of random shell scripts supplied by strangers on the internet. Both descriptions are accurate.

## "Read the PKGBUILD"

The traditional Arch security model for the AUR is wonderfully direct. Look at the code you're about to run. If a package that used to download its source tarball from GitHub suddenly has an install script containing: `npm install crypto-javascript`, perhaps you shouldn't execute it. That is not a hypothetical example. A variation of exactly this appeared in the May campaign. Orphaned packages were adopted and modified to install the malicious npm dependency through newly added install scripts.

If a package acquires an install script for no obvious reason, inspect the install script. If a new dependency appears, find out why. If the build process suddenly does something completely unrelated to building the software, stop immediately.

This is not on its face bad advice. For a lot of malicious AUR packages, it is very good advice. The problem is that “read the `PKGBUILD`” compresses an extraordinary amount of assumed knowledge into three words. You need to know Bash well enough to understand what the script is doing. You need some idea of what a normal `PKGBUILD` is supposed to look like. You need to understand source arrays, checksums, dependencies, build functions and install scripts. You need enough familiarity with Linux build systems to recognize when something unusual is suspicious rather than merely another day in CMake.

You also need to inspect the other files too. A clean `PKGBUILD` can call an underlooked `.install` script that executes the payload. You need to understand that a clean-looking build script does not magically make the upstream source trustworthy. This is an inherently technical review. Historically, Arch could get away with treating that as part of the deal.

## Arch used to select for this

Arch Linux has traditionally imposed friction. This was never entirely deliberate gatekeeping, even if the "I use arch btw" crowd wishes it was. Arch simply expected the person administering the system to administer the system. Installing Arch meant partitioning disks, mounting filesystems, installing a base system, configuring networking, setting up a bootloader, choosing the rest of the operating environment, and consulting the Arch Wiki repeatedly until the computer became useful. 

You learned what `pacman` was because you needed it. You roughly learned how packages work. You learned that the Wiki wasn't optional decoration. You probably became comfortable enough with a terminal that opening a shell script and reading it was not an exotic security procedure.

None of this made Arch users immune to malware. Technical people are extraordinarily capable of pressing enter without reading things. I'm still annoyed the default guidance for installing `rustup` is `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`.

But the friction did some filtering. By the time someone encountered the AUR, there was a decent chance they understood what was actually being offered: a community-maintained recipe for building software on their own machine. Installing an AUR helper probablyu meant you had to go through the ceremony of installing something from the AUR by hand at least once. "Read the `PKGBUILD`" made sense in that culture, the culture simply got bigger.

## The audience changed

You no longer need to install Arch to end up effectively living in the Arch ecosystem. EndeavourOS is a good example. It gives you a graphical installer, sensible defaults, and a system that remains deliberately close to Arch. It also makes the AUR remarkably convenient, as it comes with `yay` preinstalled. Its own documentation on `yay` correctly warns that AUR build instructions are user-provided, unsupported by Arch, and installed at your own risk. Then it tells you:

```sh
yay -S package_name
```

It explicitly describes installing packages with `yay` as basically the same as using `pacman`.

CachyOS has pushed the same general idea further. Its current post-install documentation recommends [Shelly](https://github.com/Seafoam-Labs/Shelly-ALPM), a graphical and command-line package manager that can install software from the official repositories, the AUR, Flatpak, AppImage and local packages through one interface.

This is good. There's no moral virtue in spending a Saturday afternoon debugging your bootloader before you are permitted to use pacman. A graphical installer does not make your Linux installation less pure. Most people do not actually need to know what `genfstab` does to use their computer. Making Arch-derived systems accessible to people who simply want a fast rolling-release distribution is not a failure.

But successful interfaces change who uses the infrastructure underneath them. A person can now install an Arch-based distribution without manually constructing the system. They can use graphical tools to manage software. They can encounter the AUR before they have ever written a shell script. They can install an AUR package without really knowing what a `PKGBUILD` is.

Then the security guidance tells them to audit the Bash, and that's where the mismatch is. The AUR was built in a culture where technical literacy supplied part of the safety model, while the broader ecosystem has spent years successfully reducing the amount of technical literacy required to enter it. Those two developments eventually had to meet.

## `yay -S` is almost too good

Compare these two commands:
```sh
sudo pacman -S foo
```

and:
```sh
yay -S foo
```

They look like variations of the same operation. Conceptually, they are not. The first asks a package manager to install a package from a configured binary repository. The second may ultimately retrieve community-supplied build instructions, download source code according to those instructions, execute the build locally, create a package and install it. The trust models behind them are completely different.

This is not really a criticism of `yay`. `yay` is good software, even if I personally use `paru`. That's almost the problem. AUR helpers have become good enough that they can smooth over the exact friction that once reminded users what the AUR was. Arch itself tries to warn users that AUR helpers are unsupported and says users should understand the manual build process first. It has a nice comparision of helpers that explicitly tracks whether they let users review files and diffs. This is all good documentation.

Interfaces teach behaviour more effectively than documentation. If two operations look the same, use the same package names, appear in the same search interface and get updated by the same command, people will naturally begin assigning them similar trust. After a while, the mental model becomes obvious: The AUR is where Arch packages live when they are not in the normal repositories. That mental model is close enough, except when one of those packages has just been adopted by an account you have never heard of and now contains a different build script.

You cannot make executing a stranger's shell script feel almost indistinguishable from installing an official package and then act surprised when people develop the same trust relationship with both operations. The computer is teaching them the wrong lessons.

## Adoption is the ugly part

This is where I stop being particularly sympathetic to the argument that the AUR's problems are entirely the user's responsibility. Package adoption is a real trust boundary. Imagine for instance, a package has been in the AUR for eight years. It has a recognizable name, hundreds of votes, and years of comments. Thousands of people may have installed it. The maintainer has been updating it consistently for years. Some users have reviewed its `PKGBUILD`, others have simply accumulated confidence because the thing has behaved normally for a long time.

Then the maintainer disappears. The package gets marked as orphaned. A new account adopts it. From the AUR's perspective this is success, the project has found a new maintainer. From the user's perspective, the line in their update list still has the same package name. From a security perspective, the principal controlling the build instructions has changed completely.

The recent attacks have repeatedly exploited exactly this boundary. In May, burner accounts adopted orphaned packages and immediately pushed malicious changes. In June, Arch's own incident notice specifically described a high volume of malicious package adoptions and updates. Then on July 30, adoption abuse became severe enough that Arch disabled the feature altogether.

The mailing-list thread from that incident contains an almost perfect little snapshot of the problem. The next morning, someone noticed a new account using a disposable email address had adopted a single package without updating it yet. They reported it as suspicious. An Arch package maintainer replied three words later in the thread:

> Good catch! Suspended.

The package had not even received the malicious commit yet. The adoption itself was enough to look wrong in context. The next day, Arch disabled all pushes across the AUR. At some point this stops looking like a few users failing an impromptu Bash exam. There is an actual system boundary facing a coordinated attack.

## The answer is not an App Store

The tempting solution is more moderation. Review every new package and update. Require approval before adoption. Scan every upload. Build everything centrally and distribute the binaries. Establish trusted maintainers. Add enough process until nothing reaches a user without someone from Arch blessing it first.

Congratulations. We've invented `extra`. The AUR is useful precisely because Arch does not need to care about every package in it. A utility used by nine people can exist. A nightly build can exist. A package can apply some ridiculous patch required by three laptops with a particular fingerprint reader. A developer can publish software upstream and somebody completely unrelated can package it for Arch without asking either Arch or the developer for permission. This is the genuinely great part about the AUR. Central review would not merely cost volunteer time, it would fundamentally change what the AUR is.

## Aseprite is why the AUR should stay weird

Aseprite is a pixel-art editor with an unusual distribution model. [The source code is available](https://github.com/aseprite/aseprite). Anyone can download it, compile it, modify it for personal use, and use the resulting program to create commercial artwork.

Aseprite is not however, open-source in the traditional sense. It's source-available commercial software. The developers sell convenient official binaries instead. Their FAQ says this very plainly: [You are not allowed to redistribute compiled binaries of Aseprite](https://www.aseprite.org/faq/#can-i-redistribute-aseprite):

> No. From August 2016 you cannot redistribute compiled versions of Aseprite. We have replaced the General Public License (GPLv2) with the new Aseprite EULA. The only way to redistribute Aseprite is with an special educational license.

> Anyway this does not restrict most users: You can still compile the source code, and use the program to create your assets for commercial games. You can also make contributions to Aseprite or modify its source code for your personal purposes.

This creates an annoying problem for ordinary Linux packaging. Linux package managers tend to work like this for binaries, someone compiles the program, puts the compiled package on a server, you download their copy. For Aseprite, this is exactly the bit the EULA complicates.

The AUR has an almost comically elegant solution. Don't distribute Aseprite, just distribute the instructions to compile it. The [`aseprite` package](https://aur.archlinux.org/packages/aseprite) has existed in the AUR since 2011. Its current package recipe points at Aseprite's official source release, carries the packaging and compatibility patches required to build it on Arch, and lets `makepkg` produce an ordinary Arch package locally. The maintainer even has a pinned comment explicitly reminding users that binaries built with the `PKGBUILD` cannot be redistributed.

This is the AUR at its absolute best. No one needs to redistribute the finished application. No one needs to tell every Arch user that wants to make pixel art how to manually reconstruct an annoyingly complicated build procedure. You just download a script that does it for you, and your package manager can then track the resulting installation like everything else.

This is excellent. Without something like the AUR, the alternative is to tell every Arch user who wants Aseprite to follow upstream's build instructions manually, resolve the dependencies, figure out the Skia situation, install everything into appropriate paths, remember what they did six months later, and repeat the adventure when Aseprite releases another version.

There is something deeply useful about the AUR's model. The AUR is really a repository full of institutional memory about how to make weird software behave like an Arch package. Sometimes that means three lines around a tarball. Sometimes it means a constellation of patches around an application with unusual licensing and a complicated dependency chain. Sometimes it means software upstream has never heard of Arch Linux and probably never will. Aseprite works so well in the AUR because the AUR is not an App Store.

## Unfortunately, the recipe is executable

This is also the entire security problem. The same property that makes the Aseprite package possible makes a malicious package dangerous. The AUR sends you instructions and your computer happily follows them. There is no clean architectural trick where we retain arbitrary community-maintained build logic but somehow guarantee that arbitrary community-maintained build logic is trustworthy.

Of course, you can scan it. You can constrain pieces of it. You can detect obvious sketchy patterns. You can make abuse harder. You can improve account security. You can rate-limit suspicious activity. You can flag strange changes. None of this turns an unreviewed shell script into a signed binary built by Arch infrastructure.

The answer has to start by accepting what the AUR is and designing around it.

## Make trust changes visible

The most useful improvements do not require pretending the AUR can become safe in the same way an official repository is safe. They require making its actual trust model visible.

If a maintainer changes, it should be loud. If I have had a package installed for three years and it has just been adopted by a different account, my next update should not look like:
```
foo 1.4.2-1 -> 1.4.3-1
```

It should look more like:
```
WARNING: foo changed maintainers since your last build. 
Previous maintainer: alice 
Current maintainer: definitely_normal_user_483 

Review changes before continuing.
```

Not buried halfway through the output, or through a flag if I know to ask. If the trust relationship changed, tell me. Scream it at me until I acknowledge it. The first update after an adoption deserves additional friction and scrutiny.

The same applies to other meaningful changes. A new source domain, or binary blob, or `.install` file, or dependency. An update that suddenly invokes `npm` in a package that previously had nothing to do with JavaScript. A helper does not need to proclaim any of these malicious, that would create a different and probably much stupider problem. It can simply say: "hey this changed, look at it before continuing". This is already partially a solved problem, AUR helpers like `paru` and `yay` have mechanisms for reviewing diffs between updates.

The next step is cultural. Stop treating review as an optional ceremony for paranoid users and start treating changed trust as part of the package update itself. There are places where friction is good. A password prompt is friction. A code review requirement is friction. The big red handle that stops a train is friction. Nobody redesigns it into a tasteful grey swipe gesture because users reported that emergencies involved too many deliberate actions.

## This is not EndeavourOS's fault

There is an easy way to make this argument very stupid. Arch was better when you had to install it manually. People should learn Linux. If you cannot audit Bash, don't use the AUR. "Skill issue".

Absolutely not, the fact that more people can use a good operating system is not a problem.. EndeavourOS is not wrong for making Arch easier to install. CachyOS is not wrong for building friendly package-management interfaces. `yay` is not wrong for being convenient. The broader Linux ecosystem becoming usable by people who do not consider a Saturday spent repairing GRUB a recreational activity is a good development.

The problem is what happens when old systems become successful outside the assumptions they were built around. The AUR's safety model made more sense when a typical user arrived after manually installing Arch and understood that "user repository" meant "here are some scripts from other Arch users." Now an increasingly plausible path is download an Arch alternative, click through Calamares, open a graphical package manager, search for an application, click "Install". Somewhere under all of that, Bash supplied by a stranger executes. The warning didn't become false, the environment around it just changed. A safety model based partly on user expertise becomes weaker as expertise stops being an implicit prerequisite for participation.

## Arch knows something has to change

The most revealing part of the last two months is not that malicious packages appeared. Malicious packages in a repository of unvetted user submissions are not exactly an unimaginable event, and Arch's ecosystem has grown enough to make the AUR a valuable target. What is interesting is the escalation of Arch's response:

June 12: [public warning about malicious adoptions and updates](https://lists.archlinux.org/archives/list/arch-announce%40lists.archlinux.org/message/FYVZMO3NVKG7FFB25FZQBMDDTZAU7WQF/).

June 15: [New registrations are disabled](https://lists.archlinux.org/archives/list/aur-general%40lists.archlinux.org/thread/4JRS73YVTE7JUYHHE3ZDUIHXYHXZ3YQQ/)

July 13: [Reopen registrations with stricter account controls](https://lists.archlinux.org/archives/list/aur-general@lists.archlinux.org/message/TT3OCFFNM6SBMUBKIVTHTKA6UZJNMXIJ/)

July 30: [Disable package adoption](https://lists.archlinux.org/archives/list/aur-general%40lists.archlinux.org/thread/DRDEU3JUSC72CB265XHXPFA3DFSLXPBP/)

August 1: [Disable pushes altogether](https://lists.archlinux.org/archives/list/aur-general@lists.archlinux.org/message/YPJ3FQYJTJXXY3RUXCYLMHUKHLIUNVFF/)

That is infrastructure responding to active abuse. Arch's maintainers are cleary taking this seriously. The interesting question is what permanent changes can improve the situation without destroying the thing being protected. I hope the answer is boring. Better account protections, better provenance, better abuse detection, more obvious maintainer transitions, and AUR helpers that make meaningful changes harder to ignore. Perhaps additional restrictions around newly adopted packages or newly created accounts.

Small controls placed exactly where authority changes hands. Not a giant centralized review bureaucracy. Not some LLM staring at a `PKGBUILD` and producing a green shield because it has decided the shell script has good vibes. Please, God, not that. Someone was already experimenting with Claude-based automated `PKGBUILD` scanning during the June incident, which immediately produced a [mailing-list discussion](https://lists.archlinux.org/archives/list/aur-general@lists.archlinux.org/thread/WSOYJOV72Z6BNHVFI26SPCBDWH6YQFCF/#XYBE2YAMJWEMSUZLPRHTXO3JEIAHZNIH) about how much judgment still had to sit around the scanner’s output.

## I would also like to push a package, please

There is a stupidly personal reason I want this problem solved without turning the AUR into something else. I have a draft `PKGBUILD` for [Elsewhere](projects/elsewhere/).

Elsewhere is my little Rust CLI for publishing from a website out to other platforms. It is exactly the kind of thing the AUR should contain. It's niche, maintained by onme person, possibly useful to an incredibly small number of equally weird people. There is absolutely no reason an Arch package maintainer should spend any portion of their finite lifespan caring about it.

I however, use Arch. I would love to be able to type
```sh
paru -S elsewhere
```

and install my own stupid side project like a normal package.

Naturally, I finished preparing the AUR package at roughly the same moment Arch had a malware incident bad enough to turn off AUR pushes. So the `PKGBUILD` just sits on my computer. It works. I cannot push it to the Arch User Repository because the Arch User Repository has temporarily stopped accepting pushes.

This is also why I resist the conclusion that publishing needs to become dramatically harder. Elsewhere does not need a review board. I should not have to persuade Arch that my POSSE utility advances the strategic priorities of the distribution. I should not need three existing package maintainers to attest to my character before the package may exist. I should be able to write a small build recipe, put my name on it, and let another Arch user inspect it if they decide they want the software.

## Keep the weird thing

I like the AUR. It's one of the reasons I use Arch. I like that it assumes I own my computer. I like that its answer to unsupported software is often just a small text file describing how to build it. I like that obscure software can be packaged without convincing a distribution maintainer that enough people care. I like that Aseprite can fit naturally into the system even though its licensing makes ordinary binary redistribution awkward. I like that if I disagree with the build instructions, I can change them. I like that my own completely unnecessary side project can eventually sit beside everything else without requiring permission from Arch.

This is unusually good software infrastructure. It is also infrastructure built around assumptions. One of those assumptions was that the person using it understood what they were doing well enough to recognize that an AUR package was not the same thing as an official Arch package.

The ecosystem has outgrown that assumption. That does not mean the AUR should become an App Store. It means the interfaces around it need to become more truthful. Keep telling people to read the `PKGBUILD`. Keep the user-maintained recipes. Keep the obscure packages. Keep the low barrier to contribution. Keep the glorious little hack where Aseprite can be packaged without distributing Aseprite.

But when a package changes hands, say so. When its build process changes substantially, show it. When an operation crosses from "install a package Arch built" into "execute a stranger's build instructions," do not make those things look identical and hope the documentation carries the entire security model.

The AUR's danger and its usefulness come from the same place. It gives you with the recipe and trusts you with the kitchen. That is worth preserving, but the stove should still tell you when somebody replaced the gas line.
