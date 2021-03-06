---
layout: single
title:  "Backup Script for my Nextcloud"
excerpt: "A script to create a backup of my Nextcloud instance. Mainly to be prepared in case of an update going wrong."
date:   2022-04-13
# last_modified_at: "{% last_modified_at %}"
categories: Software
tags: ["software", "server"]
toc: true
author: Thies Lennart Alff
# header:
#     overlay_image: /assets/images/ov9281_b0165.jpg
#     overlay_filter: 0.5

---

## Backup creation via interactive script

The script I use to create the Nextcloud backup is the following:

{% highlight sh %}
#!/usr/bin/bash
set -e

DATE_STR=`date +"%Y-%m-%d"`
BACKUP_DIR="/backup/nextcloud-backup/$DATE_STR"

sudo -u www-data php /var/www/nextcloud/occ maintenance:mode --on

mkdir $BACKUP_DIR
echo "Enter mysql_nextcloud password."
mysqldump --single-transaction -u nextcloud -p nextcloud > "$BACKUP_DIR/nextcloud-sqlbkp".bak
rsync -Aax --info=progress2 /var/www/nextcloud/ "$BACKUP_DIR/nextcloud-dirbkp"

sudo -u www-data php /var/www/nextcloud/occ maintenance:mode --off

{% endhighlight %}

As one can clearly see, it is an interactive script that I invoke when updating my cloud. It performs the following steps:

* Enabling maintenance mode
* Backing up the database
* Backing up the Nextcloud data
* Disabling maintenance mode

The backups are stored in directories named by their date of creation.

## What could be done better?

* Incremental backups to save disk space
* Make the backup completely automated and perform them on a regular time base
* Store the backups not only on a separate drive but on a different computer