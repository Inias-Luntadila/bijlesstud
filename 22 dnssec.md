# SSH daemon configuration

## Important folders

## Assignments

Setting up DNSSEC on a BIND server involves a few specific commands and configuration file edits. Here's a more detailed breakdown:

### 1. Generate Keys:

As before, you generate a Zone Signing Key (ZSK) and a Key Signing Key (KSK) for your zone.

```bash
dnssec-keygen -a RSASHA256 -b 2048 -n ZONE example.com
dnssec-keygen -a RSASHA256 -b 2048 -n ZONE -f KSK example.com
```

### 2. Sign the Zone:

Use `dnssec-signzone` to sign your zone file (`example.com.zone`), referencing your ZSK and KSK.

```bash
dnssec-signzone -o example.com -k Kexample.com.+007+12345.key example.com.zone Kexample.com.+007+67890.key
```

This will create a new file, like `example.com.zone.signed`.

### 3. Update BIND Configuration:

Edit your BIND configuration file (`named.conf` or `named.conf.local`, depending on your setup).

```bash
zone "example.com" IN {
    type master;
    file "/etc/bind/zones/example.com.zone.signed";  // Update this line
    // ... other configuration ...
};
```

### 4. Reload BIND Configuration:

After updating the configuration, reload BIND to apply the changes.

```bash
rndc reload
```

or

```bash
systemctl reload named
```

### 5. Verify:

Ensure there are no errors in your BIND logs (typically `/var/log/syslog` or `/var/log/named/named.log`), and test your DNSSEC setup using `dig` or an online DNSSEC validator.

### Additional Notes:

- **File Paths**: Ensure your file paths in the BIND configuration match where your keys and signed zone files are actually located.
- **Permissions**: Ensure that the user running BIND has read access to the keys and signed zone files.
- **DNSSEC Maintenance**: Remember, DNSSEC requires ongoing maintenance. Regularly check the validity of your signatures, and plan for key rollovers.
- **Backups**: Always keep backups of your keys in a secure location.

Setting up DNSSEC on a BIND server is indeed straightforward if you follow the steps carefully, but it requires attention to detail and ongoing maintenance. Once set up, it significantly increases the security of your DNS infrastructure. 🛡️✨

## Handy stuff
