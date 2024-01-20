# What is DNS

Domain Name Server

IP-address <=> URI

192.168.0.1 <=> www.google.com

    🅰 Understand the DNS protocol with great detail, including one of the most severe, and talked-about bugs: The Dan Kaminsky DNS Vulnerability.

    🅱 Install and configure an authoritative DNS server for your zone slimme-rik.sasm.uclllabs.be according to the specifications below.

    🅲 Learn how to properly troubleshoot and verify your configuration, so it meets all required specifications.

    🅳 Automate certain DNS configurations like the creation of zones and records using scripts.

## Important folders

    /etc/bind
        /etc/bind/zones     DNS Records
        /etc/bind/named.conf.local      zone record
        /etc/bind/named.conf.options

    /etc/scripts

## assignment

### install server

    sudo apt-get update
    sudo apt-get install bind9

a DNS record for "ferre-verheyen.sasm.ucllabs.be" with the IP address 193.191.176.159:

1. Open the BIND configuration file for editing. `/etc/bind/named.conf.local`.

2. Add a new zone configuration for your domain at the end of the file.:

   ```bind
   zone "ferre-verheyen.sasm.ucllabs.be" {
       type master;
       file "/etc/bind/zones/db.ferre-verheyen.sasm.ucllabs.be";
   };
   ```

3. Create a new zone file for your domain. You can create a file named `db.ferre-verheyen.sasm.ucllabs.be` in the specified directory (e.g., `/etc/bind/zones/`) with the following content:

   ```bind
   $TTL 86400
   @       IN      SOA     ns1.ferre-verheyen.sasm.ucllabs.be. admin.ferre-verheyen.sasm.ucllabs.be. (
                   2024012001      ; Serial
                   3600            ; Refresh
                   1800            ; Retry
                   604800          ; Expire
                   86400           ; Minimum TTL
   )

   ; Name servers
   @       IN      NS      ns1.ferre-verheyen.sasm.ucllabs.be.

   ; Define the IP address
   @       IN      A       193.191.176.159
   ```

4. Save the zone file.

5. Restart the BIND server to apply the changes:
   ```bash
   sudo service bind9 restart
   ```

### It resolves ns.slimme-rik.sasm.uclllabs.be and www.slimme-rik.sasm.uclllabs.be, both to your own IP address.

edit zone config: `/etc/bind/zones/db.ferre-verheyen.sasm.uclllabs.be`

    ; Define the IP address
    www       IN      A       193.191.176.159

### test.slimme-rik.sasm.uclllabs.be resolves to 193.191.177.254.

edit zone config: `/etc/bind/zones/db.ferre-verheyen.sasm.uclllabs.be`

    ; Define the IP address
    test       IN      A       193.191.177.254

### Four nameservers should be authoritative for your domain:

edit zone config: `/etc/bind/zones/ferre-verheyen.sasm.uclllabs.be.db`

    ; Define the nameservers
    @       IN      NS      ns.ferre-verheyen.sasm.uclllabs.be.
    @       IN      NS      ns1.uclllabs.be.
    @       IN      NS      ns2.uclllabs.be.
    @       IN      NS      ns.ruben-toen.sasm.uclllabs.be.

## handy stuff

Test dns record for www.ferre-verheyen.sasm.uclllabs.be

    dig -t www ferre-verheyen.sasm.uclllabs.be +short

Troubleshooting see: https://wiki.uclllabs.be/index.php/SaSM_Lab_dns
