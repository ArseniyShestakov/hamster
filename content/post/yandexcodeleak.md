+++
title = "Yandex Services Source Code Leak"
description = "Short overview of breach contents"
tags = [
    "cybersecurity",
    "hobby"
]
date = "2023-01-26"
comments = true
+++

Just a few hours ago I found [mention on Twitter](https://twitter.com/dbalakov/status/1618383988351201282) that proprietary source code of Russian giant Yandex been leaked on online community called *BreachForums*. In this post I'll share results of my **friend** digging into said archives.

Important details about torrent:

* It just content of repository without anything else.
* All files are dated back to [24 February 2022](https://en.wikipedia.org/wiki/Russo-Ukrainian_War).
* It does not contain git history, mostly just code
* No pre-built binaries for most of software with only few exceptions
* There are no pre-trained ML models with some exceptions

# Non-commercial announcement

While you're reading this post please consider donating to [Helping Hand](https://handofhelp.com.ua) for Ukraine Relief. This is charity friend of mine work for and it actually help civilians affected by Russian agression. Thanks for reading this bit!

# Why is this big?

Yandex is one of largest IT companies in Russia. Within country it provide wider range of services than Google. Imagine one company that replace Google, Uber, Amazon, Netflix and Spotify.

# Is this leak real?

I personally never worked at Yandex, but I know several people who worked there at different times or work there still. I verified that at least some of archives for sure contain modern source code for company services as well as documentation pointing to real intranet URLs.

# What's inside

It looks like at least source code for all major services of Yandex been leaked:

* Search Engine and Indexing Bot
* Maps - Like Google Maps and Street View
* Alice - AI assistant like Siri / Alexa
* Taxi - Uber-like taxi service
* Direct - Ads service like Google Ads / Adwords
* Mail - Mail service like GMail
* Disk - File storage service like Google drive
* Market - Marketplace like Amazon
* Travel - Like a Booking.com plus Airplane, Train and Bus tickets
* Yandex360 - Like Google Workspaces for services on your own domain
* Cloud - Probably not all infrastructure code was leaked.
* Pay - Payment processing like Stripe, but with limited set of features
* Metrika - Like Google Analytics

And at least backend part of majority of other company services is there.
Largest archive called "frontend" is yet to be explored.

# Full file list of files:

If you dont want to download torrent, but curious of what's inside you can get list of files from following gist:

[https://gist.github.com/ArseniyShestakov/53a80e3214601aa20d1075872a1ea989](https://gist.github.com/ArseniyShestakov/53a80e3214601aa20d1075872a1ea989)

You can also clone it like normnal git repository:

```
git clone https://gist.github.com/ArseniyShestakov/53a80e3214601aa20d1075872a1ea989
```

List of all files can be obtained with [following commands](https://gist.github.com/ArseniyShestakov/4863f6b30967ad70ac62f0ae324e00b2).


# Full list of files in torrent

```
aapi.tar.bz2                        client_method.tar.bz2               gencfg.tar.bz2                      mobile-WARNING-notfull.tar.bz2.part skynet.tar.bz2
admins.tar.bz2                      cloud.tar.bz2.part                  groups.tar.bz2                      nginx.tar.bz2                       smart_devices.tar.bz2.part
ads.tar.bz2                         commerce.tar.bz2.part               helpdesk.tar.bz2                    noc.tar.bz2.part                    smarttv.tar.bz2
alice.tar.bz2.part                  config.tar.bz2                      infra.tar.bz2                       partner.tar.bz2                     solomon.tar.bz2.part
analytics.tar.bz2.part              connect.tar.bz2.part                intranet.tar.bz2                    passport.tar.bz2.part               stocks.tar.bz2
antiadblock.tar.bz2                 crm.tar.bz2.part                    investors.tar.bz2                   pay.tar.bz2                         switch.tar.bz2
antirobot.tar.bz2                   crypta.tar.bz2                      it-office.tar.bz2                   payplatform.tar.bz2.part            tasklet.tar.bz2
autocheck.tar.bz2                   customer_service.tar.bz2            jupytercloud.tar.bz2                paysys.tar.bz2                      taxi.tar.bz2.part
balancer.tar.bz2                    datacloud.tar.bz2                   kernel.tar.bz2.part                 portal.tar.bz2.part                 tools.tar.bz2
billing.tar.bz2                     delivery.tar.bz2.part               library.tar.bz2.part                privacy_office.tar.bz2              travel.tar.bz2.part
bindings.tar.bz2                    direct.tar.bz2.part                 load.tar.bz2.part                   products.tar.bz2                    wmconsole.tar.bz2
captcha.tar.bz2                     disk.tar.bz2                        mail.tar.bz2.part                   robot.tar.bz2                       yandex360.tar.bz2.part
cdn.tar.bz2                         docs.tar.bz2                        maps.tar.bz2.part                   rt-research.tar.bz2                 yandex_io.tar.bz2.part
certs.tar.bz2                       drive.tar.bz2.part                  maps_2.tar.bz2.part                 saas.tar.bz2                        yaphone.tar.bz2
ci.tar.bz2.part                     extsearch.tar.bz2.part              maps_adv.tar.bz2                    sandbox.tar.bz2                     yawe.tar.bz2
classifieds.tar.bz2.part            frontend.tar.bz2.part               market.tar.bz2.part                 search.tar.bz2
client_analytics.tar.bz2.part       fuzzing.tar.bz2                     metrika.tar.bz2.part                security.tar.bz2
```

# Security implications.

Since this is leak only contain contents of git repositories there is no personal data. There are at least some API keys, but they are likely only been used for testing deployment only.
