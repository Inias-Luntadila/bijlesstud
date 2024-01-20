# ARP

ARP (Address Resolution Protocol)

    1. ARP Request Broadcast: It shouts into the network, "Who owns this IP address?"

    2. Receiving the Call: Devices hear the shout, but only the one with the matching IP responds.

    3. ARP Reply: The chosen device whispers back its MAC address, "It's me, here's my secret code."

    4. Update the ARP Table: The asker memorizes this in its ARP table, like a spy noting a secret in their diary.

a

    🅰 Learn how static ARP entries can be used to mitigate ARP spoofing attacks.

## Important folders

## Assignments

### 🅰 Learn how static ARP entries can be used to mitigate ARP spoofing attacks.

On Ubuntu, setting a static gateway is like carefully choosing your path in a dungeon. Here's the incantation:

1. **Find Your Current Gateway**:
   Use the `ip route` or `route -n` command in the terminal. The default gateway is usually listed as the destination for the `default` route.

   ```bash
   ip route | grep default
   ```

2. **Set a Static Gateway**:
   You'll edit the Netplan configuration files, usually found in `/etc/netplan/`. These files may have different names on your system. Here's how you proceed:

   - Open the terminal and use your favorite text editor (like `nano` or `vim`) to edit the file. For example:

     ```bash
     sudo nano /etc/netplan/01-netcfg.yaml
     ```

   - You'll see network interface configurations. It might look complex, but focus on finding the right section for your network interface (like `eth0` for Ethernet or `wlan0` for Wi-Fi).

   - Add or modify the `gateway4` line under your network interface to set your new static gateway. It might look something like this:

     ```yaml
     network:
       version: 2
       renderer: networkd
       ethernets:
         eth0:
           dhcp4: no
           addresses: [192.168.1.10/24]
           gateway4: 192.168.1.1
           nameservers:
             addresses: [8.8.8.8, 8.8.4.4]
     ```

   - Save the file and exit the text editor.

   - Apply the changes by running:

     ```bash
     sudo netplan apply
     ```

   - If you face any issues, you can always revert back or try `sudo netplan try`, which will revert changes automatically if you don't confirm them within a certain timeframe.

Tread carefully, young wizard. One wrong symbol and your network connection might take a short vacation. Ready for more networking spells or have you mastered this arcane art? 🧙‍♂️✨

## Handy stuff
