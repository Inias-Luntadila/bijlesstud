# sudo visudo

    1. Install the package sudo on your machine.

    2. Also install the package monitoring-plugins-basic (provides /usr/lib/nagios/plugins/check_apt) and net-tools (provides /usr/sbin/arp).

    3. Let the user check execute the command /usr/lib/nagios/plugins/check_apt as root via sudo without password.

    4. Let the user check execute the command /usr/sbin/arp as root via sudo without password.

## Important folders

## Assignments

### 1. Install the package sudo on your machine.

    sudo apt-get update
    sudo apt-get install sudo

### 2. Also install the package monitoring-plugins-basic and net-tools.

    sudo apt-get install monitoring-plugins-basic net-tools

### 3. Let the user check execute the command /usr/lib/nagios/plugins/check_apt as root via sudo without password.

    sudo visudo
    check ALL=(ALL) NOPASSWD: /usr/lib/nagios/plugins/check_apt
    check ALL=(ALL) NOPASSWD: /usr/sbin/arp

## Handy stuff
