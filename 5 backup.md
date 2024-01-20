# Automatic on-change backups

## Important folders

    /etc/scripts/backup.sh

    /etc/scripts/incron_config.sh

## Assignments

### 1. Configure the correct locale settings for your server.

#### use inotify to create automatic on-change backup

First, we need a script which will create a backup of the requested files:

    FILE /etc/scripts/backup.sh actual backup script

    #!/bin/bash

    mkdir -p /var/backups/inotify
    if echo $1 | grep -qP "\..*\.sw(p|x|px)$" ; then #for vim users:
            exit 0
    fi
    cp -p --parents $1  /var/backups/inotify
    mv /var/backups/inotify$1 /var/backups/inotify$1_$(date +'%Y.%m.%d_%H:%M:%S')

We will use incron to launch our backup script automatically when needed.

incron needs a list '/etc' in this case.

    FILE /etc/scripts/incron_config.shscript to configure incron

    #!/bin/bash
    find /etc -type d ! -regex '.*/.git.*' -print0 | xargs -0 -I{} echo "{} IN_CLOSE_WRITE,recursive=false /etc/scripts/backup.sh \$@/\$#" > /etc/incron.d/etc.conf
    systemctl restart incron

Incron configuration script runs severy 15 minutes.

    crontab -e

    1. The `backup.sh` script creates backups of files in `/etc`.

    2. The `incron_config.sh` script dynamically updates incron's configuration to monitor directories under `/etc`, excluding `.git` directories.

    3. incron watches for file changes in the specified directories and triggers the backup script when a file is closed after writing.

# Automatic offline (remote) backups

## Important folders

## Assignments

### 1. Configure the correct locale settings for your server.

## Handy stuff
