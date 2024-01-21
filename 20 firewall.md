# SSH daemon configuration

## Important folders

## Assignments

You're right! We need to ensure that the rules apply to both IPv4 and IPv6 traffic. Below, I've expanded the configuration to include IPv6 rules using `ip6tables`:

1. **Configure iptables Rules for IPv4 and IPv6**

   - Add specific rules for IPv4 (`iptables`) and IPv6 (`ip6tables`):
     - Allow specific TCP and UDP ports:
       ```bash
       # For IPv4
       sudo iptables -A INPUT -p tcp -m multiport --dports 25,80,443,587,993,22345 -j ACCEPT
       sudo iptables -A INPUT -p udp -m multiport --dports 53 -j ACCEPT
       sudo iptables -A INPUT -p tcp -m multiport --dports 53 -j ACCEPT
       # For IPv6
       sudo ip6tables -A INPUT -p tcp -m multiport --dports 25,80,443,587,993,22345 -j ACCEPT
       sudo ip6tables -A INPUT -p udp -m multiport --dports 53 -j ACCEPT
       sudo ip6tables -A INPUT -p tcp -m multiport --dports 53 -j ACCEPT
       ```
     - Allow ICMP (ping) for IPv4/IPv6:
       ```bash
       # For IPv4
       sudo iptables -A INPUT -p icmp -j ACCEPT
       # For IPv6
       sudo ip6tables -A INPUT -p icmpv6 -j ACCEPT
       ```
     - Allow certain IP ranges (IPv4 only, as these are IPv4 addresses):
       ```bash
       sudo iptables -A INPUT -s 10.0.0.0/8 -j ACCEPT
       sudo iptables -A INPUT -s 192.168.255.0/24 -j ACCEPT
       sudo iptables -A INPUT -s 172.16.0.0/16 -j ACCEPT
       ```
     - Add rules for `yoda` and `leia` (make sure you resolve their IPv6 addresses as well):
       ```bash
       # For IPv4
       sudo iptables -A INPUT -s yoda_ip -j ACCEPT
       sudo iptables -A INPUT -s leia_ip -j ACCEPT
       # For IPv6
       sudo ip6tables -A INPUT -s yoda_ipv6 -j ACCEPT
       sudo ip6tables -A INPUT -s leia_ipv6 -j ACCEPT
       ```
     - Drop all other incoming traffic:
       ```bash
       # For IPv4
       sudo iptables -A INPUT -j DROP
       # For IPv6
       sudo ip6tables -A INPUT -j DROP
       ```

2. **Set Default Policies**

   - Now, set the default policies for both IPv4 and IPv6:
     ```bash
     # For IPv4
     sudo iptables -P INPUT DROP
     sudo iptables -P FORWARD DROP
     sudo iptables -P OUTPUT ACCEPT
     # For IPv6
     sudo ip6tables -P INPUT DROP
     sudo ip6tables -P FORWARD DROP
     sudo ip6tables -P OUTPUT ACCEPT
     ```

3. **Save Rules for Persistence**

   - You'll need to save both IPv4 and IPv6 rules to ensure they persist across reboots:
     ```bash
     sudo apt-get install iptables-persistent
     sudo netfilter-persistent save
     ```

4. **Create Systemd Service (Optional)**
   - If you're creating a systemd service, make sure your script handles both iptables and ip6tables.

This configuration ensures that your rules apply to both IPv4 and IPv6 traffic, providing comprehensive coverage. If you have any more requirements or need further assistance, feel free to ask!

## Handy stuff

lookup ipadress:

    nslookup URL
