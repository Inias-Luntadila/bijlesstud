# SSH daemon configuration

## Important folders

## Assignments

## Important folders

## Assignments

## Handy stuff

Alright, let's embark on this SMTP server setup journey with a sprinkle of fun! Here’s a brief overview:

1. **Install Postfix & Dovecot**:

   - Postfix handles the SMTP part.
   - Dovecot takes care of the IMAP and stores emails.

2. **Configure Postfix**:

   - Ensure it's not an open relay.
   - Set it to listen on port 25 and identify as mx.ferre-verheyen.sasm.uclllabs.be.
   - Accept mail for the specified users and domains.

3. **Configure Dovecot**:

   - Store emails in /var/vmail/username in maildir format.
   - Handle virtual users.

4. **DNS Records**:
   - Set an MX record and ensure mx.ferre-verheyen.sasm.uclllabs.be points to your server.

### Installation

```bash
sudo apt-get update
sudo apt-get install postfix dovecot-core dovecot-imapd
```

### Postfix Configuration

Edit `/etc/postfix/main.cf`. Add or modify:

```bash
# Restrict relay
mynetworks = 127.0.0.0/8, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

# SMTPD Configuration
smtpd_banner = $myhostname ESMTP mx.ferre-verheyen.sasm.uclllabs.be
inet_interfaces = all
inet_protocols = all
myhostname = mx.ferre-verheyen.sasm.uclllabs.be
mydestination = $myhostname, ferre-verheyen.sasm.uclllabs.be, localhost.localdomain, localhost
local_recipient_maps =

# Maildir and virtual users
virtual_mailbox_domains = ferre-verheyen.sasm.uclllabs.be
virtual_mailbox_base = /var/vmail
virtual_mailbox_maps = hash:/etc/postfix/vmailbox
virtual_minimum_uid = 100
virtual_uid_maps = static:5000
virtual_gid_maps = static:5000
```

Create `/etc/postfix/vmailbox`:

```bash
user1@ferre-verheyen.sasm.uclllabs.be /var/vmail/user1/
user2@ferre-verheyen.sasm.uclllabs.be /var/vmail/user2/
check@ferre-verheyen.sasm.uclllabs.be /var/vmail/check/
```

Update Postfix maps:

```bash
postmap /etc/postfix/vmailbox
```

### Dovecot Configuration

Edit `/etc/dovecot/conf.d/10-mail.conf`:

```bash
mail_location = maildir:/var/vmail/%u
```

Edit `/etc/dovecot/conf.d/10-auth.conf` and ensure:

```bash
!include auth-system.conf.ext
!include auth-passwdfile.conf.ext
```

Create `/etc/dovecot/conf.d/auth-passwdfile.conf.ext`:

```bash
passdb {
  driver = passwd-file
  args = scheme=SHA256-CRYPT username_format=%u /etc/dovecot/users
}

userdb {
  driver = static
  args = uid=5000 gid=5000 home=/var/vmail/%u allow_all_users=yes
}
```

### Add Virtual Users

```bash
mkdir /var/vmail
chown -R 5000:5000 /var/vmail
doveadm pw -s SHA256-CRYPT > /etc/dovecot/users
```

(Enter the password for the user, then add user info in the `/etc/dovecot/users` file like `user1@ferre-verheyen.sasm.uclllabs.be:{SHA256-CRYPT}passwordhash`)

### Restart Services

```bash
service postfix restart
service dovecot restart
```

### DNS Setup

- Set MX record for `ferre-verheyen.sasm.uclllabs.be` to `mx.ferre-verheyen.sasm.uclllabs.be`.
- Make sure `mx.ferre-verheyen.sasm.uclllabs.be` points to your server's IP.

Remember to test and verify at each step. Want a more detailed guide or hit any roadblocks? Just say the word!
