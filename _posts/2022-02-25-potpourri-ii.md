---
layout: post
title: "Potpourri II"
date: 2022-02-25
ready: true
---

## Keyboard remapping

As a programmer, your keyboard is your main input method. As with pretty much anything in your computer, it is configurable (and worth configuring).

The most basic change is to remap keys.
This usually involves some software that is listening and, whenever a certain key is pressed, it intercepts that event and replaces it with another event corresponding to a different key. Some examples:

- Remap Caps Lock to Ctrl or Escape. We (the instructors) highly encourage this setting since Caps Lock has a very convenient location but is rarely used.
- Remapping PrtSc to Play/Pause music. Most OSes have a play/pause key.
- Swapping Ctrl and the Meta (Windows or Command) key.

You can also map keys to arbitrary commands of your choosing. This is useful for common tasks that you perform. Here, some software listens for a specific key combination and executes some script whenever that event is detected.

- Open a new terminal or browser window.
- Inserting some specific text, e.g. your long email address or your MIT ID number.
- Sleeping the computer or the displays.

There are even more complex modifications you can configure:

- Remapping sequences of keys, e.g. pressing shift five times toggles Caps Lock.
- Remapping on tap vs on hold, e.g. Caps Lock key is remapped to Esc if you quickly tap it, but is remapped to Ctrl if you hold it and use it as a modifier.
- Having remaps being keyboard or software specific.

Some software resources to get started on the topic:

