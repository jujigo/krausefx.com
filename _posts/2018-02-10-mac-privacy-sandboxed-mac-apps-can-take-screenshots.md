---
layout: post
title: 'Mac Privacy: Sandboxed Mac apps can record your screen at any time without you knowing'
categories: []
tags:
- mac
- screenshot
- privacy
status: publish
type: post
published: true
meta: {}
---

<div class="press">
  <a href="https://news.ycombinator.com/item?id=16350277">
    <img src="/assets/privacy/hackernews.ico">
  </a>
  <a href="https://www.heise.de/mac-and-i/meldung/Mac-Apps-koennen-heimlich-Bildschirminhalt-aufzeichnen-trotz-Sandbox-3965929.html">
    <img src="/assets/privacy/heise.svg">
  </a>
  <a href="https://daringfireball.net/2018/02/non_native_apps_threat_to_mac">
    <img src="/assets/privacy/DaringFireball.png">
  </a>
  <a href="https://lifehacker.com/how-mac-apps-can-spy-on-your-computer-1822928525">
    <img src="/assets/privacy/Lifehacker.png">
  </a>
</div>

---

**Update 2019-06-04** Apple has fixed this issue with macOS Catalina

## Facts

Any Mac app, sandboxed or not sandboxed can:

- Take screenshots of your Mac silently without you knowing
- Access every pixel, even if the Mac app is in the background
- Use basic [OCR software](https://en.wikipedia.org/wiki/Optical_character_recognition) to read the text on the screen
- Access all connected monitors

## What's the worst that could happen?

- Read password and keys from password managers
- Detect what web services you use (e.g. email provider)
- Read all emails and messages you open on your Mac
- When a developer is targeted, this allows the attacker to potentially access sensitive source code, API keys or similar data
- Learn personal information about the user, like their bank details, salary, address, etc.

<a href="/assets/posts/mac-take-screenshots.png" target="_blank">
  <img src="/assets/posts/mac-take-screenshots.png" />
</a>

## Disclaimer

This project is a proof of concept and should not be used in production. The goal is to highlight a privacy loophole that can be abused by Mac apps.

## How can I protect myself as a user?

To my knowledge there is no way to protect yourself as of now.

## Proposal

There are lots of valid use-cases for Mac apps to record the screen, e.g. [1Password 2fA support](https://support.1password.com/one-time-passwords/), screen recording software or even simple screen sharing via your web browser or Skype. However there must be some kind of control:

- The App Store review process could verify the Sandbox entitlements for accessing the screen
- Put the user in charge with a permission dialog
- Additionally the user should be notified whenever an app accesses the screen.

Of course, I also [filed a radar](https://openradar.appspot.com/radar?id=5610698700750848) (rdar://37423927) to notify Apple about this issue.

## How does it work?

A developer just needs to use `CGWindowListCreateImage` to generate a capture of the complete screen within an instant:

```ruby
CGImageRef screenshot = CGWindowListCreateImage(
  CGRectInfinite, 
  kCGWindowListOptionOnScreenOnly, 
  kCGNullWindowID, 
  kCGWindowImageDefault);

NSBitmapImageRep *bitmapRep = [[NSBitmapImageRep alloc] initWithCGImage:screenshot];
```

In my experiments, I piped the generated image over to a [OCR library](https://en.wikipedia.org/wiki/Optical_character_recognition) and was able to get all text that was rendered on the user's machine.

**Update 2019-06-04** Looks like Apple has fixed this issue with macOS Catalina

## Similar projects I've worked on 

I published more posts on how to access the camera, the user's location data, their Mac screen and their iCloud password, check out [krausefx.com/privacy](/privacy) for more.
