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

NSEC records
NSEC (or NSEC3) records are automatically generated when you sign your DNS zone with DNSSEC. They're not something you create manually; instead, the DNSSEC tools (like `dnssec-signzone` for BIND) handle their creation. Here's a bit about their creation, location, and verification:

1. **Creation**:

   - When you sign a zone with `dnssec-signzone`, it automatically creates NSEC or NSEC3 records for each name in the zone, linking them together in a chain (or creating hashed records in the case of NSEC3).
   - The choice between NSEC and NSEC3 can depend on your security requirements and concerns about zone walking (enumerating all names in a zone).

2. **Location**:

   - NSEC and NSEC3 records reside within the zone file itself, just like any other DNS records (A, MX, TXT, etc.).
   - After signing the zone, if you inspect your signed zone file (e.g., `example.com.zone.signed`), you'll find NSEC or NSEC3 records listed alongside your other DNS records.

3. **Verification**:
   - **Using `dig`**: You can use the `dig` command with the `+dnssec` flag to query your DNS records. This won't directly show you NSEC/NSEC3 records, but you'll see a `RRSIG` signature for each record, indicating it's secured by DNSSEC.
     ```bash
     dig +dnssec example.com
     ```
   - **Testing Non-Existence**: To specifically see NSEC/NSEC3 in action, you can query a non-existent subdomain. You should receive an NSEC/NSEC3 record in the response, proving the non-existence of the queried name.
   - **Online Tools**: Tools like Verisign's DNSSEC Analyzer (dnssec-analyzer.verisignlabs.com) can visualize your domain's DNSSEC status, including the presence and validity of NSEC/NSEC3 records.

It's important to regularly ensure that your DNSSEC, including NSEC/NSEC3 records, is functioning correctly. Regularly checking the signatures and the overall health of your DNSSEC setup helps maintain the security and integrity of your domain.
