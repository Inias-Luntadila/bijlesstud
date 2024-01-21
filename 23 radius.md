# SSH daemon configuration

## Important folders

## Assignments

Sure, let's get your FreeRADIUS server up and quirky! Here's a short version:

1. Install FreeRADIUS.
2. Set it to start on boot.
3. Configure user with password.
4. Enable control socket for specific user debugging.

Ready for the magic spells (a.k.a detailed steps)? Let's go! 🧙‍♂️✨

### 1. Install FreeRADIUS

```bash
sudo apt-get update
sudo apt-get install freeradius freeradius-utils
```

### 2. Start on Boot

```bash
sudo systemctl enable freeradius
```

### 3. Configure User with Password

Add the user in `/etc/freeradius/3.0/users`:

```
check Cleartext-Password := "Ch3ck"
```

### 4. Enable Control Socket & Debug for User Check

In `/etc/freeradius/3.0/radiusd.conf`, enable the control-socket:

```shell
control-socket {
    path = "/var/run/radiusd/radiusd.sock"
    mode = rw
    user = radiusd
    group = radiusd
}
```

For debugging specific user, you might need to get a bit creative. FreeRADIUS doesn't support user-specific debugging out of the box. However, you can:

- Use `raddebug` for debugging with filters.
- Write a script to monitor the log and extract relevant entries for the user "check".

### Quick Checks

- Restart the server: `sudo systemctl restart freeradius`
- Check status: `sudo systemctl status freeradius`

How's that? Need more sparkle or are we shining bright? ✨

## Handy stuff
