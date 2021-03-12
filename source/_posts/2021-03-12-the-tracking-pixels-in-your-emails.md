---
title: The Tracking Pixels in Your Emails
permalink: the-tracking-pixels-in-your-emails
date: 2021-03-12 12:41:00
updated:
tags: [Emails, Tracking Pixels]
cover:
desc:
share_cover:
from:
---

## What Is Tracking Pixel

The Tracking Pixel is a technique found these days in Email marketing, promotions, newsletters and even casual notifications from mostly the  companies or through an Email campaign service. It inserts a transparent 1px x 1px image to the mail body that is invisible to human and use the good-old Web technology to send information to whoever's on the other end collecting many aspects about the Email reader:

- Operating system
- Device model
- Type of the Email client
- The reader's screen resolution
- When and how many times the Email was read or website visited
- Activities on the website in a session
- The IP address of the reader (this gives away the ISP information and even location based on some extremely precise GeoIP databases that are very easy to get)

A sample tracking pixel snippet from [MailTrackerBlocker](https://github.com/apparition47/MailTrackerBlocker):

```diff
<a style="color: #770506;">
  <img src="http://cdn.website.com/newsletter/logo.png" width="438" height="42" border="0" style="max-width: 90%; height: auto" alt="logo.png">
</a>

<br>
<a href="https://website.us5.list-manage.com/unsubscribe?u=abdef">Click here to unsubscribe</a> or <a href="https://website.us5.list-manage.com/profile?u=abdef">Update subscription preferences</a>

- <img width="0" height="0" class="mailtrack-img" alt="" style="display:flex" src="https://mailtrack.io/trace/mail/0eabccbe98c98e9b8e9a8b89eab89ce9ab89e8bc.png?u=1234567">
```

Note that the highlighted line of code inserts the invisible pixel of image to the mail body.

Actually very few people ever noticed that their behaviours were tracked (or to say harvested) by the people who sent the Emails. But as soon as one realises it, bad feelings come pouring in: you don't know which Emails have tracking pixels; you can never "opt-out" a tracking pixel; you don't know exactly what information were collected by the trackers and what they plan to do with it.

## Who Are Tracking Me

To name just a few Email senders I've caught embedding the tracking pixels, here's a list:

| Sender                    | From Address                                      | Tracking Pixel Service Provider |
| ------------------------- | ------------------------------------------------- | ------------------------------- |
| Sony PlayStation          | reply@txn-email.playstation.com                   | Salesforce                      |
| IFTTT                     | alerts@ifttt.com                                  | Customer.io                     |
| Nintendo                  | nintendo-noreply@ccg.nintendo.com                 | (Self-owned Service)            |
| LinkedIn                  | messages-noreply@linkedin.com                     | (Self-owned Service)            |
| Duolingo                  | hello@duolingo.com                                | Amazon SES                      |
| Lark Suite (a.k.a Feishu) | support@service.larksuite.com                     | (Self-owned Service)            |
| Bedroom Producers Blog    | tomislav@bedroomproducersblog.com                 | MailerLite                      |
| Trello                    | taco@trello.com                                   | (Self-owned Service)            |
| Mapbox                    | newsletter@mapbox.com<br />billing@mapbox.com     | Customer.io                     |
| Apple App Store           | no_reply@email.apple.com                          | (Self-owned Service)            |
| Be My Eyes                | account@bemyeyes.com<br />onboarding@bemyeyes.com | Postmark<br />Mixpanel          |
| GitHub                    | notifications@github.com                          | (Self-owned Service)            |
| Microsoft 365             | Microsoft365@mail.microsoft365.com                | (Self-owned Service)            |
| Cloudflare                | newsletter@cloudflare.com                         | (Self-owned Service)            |
| Console.dev               | weekly@console.dev                                | Mailchimp                       |
| AudioThing.net            | news@audiothing.net                               | Mailchimp                       |
| BugSnag                   | bugsnagmarketing@bugsnag.com                      | (Self-owned Service)            |
| GOG                       | newsletter@email2.gog.com                         | Google                          |
| IMDB                      | do-not-reply@imdb.com                             | Amazon SES                      |

It can go very long as more and more services I use. And I was tracked all the way long.

## How to avoid being tracked?

If you are using a macOS computer, check out the open-source project [MailTrackerBlocker](https://github.com/apparition47/MailTrackerBlocker). It's a plug-in for the Apple Mail app. Actually the list above was sorted out using this plug-in.

Alternatively you can choose an Email service that provides tracking pixel blocking natively, like [Hey](https://hey.com) (paid). In fact the makers of Hey mail made this website to stand up against tracking pixels: https://notospypixels.com

For blocking tracking pixels on other platforms, applications or Email providers, you can [write to me](mailto:me@mogita.com?subject=[Otaku Blog] Avoid Tracking Pixels) and tell me your method or tools. I'd be happy to add them here.