- macOS - [karabiner-elements](https://pqrs.org/osx/karabiner/), [skhd](https://github.com/koekeishiya/skhd) or [BetterTouchTool](https://folivora.ai/)
- Linux - [xmodmap](https://wiki.archlinux.org/index.php/Xmodmap) or [Autokey](https://github.com/autokey/autokey)
- Windows - Builtin in Control Panel, [AutoHotkey](https://www.autohotkey.com/) or [SharpKeys](https://www.randyrants.com/category/sharpkeys/)
- QMK - If your keyboard supports custom firmware you can use [QMK](https://docs.qmk.fm/) to configure the hardware device itself so the remaps works for any machine you use the keyboard with.

## Backups

Any data that you haven’t backed up is data that could be gone at any moment, forever.
It's easy to copy data around, it's hard to reliably backup data.
Here are some good backup basics and the pitfalls of some approaches.

First, a copy of the data in the same disk is not a backup, because the disk is the single point of failure for all the data. Similarly, an external drive in your home is also a weak backup solution since it could be lost in a fire/robbery/&c. Instead, having an off-site backup is a recommended practice.

Synchronization solutions are not backups. For instance, Dropbox/GDrive are convenient solutions, but when data is erased or corrupted they propagate the change. For the same reason, disk mirroring solutions like RAID are not backups. They don't help if data gets deleted, corrupted or encrypted by ransomware.

Some core features of good backups solutions are versioning, deduplication and security.
Versioning backups ensure that you can access your history of changes and efficiently recover files.
Efficient backup solutions use data deduplication to only store incremental changes and reduce the storage overhead.
Regarding security, you should ask yourself what someone would need to know/have in order to read your data and, more importantly, to delete all your data and associated backups.
Lastly, blindly trusting backups is a terrible idea and you should verify regularly that you can use them to recover data.

Backups go beyond local files in your computer.
Given the significant growth of web applications, large amounts of your data are only stored in the cloud.
For instance, your webmail, social media photos, music playlists in streaming services or online docs are gone if you lose access to the corresponding accounts.
Having an offline copy of this information is the way to go, and you can find online tools that people have built to fetch the data and save it.

For a more detailed explanation, see these lecture notes on [Backups](https://missing.csail.mit.edu/2019/backups/).

## APIs

We've talked a lot in this class about using your computer more
efficiently to accomplish _local_ tasks, but you will find that many of
these lessons also extend to the wider internet. Most services online
will have "APIs" that let you programmatically access their data. For
example, the US government has an API that lets you get weather
forecasts, which you could use to easily get a weather forecast in your
shell.

Most of these APIs have a similar format. They are structured URLs,
often rooted at `api.service.com`, where the path and query parameters
indicate what data you want to read or what action you want to perform.
For the US weather data for example, to get the forecast for a
particular location, you issue GET request (with `curl` for example) to
https://api.weather.gov/points/42.3604,-71.094. The response itself
contains a bunch of other URLs that let you get specific forecasts for
that region. Usually, the responses are formatted as JSON, which you can
then pipe through a tool like [`jq`](https://stedolan.github.io/jq/) to
massage into what you care about.

Some APIs require authentication, and this usually takes the form of
some sort of secret _token_ that you need to include with the request.
You should read the documentation for the API to see what the particular
service you are looking for uses, but "[OAuth](https://www.oauth.com/)"
is a protocol you will often see used. At its heart, OAuth is a way to
give you tokens that can "act as you" on a given service, and can only
be used for particular purposes. Keep in mind that these tokens are
_secret_, and anyone who gains access to your token can do whatever the
token allows under _your_ account!

[IFTTT](https://ifttt.com/) is a website and service centered around the
idea of APIs — it provides integrations with tons of services, and lets
you chain events from them in nearly arbitrary ways. Give it a look!

## Markdown

There is a high chance that you will write some text over the course of
your career. And often, you will want to mark up that text in simple
ways. You want some text to be bold or italic, or you want to add
headers, links, and code fragments. Instead of pulling out a heavy tool
like Word or LaTeX, you may want to consider using the lightweight
markup language [Markdown](https://commonmark.org/help/).

You have probably seen Markdown already, or at least some variant of it.
Subsets of it are used and supported almost everywhere, even if it's not
under the name Markdown. At its core, Markdown is an attempt to codify
the way that people already often mark up text when they are writing
plain text documents. Emphasis (_italics_) is added by surrounding a
word with `*`. Strong emphasis (**bold**) is added using `**`. Lines
starting with `#` are headings (and the number of `#`s is the subheading
level). Any line starting with `-` is a bullet list item, and any line
starting with a number + `.` is a numbered list item. Backtick is used
to show words in `code font`, and a code block can be entered by
indenting a line with four spaces or surrounding it with
triple-backticks:

    ```
    code goes here
    ```

To add a link, place the _text_ for the link in square brackets,
and the URL immediately following that in parentheses: `[name](url)`.
Markdown is easy to get started with, and you can use it nearly
everywhere. In fact, the lecture notes for this lecture, and all the
others, are written in Markdown, and you can see the raw Markdown
[here](https://raw.githubusercontent.com/missing-semester/missing-semester/master/_2020/potpourri.md).

## Docker, Vagrant, VMs, Cloud, OpenStack

[Virtual machines](https://en.wikipedia.org/wiki/Virtual_machine) and similar
tools like containers let you emulate a whole computer system, including the
operating system. This can be useful for creating an isolated environment for
testing, development, or exploration (e.g. running potentially malicious code).

[Vagrant](https://www.vagrantup.com/) is a tool that lets you describe machine
configurations (operating system, services, packages, etc.) in code, and then
instantiate VMs with a simple `vagrant up`. [Docker](https://www.docker.com/)
is conceptually similar but it uses containers instead.

You can also rent virtual machines on the cloud, and it's a nice way to get instant
access to:

- A cheap always-on machine that has a public IP address, used to host services
- A machine with a lot of CPU, disk, RAM, and/or GPU
- Many more machines than you physically have access to (billing is often by
  the second, so if you want a lot of computing for a short amount of time, it's
  feasible to rent 1000 computers for a couple of minutes)

Popular services include [Amazon AWS](https://aws.amazon.com/), [Google
Cloud](https://cloud.google.com/),[ Microsoft Azure](https://azure.microsoft.com/),
[DigitalOcean](https://www.digitalocean.com/).

If you're a member of MIT CSAIL, you can get free VMs for research purposes
through the [CSAIL OpenStack
instance](https://tig.csail.mit.edu/shared-computing/open-stack/).

## GitHub

[GitHub](https://github.com/) is one of the most popular platforms for
open-source software development. Many of the tools we've talked about in this
class, like [vim](https://github.com/vim/vim), are hosted on
GitHub. It's easy to get started contributing to open-source to help improve
the tools that you use every day.

There are two primary ways in which people contribute to projects on GitHub:

- Creating an
  [issue](https://help.github.com/en/github/managing-your-work-on-github/creating-an-issue).
  This can be used to report bugs or request a new feature. Neither of these
  involves reading or writing code, so it can be pretty lightweight to do.
  High-quality bug reports can be extremely valuable to developers. Commenting on
  existing discussions can be helpful too.
- Contribute code through a [pull
  request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests).
  This is generally more involved than creating an issue. You can
  [fork](https://help.github.com/en/github/getting-started-with-github/fork-a-repo)
  a repository on GitHub, clone your fork, create a new branch, make some changes
  (e.g. fix a bug or implement a feature), push the branch, and then [create a
  pull
  request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request).
  After this, there will generally be some back-and-forth with the project
  maintainers, who will give you feedback on your patch. Finally, if all goes
  well, your patch will be merged into the upstream repository. Often times,
  larger projects will have a contributing guide, tag beginner-friendly issues,
  and some even have mentorship programs to help first-time contributors become
  familiar with the project.

## Exercises

1. Figure out how to remap your Caps Lock key to something you use more often (such as Escape or Ctrl or Backspace).

1. Make a custom global keyboard shortcut to open a new terminal window or a new browser window.

1. Consider how you are (not) backing up your data and look into fixing/improving that.

1. Figure out how to backup your email accounts

1. Choose a webservice you use often (Spotify, Google Music, etc.) and figure out what options for backing up your data are. Often people have already made tools (such as youtube-dl) solutions based on available APIs.

1. Find and use a web api to get the weather in your city/area, and/or browse/try an IFTTT or Zapier recipe.
1. Play around with Markdown on a web editor like [this one](https://dillinger.io/)

1. Try Docker and install a simple Linux image on the container. And/or, user VirutalBox to install a VM with another operating system. Try SSHing into the container or VM.
