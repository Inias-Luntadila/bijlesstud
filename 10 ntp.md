# NTP

Network Time Protocol (NTP)
NTP (Network Time Protocol) is used to synchronize the clocks of networked computers to a few milliseconds of Coordinated Universal Time (UTC)

    🅰 Understand how NTP works (high level understanding is sufficient) and why it is used.

    🅱 Learn how to properly configure and secure an NTP time server.

    🅲 Configure your ntpd daemon (the server) to reflect the specifications outlined below.

    Setup an NTP server and use it to keep your local clock synchronized with a time server on the internet.

    - Use be.pool.ntp.org.

    - Drop all queries coming from the internet.

    - Only yoda.uclllabs.be and one other specific server of your choice (except leia.uclllabs.be) should be able to perform time queries, both via IPv4 and IPv6.

## Important folders

    /etc/ntp.conf

## Assignments

### 🅰 Understand how NTP works (high level understanding is sufficient) and why it is used.

    - NTP (Network Time Protocol) is used to synchronize the clocks of networked computers to a few milliseconds of Coordinated Universal Time (UTC).

    - It uses a hierarchical, semi-layered system of time sources. The layers are termed strata, where stratum 0 are high-precision timekeeping devices (like atomic clocks), stratum 1 are computers directly connected to stratum 0 devices, and so on.

    - It's crucial for tasks that need precise timekeeping (e.g., financial transactions, logging, and various system functions).

### 🅱 Learn how to properly configure and secure an NTP time server.

    sudo apt-get install ntp

    sudo nano /etc/ntp.conf
        server be.pool.ntp.org iburst

    sudo systemctl restart ntp

### 🅲 Configure your ntpd daemon (the server) restrict queries.

drop all queries and only allow certain servers

    sudo nano /etc/ntp.conf
        restrict 127.0.0.1
        restrict ::1
        restrict yoda.uclllabs.be nomodify notrap noquery
        restrict example.ntp.server nomodify notrap noquery
        restrict -6 yoda.uclllabs.be nomodify notrap noquery
        restrict -6 example.ntp.server nomodify notrap noquery
        restrict default ignore

    sudo systemctl restart ntp

## Handy stuff

Verify

    ntpq -p
