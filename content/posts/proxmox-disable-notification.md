---
title: "How to disable the \"PROXMOX you don't have a valid license for this server popup\" fast"
date: 2023-07-06T20:21:48+03:00
# bookComments: false
# bookSearchExclude: false
---

So, if you're **not** paying for the *stable* version of **PROXMOX VE**, along with the ever-lasting problems of deleting repositories, each time your web-browser session expires and after you log in, you'll be welcomed by a notification saying yo do not have an active subscription to the stable repositories. We want to get rid of it. 

To do so, login into the **PROXMOX VE** web GUI, or ssh into a node's console:

![WEBUI](/proxmox50.webp)

While I am not a big fan of running scrips downloaded from the internet, you may check the code by yourself in the github repo:
[Github repo hyperlink](https://raw.githubusercontent.com/foundObjects/pve-nag-buster/master/install.sh )


Or copy-paste the following link:
https://raw.githubusercontent.com/foundObjects/pve-nag-buster/master/install.sh 

Run the following commands in the terminal:

```bash
wget https://raw.githubusercontent.com/foundObjects/pve-nag-buster/master/install.sh 

chmod +x install.sh

./install.sh
```
The nag is gone.

Until the next major update of PROXMOX, you should be fine.


