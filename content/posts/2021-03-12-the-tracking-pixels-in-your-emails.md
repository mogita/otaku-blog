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
| Adobe                           | mail@mail.adobe.com<br />message@adobe.com                   | Adobe                                      |
| IFTTT                           | alerts@ifttt.com                                             | Customer.io                                |
| Nintendo                        | nintendo-noreply@ccg.nintendo.com                            | (Self-owned Tracker)                       |
| LinkedIn                        | messages-noreply@linkedin.com                                | LinkedIn                                   |
| Duolingo                        | hello@duolingo.com                                           | Amazon SES                                 |
| Flickr                          | flickrteam@arrow.flickr.com                                  | SparkPost                                  |
| Lark Suite (a.k.a Feishu)       | support@service.larksuite.com                                | SendGrid                                   |
| Bedroom Producers Blog          | tomislav@bedroomproducersblog.com                            | MailerLite                                 |
| Trello                          | taco@trello.com<br />do-not-reply@trello.com<br />invitation-do-not-reply@trello.com | Atlassian                                  |
| Dropbox                         | no-reply@dropbox.com                                         | (Self-owned Tracker)                       |
| CodeSandbox                     | hello@codesandbox.io                                         | Mailgun                                    |
| Mapbox                          | newsletter@mapbox.com<br />billing@mapbox.com<br />sales@mapbox.com<br />community@mapbox.com | Customer.io                                |
| MapTiler                        | news@maptiler.com                                            | Google                                     |
| Mapillary                       | support@mapillary.com                                        | SendGrid                                   |
| Apple App Store                 | no_reply@email.apple.com                                     | Apple                                      |
| iCloud (GCBD)                   | no_reply@iCloud.gzdata.com.cn                                | (Self-owned Tracker)                       |
| Apple iTunes Connect            | do_not_reply@email.apple.com                                 | (Self-owned Tracker)                       |
| Apple Developer                 | developer@insideapple.apple.com                              | Apple                                      |
| Be My Eyes                      | account@bemyeyes.com<br />onboarding@bemyeyes.com            | Postmark<br />Mixpanel                     |
| Goodreads                       | no-reply@mail.goodreads.com                                  | Amazon SES                                 |
| Foursquare                      | noreply@foursquare.com                                       | SendGrid                                   |
| Baidu                           | no-reply-bce@baidu.com                                       | (Self-owned Tracker)                       |
| GitHub                          | notifications@github.com                                     | GitHub                                     |
| Microsoft                       | Microsoft365@mail.microsoft365.com<br />msa@communication.microsoft.com<br />azure@email.microsoft.com | Microsoft                                  |
| Cloudflare                      | newsletter@cloudflare.com                                    | (Self-owned Tracker)                       |
| Sentry                          | webinars@sentry.io                                           | Hubspot                                    |
| Upwork                          | donotreply@upwork.com                                        | Upwork                                     |
| AWS                             | no-reply@marketplace.aws                                     | Amazon SES                                 |
| Facebook                        | security@facebookmail.com<br />notification@facebookmail.com | Facebook                                   |
| Instagram                       | security@mail.instagram.com                                  | Facebook                                   |
| Paypal                          | service@intl.paypal.com                                      | (Self-owned Tracker)<br />Return Path      |
| Stripe                          | receipts+masked@stripe.com                                   | (Self-owned Tracker)                       |
| Auth0                           | no-reply@emails.auth0.com<br />no-reply-support@auth0.com    | SendGrid<br />Mandrill                     |
| Airbnb                          | automated@airbnb.com                                         | (Self-owned Tracker)                       |
| Coursera                        | no-reply@m.mail.coursera.org                                 | SparkPost                                  |
| Console.dev                     | weekly@console.dev                                           | Mailchimp                                  |
| AudioThing                      | news@audiothing.net                                          | Mailchimp                                  |
| BugSnag                         | bugsnagmarketing@bugsnag.com                                 | (Self-owned Tracker)                       |
| Slite                           | marc@slite.com                                               | SendGrid                                   |
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
| Clubhouse.io                    | support@clubhouse.io<br />mayrelease@splash.events           | Intercom<br />SendGrid                     |
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
| Google Workspace                | google-workspace-alerts-noreply@google.com                   | Google                                     |
| Google Cloud Platform           | CloudPlatform-noreply@google.com                             | Google                                     |
| Climb App                       | (hidden due to Apple Login)                                  | SendGrid                                   |
| China Merchants Bank            | 95555ad@message.cmbchina.com                                 | (Self-owned Tracker)                       |
| Magoosh                         | help@magoosh.com                                             | SendGrid                                   |
| Todoist                         | no-reply@todoist.com                                         | SendGrid                                   |
| MuseScore                       | no-reply@musescore.com                                       | (Self-owned Tracker)                       |
| GitGuardian                     | support@gitguardian.com<br />security@mail.gitguardian.com<br />contact@gitguardian.com | SendGrid<br />Mailgun                      |
| Speed Dial 2                    | hello@speeddial2.com                                         | Postmark                                   |
| Sococo                          | noreply@sococo.net                                           | Mandrill                                   |
| Chamaileon                      | hello@chamaileon.io                                          | Sendy                                      |
| Browserstack Team               | news@browserstack.com                                        | Mailchimp                                  |
| Ultimate Guitar                 | no_reply@ultimate-guitar.com                                 | Mailtrain                                  |
| Bunny Studio                    | support@bunnystudio.com                                      | SendGrid                                   |
| Waymo                           | noreply@waymo.com                                            | Google                                     |
| 数码荔枝 lizhi.io               | hi@tongxun.lizhi.io                                          | Mailchimp                                  |
| Three Dots Labs                 | contact@threedotslabs.com                                    | Mailchimp                                  |
| Vercel                          | ship@vercel.com                                              | SendGrid                                   |
| Guitar Pro                      | contact@arobas-music.com                                     | Mailchimp                                  |
| Loom                            | welcome@loom.com<br />team@loom.com<br />no-reply@loom.com   | Intercom<br />Mandrill                     |
| Opstree                         | riya@opstree.com                                             | Hubspot                                    |
| WeTransfer                      | noreply@wetransfer.com                                       | SendGrid                                   |
| Flim                            | hello@flim.ai                                                | Mailjet                                    |
| Grab                            | corpsupport.sg@grab.com                                      | Amazon SES                                 |
| Pusher                          | team@pusher.com                                              | Customer.io                                |
| Chess.com                       | hello@chess.com<br />alert@chess.com<br />receipt@chess.com  | Fastic<br />SendGrid<br />SendGrid         |
| Soundtoys                       | news@soundtoys.com                                           | Mailchimp                                  |
| italki                          | noreply@italki.com                                           | Amazon SES                                 |
| Codecov                         | hello@codecov.io                                             | (Self-owned Tracker)                       |
| Wurkr                           | helena@wurkr.io                                              | Hubspot                                    |
| Coinbase                        | info@cb.mail.coinbase.com                                    | Fastic                                     |
| Zapier                          | billing@zapier.com                                           | Litmus                                     |
| HBO Max                         | HBOMax@mail.hbomax.com                                       | SendGrid                                   |
| Malaysia Kini                   | newslab@malaysiakini.com<br />membership@malaysiakini.com    | Amazon SES                                 |
| Book Depository                 | reply@support.bookdepository.com                             | Litmus                                     |
| Anytype                         | hello@anytype.io                                             | SendGrid                                   |
| Change.org                      | change@a.change.org                                          | (Self-owned Tracker)                       |
| Flo                             | legal@floemail.com                                           | SendGrid                                   |
| Medium                          | noreply@medium.com                                           | SendGrid                                   |
| LingQ                           | ana.rivera@lingq.com<br />support@lingq.com                  | SendGrid                                   |
| WordPress                       | comment-reply@wordpress.com                                  | WordPress                                  |
| Leanpub                         | news@leanpub.com                                             | Mailchimp                                  |
| Pocket Casts                    | noreply@pocketcasts.com                                      | Mandrill                                   |
| Amazon Web Services             | aws-cn-marketing-email-replies@amazon.com                    | Amazon SES                                 |
| Spotify                         | no-reply@spotify.com                                         | SendGrid                                   |
| Los Angeles Valley Collage      | LAVCstudentsupport@laccd.edu                                 | iContact                                   |
| StarWind                        | brooke.johnson@starwind.com                                  | Hubspot                                    |
| Product Hunt                    | hello@team.producthunt.com                                   | Mailjet                                    |
| Gengo Translator Team           | no-reply@gengo.com                                           | SendGrid                                   |
| Robinhood Snacks                | noreply@robinhood.com                                        | SendGrid                                   |
| Live2D                          | support@live2d.com                                           | Mailchimp                                  |
| JetBrains                       | news@jetbrains.com                                           | Adobe                                      |
| Docker                          | no-reply@notify.docker.com                                   | Mailgun                                    |
| Discord                         | noreply@discord.com                                          | SendGrid                                   |
| Skillshare                      | hello@skillshare.com                                         | SendGrid                                   |
| Kickstarter                     | no-reply@kickstarter.com                                     | SendGrid                                   |

It can go very long as more and more services I use. And I was tracked all the way long.

> Update 2021-03-22: It appears that many of the "Self-owned Trackers" are not owned by the business owners. They don't reveal the actual vendor by using the custom domain names. Certain tracing was done to disover the actual campaign Email sender and the tracker, which showed a certain pattern of how the custom domain names are formed. E.g. `http://url1234.mail.some-service.io` (certain parts were masked with dummy characters) points to the IP address that resolves to be in possession of SendGrid. Oh my they use the `http` that scatters people's information all around the web un-encrypted while relentlessly collecting them.

## How to avoid being tracked?

If you are using a macOS computer, check out the open-source project [MailTrackerBlocker](https://github.com/apparition47/MailTrackerBlocker). It's a plug-in for the Apple Mail app. Actually the list above was sorted out using this plug-in.

Alternatively you can choose an Email service that provides tracking pixel blocking natively, like [Hey](https://hey.com) (paid). In fact the makers of Hey mail made this website to stand up against tracking pixels: https://notospypixels.com

For blocking tracking pixels on other platforms, applications or Email providers, you can [write to me](mailto:me@mogita.com?subject=[Otaku%20Blog]%20Avoid%20Tracking%20Pixels) and tell me your method or tools. I'd be happy to add them here.

