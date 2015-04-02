---
layout: post
title: Enabling SendMail on Ubuntu
author: Oyewale Oyediran
feature-image: tux-mail-1ty.gif
tags:
- ubuntu
- code
- sendmail
- unix
- php
- appengine
- web
---


So I guess you are building an application that requires email to be sent out. Depending on your platform (PHP has the builtin [mail()] function and perhaps AppEngine's [Sendmail function]), you might find yourself needing to use the Unix sendmail function.

To Install SendMail on Ubuntu, just run the command
-------------------------------------------------------

```sh
# sudo apt-get install sendmail
```


To modify the default configuration:
-------------------------------------------------------

```sh
# sendmailconfig
```



Now your PHP mail() function and other services that depend on Sendmail should be firing now



Additionally, you might want to tail the log file to monitor sendmail functions. 

```sh
# tail -f /var/log/maillog
```



---
So I hope you have a smoother devlopment experience with mails from your local machine.

![placeholder](/images/tux-mail-1ty.gif "Tux")


** ..with love from Oyewale. **


[Sendmail function]:https://developers.google.com/appengine/docs/python/mail/functions#send_mail
[mail()]:http://www.php.net/manual/en/function.mail.php