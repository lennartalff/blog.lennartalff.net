---
layout: single
excerpt: Recover data from a failing unresponsive SD card with ddrescue
title:  "Recover Data From a Corrupted SD Card"
categories: Software
tags: [software, hardware, linux]
toc: true
# header:
#     overlay_image: /assets/images/ov9281_b0165.jpg
#     overlay_filter: 0.5

---


Let's start with a 'quick' run. And by quick I mean unbearbable slow if you just want to get stuff done.

{% highlight sh %}

sudo ddrescue -n /dev/mmcblk0 rescue.img rescue.map

{% endhighlight %}

By passing the `-n` option we skip the very time consuming scraping phase. The process might take several days. But this can greatly vary depending on the state of the storage device.

If the device is not too severely damaged, you might get away with it. In my case, 284 of the ca. 6000 image files were affected by bad or non-scraped memory areas. Probably there are good memory blocks in the non-scraped area, but since we cannot tell until we tried to scrape it, it will be handled as bad memory for now. 

But how can we tell, which files on the storage device are affected by non-recovered data? There is a quite simple approach: 

* compute the MD5 sum for each file and save them in a file
* fill the bad memory areas with different data
* recompute the MD5 sum for each file again
* compare the pre-fill and post-fill MD5 sums to determine possibly damaged files

{%- highlight sh -%}
    sudo losetup --partscan --find --show /rescue.img
{%- endhighlight -%}

{%- highlight sh -%}
    sudo mkdir /sdcard
    sudo mount /dev/loop0p1 /sdcard
{%- endhighlight -%}

{%- highlight sh -%}
        find /sdcard -type f -print0 | xargs -0 md5sum > ~/pre-fill.md5
{%- endhighlight -%}

Repeat for the refilled image

{%- highlight sh -%}
        sudo umount /sdcard
        sudo losetup -d /dev/loop0
{%- endhighlight -%}

{%- highlight sh -%}
        echo -n "BAD DATA" > /tmp/tmpfile
        sudo ddrescue --fill=- /tmp/tmpfile rescue.img rescue.map
{%- endhighlight -%}

{%- highlight sh -%}
        code
{%- endhighlight -%}

Repeat the steps for creating the loop device and mounting it. Compute the new MD5 sum.

{%- highlight sh -%}
    find /sdcard -type f -print0 | xargs -0 md5sum > ~/post-fill.md5
{%- endhighlight -%}

Compare the hashes and count changed files

{%- highlight sh -%}
    diff prefill.md5 postfill.md5 | grep "^>" | wc -l
{%- endhighlight -%}

To extract a file list of these potentially corrupted files execute

{%- highlight sh -%}
    diff prefill.md5 postfill.md5 | grep "^>" | sed -E 's;.* /sdcard/(.*);\1;' > damaged_files.txt
{%- endhighlight -%}