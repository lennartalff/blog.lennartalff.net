---
layout: single
title:  "ZSH Autosuggestions Duplicate Characters"
excerpt: "Tab-completion sometimes replicates characters when using zsh-autosuggestions. Setting the locale environment variables solves this issue."
date:   2022-06-28
# last_modified_at: "{% last_modified_at %}"
categories: Software
tags: ["software", "server", "fix"]
toc: true
author: Thies Lennart Alff
# header:
#     overlay_image: /assets/images/efibootmgr.png
#     overlay_filter: 0.5

---

I have noticed that sometimes the `zsh-autosuggestions` plugin repeats characters after using tab-completion. This happens when the locale environment variables are not set correctly (a detailed explanation can found [here](https://unix.stackexchange.com/a/90876)).

The environment variables can be set persistently in `/etc/default/locale` for Ubuntu systems. My setup looks like

{% highlight sh %}
LANG=en_US.UTF-8
LC_TIME=de_DE.UTF-8
LC_MONETARY=de_DE.UTF-8
LC_PAPER=de_DE.UTF-8

{% endhighlight %}

For my Ubuntu server I realized that the settings in `/etc/default/locale` have been ignored. This can be avoided by setting `UsePAM yes` in `/etc/ssh/sshd_config`. For Debian this would be the default setting.