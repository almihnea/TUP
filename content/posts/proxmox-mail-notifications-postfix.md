---
title: "Enable PROXMOX/Debian mail notitications via a Gmail address with postfix "
date: 2023-11-16T16:20:23+03:00
# bookComments: false
# bookSearchExclude: false
---
Open up your nodes shell (this has to be done on every node).

We are going to install some *MTA* (i.e. mail transfer agent) utilities.

```bash
apt install libsasl2-modules mailutils postfix-pcre
```

Once done, you'll need to get yourself a g-mail address with 2FA enabled and generate an application password for it. 
Do not use your personal e-mail. Do google how to get an app password if you don't know how to.

Edit the file located at : */etc/postfix/sasl_passwd*

```bash
[smtp.gmail.com]:587 
notyourpersonalemail@gmail.com:thisshouldbeyour16characterlongpasswordthatwasgiventoyou
```

There are only two lines in the file, and the e-mail and password are separated by ":"


We are now going to edit a header file. This is done because you would, idealy, recieve e-mails on your personal e-mail from that gmail address you filled in earlier, but the name of the "person" is going to show up as "root". You most likely do not want that. I name my "persons" after the hostname of the server that is sending the e-mail. In this case, it will be *Rhea*
```bash
vi /etc/postfix/smtp_header_checks
```
```bash
/^From:.*/ REPLACE From: Rhea anything@anything.com
```

One line, that's it. Replace *Rhea* with whatever you want, the e-mail address here (i.e. anything@anything.com) **does not matter or have anything to do** with senders of recievers, leave it like this or maybe put in something funny ;=)


Now, lets use postmap on the two files we recently edited. And do some chmodding.

```bash
postmap /etc/postfix/smtp_header_checks
chmod 600 /etc/postfix/sasl_passwd
postmap hash:/etc/postfix/sasl_passwd
```
We are now going to edit the file located at */etc/postfix/main.cf*

Delete **everything** that is in that file and paste in the following:


```bash
# See /usr/share/postfix/main.cf.dist for a commented, more complete version
smtpd_banner = $myhostname ESMTP $mail_name (Debian/GNU)
biff = no
mydestination =
# appending .domain is the MUA's job.
append_dot_mydomain = no
# Uncomment the next line to generate "delayed mail" warnings
#delay_warning_time = 4h
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
mynetworks = 127.0.0.0/8
inet_interfaces = loopback-only
recipient_delimiter = +

compatibility_level = 3.6

#
relayhost = [smtp.gmail.com]:587
smtp_use_tls = yes
smtp_sasl_auth_enable = yes
smtp_sasl_security_options = noanonymous
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt

#mydestination = $myhostname, localhost.$mydomain, localhost
smtp_header_checks = pcre:/etc/postfix/smtp_header_checks

```

Just a tad more work. Do the following

```bash
postconf compatibility_level=3.6
postfix reload
```

And now let's test it!
```bash
echo "This is a test message sent from postfix from my Proxmox Server" | mail -s "Subject of the e-mail" destinationEmail@probablyGmail.com
```

In my experience, this works the best when you send from gmail addresses to gmail addresses, so keep that in mind.

For logs, check the following locations:
```bash
cat /var/log/mail.warn
cat /var/log/mail.info
```

Now go into the PROXMOX VE web gui and configure your recieving e-mail address here:

![WEBUI](/sc43.webp)

Now, if you configure backup jobs, you will recieve an e-mail each time it is done. You will also recieve e-mails for updated packages if you configure proxmox to do so and, more importantly, you will recieve e-mails for ZFS related problems, say, in case one of your drive fails, or starts to fail.


Congratulations!