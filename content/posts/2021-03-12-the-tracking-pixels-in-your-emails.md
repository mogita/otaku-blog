---
title: The Tracking Pixels in Your Emails
slug: the-tracking-pixels-in-your-emails
date: 2021-03-12 12:41:00
updated:
tags: [Emails, Tracking Pixels]
cover:
desc:
share_cover:
from:
---

## What Is Tracking Pixel

The Tracking Pixel is a technique found these days in Email marketing, promotions, newsletters and even casual notifications from mostly the companies or through an Email campaign service. It inserts a transparent 1px x 1px image to the mail body that is invisible to human and use the good-old Web technology to send information to whoever's on the other end collecting many aspects about the Email reader:

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

| Sender                          | From Address                                                 | Tracking Pixel Service Provider            |
| ------------------------------- | ------------------------------------------------------------ | ------------------------------------------ |
| Sony PlayStation                | reply@txn-email.playstation.com                              | Salesforce                                 |
| IFTTT                           | alerts@ifttt.com                                             | Customer.io                                |
| Nintendo                        | nintendo-noreply@ccg.nintendo.com                            | (Self-owned Tracker)                       |
| LinkedIn                        | messages-noreply@linkedin.com                                | (Self-owned Tracker)                       |
| Duolingo                        | hello@duolingo.com                                           | SendGrid                                   |
| Lark Suite (a.k.a Feishu)       | support@service.larksuite.com                                | (Self-owned Tracker)                       |
| Bedroom Producers Blog          | tomislav@bedroomproducersblog.com                            | MailerLite                                 |
| Trello                          | taco@trello.com                                              | (Self-owned Tracker)                       |
| Dropbox                         | no-reply@dropbox.com                                         | (Self-owned Tracker)                       |
| Mapbox                          | newsletter@mapbox.com<br />billing@mapbox.com                | Customer.io                                |
| Apple App Store                 | no_reply@email.apple.com                                     | Apple                                      |
| iCloud (GCBD)                   | no_reply@iCloud.gzdata.com.cn                                | (Self-owned Tracker)                       |
| Apple iTunes Connect            | do_not_reply@email.apple.com                                 | (Self-owned Tracker)                       |
| Apple Developer                 | developer@insideapple.apple.com                              | Apple                                      |
| Be My Eyes                      | account@bemyeyes.com<br />onboarding@bemyeyes.com            | Postmark<br />Mixpanel                     |
| Goodreads                       | no-reply@mail.goodreads.com                                  | Amazon SES                                 |
| Foursquare                      | noreply@foursquare.com                                       | SendGrid                                   |
| Baidu                           | no-reply-bce@baidu.com                                       | (Self-owned Tracker)                       |
| GitHub                          | notifications@github.com                                     | GitHub                                     |
| Microsoft 365                   | Microsoft365@mail.microsoft365.com                           | (Self-owned Tracker)                       |
| Cloudflare                      | newsletter@cloudflare.com                                    | (Self-owned Tracker)                       |
| Sentry                          | webinars@sentry.io                                           | (Self-owned Tracker)                       |
| Upwork                          | donotreply@upwork.com                                        | Upwork                                     |
| AWS                             | no-reply@marketplace.aws                                     | Amazon SES                                 |
| Facebook                        | security@facebookmail.com<br />notification@facebookmail.com | Facebook                                   |
| Paypal                          | service@intl.paypal.com                                      | (Self-owned Tracker)<br />Return Path      |
| Stripe                          | receipts+masked@stripe.com                                   | (Self-owned Tracker)                       |
| Airbnb                          | automated@airbnb.com                                         | (Self-owned Tracker)                       |
| Coursera                        | no-reply@m.mail.coursera.org                                 | (Self-owned Tracker)                       |
| Console.dev                     | weekly@console.dev                                           | Mailchimp                                  |
| AudioThing.net                  | news@audiothing.net                                          | Mailchimp                                  |
| BugSnag                         | bugsnagmarketing@bugsnag.com                                 | (Self-owned Tracker)                       |
| GOG                             | newsletter@email2.gog.com                                    | <del>Google</del><br />GetResponse         |
| IMDB                            | do-not-reply@imdb.com                                        | Amazon SES                                 |
| CloudApp                        | no-reply@getcloudapp.com                                     | Customer.io                                |
| PreSonus                        | presonus@e.presonus.com                                      | (Self-owned Tracker)                       |
| Internet Archive                | info@archive.org                                             | Mailchimp                                  |
| Box                             | boxteam@customer.box.com                                     | spmailtechnol                              |
| IHG (A hospitality company)     | IHGRewards@mc.ihg.com                                        | (Self-owned Tracker, more than 1 per mail) |
| TuringBook.com                  | ebook@turingbook.com                                         | Mailchimp                                  |
| Raycast                         | thomas@raycast.com                                           | SendGrid                                   |
| RescueTime                      | notifications@rescuetime.com                                 | (Self-owned Tracker)                       |
| Chatra                          | support@chatra.com                                           | Postmark                                   |
| Bandcamp                        | noreply@bandcamp.com                                         | (Self-owned Tracker)                       |
| Craft.do                        | team@craft.do                                                | (Self-owned Tracker)                       |
| Journal                         | hello@usejournal.com                                         | SendGrid                                   |
| Mockaroo                        | no-reply@mockaroo.com                                        | Mailchimp                                  |
| Ghost.org (A blogging software) | hello@ghost.org                                              | (Self-owned Tracker)                       |
| Asana                           | no-reply@asana.com                                           | (Self-owned Tracker)                       |
| Remove.bg                       | noreply@remove.bg                                            | (Self-owned Tracker)                       |
| uTalk                           | hello@offers2.utalk.com                                      | (Self-owned Tracker)                       |
| Envato                          | do-not-reply@market.envato.com                               | Mandrillapp                                |
| RapidAPI                        | support@rapidapi.com                                         | Mailchimp                                  |
| BundleHunt                      | support@bundlehunt.com                                       | Mailchimp                                  |
| TIDAL                           | account@info.tidal.com                                       | SendGrid                                   |
| ClickUp                         | katie@clickup.com<br />success@clickup.com<br />help@clickup.com<br />clickupdates@clickup.com | Close                                      |
| Clubhouse.io                    | support@clubhouse.io                                         | Intercom                                   |
| Postman.com                     | postman-team@notifications.postman.com                       | Fastic                                     |
| Taiga                           | peter@mail.taiga.io                                          | SendGrid                                   |
| Koingo Software                 | appdeals@koingo.com                                          | SendGrid                                   |
| Lobe.ai                         | lobe@e-mail.microsoft.com                                    | Salesforce                                 |
| Native Instruments              | newsletter@news.native-instruments.com                       | (Self-owned Tracker)                       |
| Atlassian                       | info@e.atlassian.com                                         | Return Path                                |
| Twitch                          | no-reply@twitch.tv                                           | (Self-owned Tracker)                       |
| JustFont                        | su.weihsiang@justfont.com                                    | Google                                     |
| JFrog                           | team@go.jfrog.com                                            | (Self-owned Tracker)                       |
| NameSilo                        | abnous@namesilo.com                                          | MailerLite                                 |
| Riot Games                      | noreply@mail.accounts.riotgames.com                          | SendGrid                                   |
| NASA Mars                       | no-reply-mars@jpl.nasa.gov                                   | iContact                                   |
| Zeplin                          | hi@zeplin.io                                                 | Mailchimp                                  |
| Pipedream                       | sacerdoti@pipedream.com                                      | SendGrid                                   |
| Google Payments                 | payments-noreply@google.com                                  | Google                                     |
| Climb App                       | (hidden due to Apple Login)                                  | SendGrid                                   |
| China Merchants Bank            | 95555ad@message.cmbchina.com                                 | (Self-owned Tracker)                       |
| Magoosh                         | help@magoosh.com                                             | SendGrid                                   |

It can go very long as more and more services I use. And I was tracked all the way long.

> Update 2021-03-22: It appears that many of the "Self-owned Trackers" are not owned by the business owners. They don't reveal the actual vendor by using the custom domain names. Certain tracing was done to disover the actual campaign Email sender and the tracker, which showed a certain pattern of how the custom domain names are formed. E.g. `http://url1234.mail.some-service.io` (certain parts were masked with dummy characters) points to the IP address that resolves to be in possession of SendGrid. Oh my they use the `http` that scatters people's information all around the web un-encrypted while relentlessly collecting them.

## How to avoid being tracked?

If you are using a macOS computer, check out the open-source project [MailTrackerBlocker](https://github.com/apparition47/MailTrackerBlocker). It's a plug-in for the Apple Mail app. Actually the list above was sorted out using this plug-in.

Alternatively you can choose an Email service that provides tracking pixel blocking natively, like [Hey](https://hey.com) (paid). In fact the makers of Hey mail made this website to stand up against tracking pixels: https://notospypixels.com

For blocking tracking pixels on other platforms, applications or Email providers, you can [write to me](mailto:me@mogita.com?subject=[Otaku%20Blog]%20Avoid%20Tracking%20Pixels) and tell me your method or tools. I'd be happy to add them here.
