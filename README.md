# PiHole-config
The program configuration for filtering network packets, i.e. Iptables, is shown below (*/etc/iptables.config*).

```
*filter
# Allow all loopback (lo0) traffic
-A INPUT -i lo -j ACCEPT
# Accept all established inbound connections
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
# Allow all outbound traffic
-A OUTPUT -j ACCEPT
# Allow HTTP and HTTPS connections
-A INPUT -p tcp -m multiport --dports 80,443 -j ACCEPT
# Allow SSH connections
-A INPUT -p tcp -m state --state NEW --dport 30623 -j ACCEPT
# Specific for Pi-Hole
-A INPUT -p tcp --dport 53 -j ACCEPT
-A INPUT -p udp --dport 53 -j ACCEPT
-A INPUT -p tcp --dport 80 -j ACCEPT
-A INPUT -p udp --dport 80 -j ACCEPT
# Drop all other inbound - default deny unless explicitly allowed policy
-A INPUT -j DROP
-A FORWARD -j DROP
COMMIT
```

To set filters in the Iptables after rebooting, please follow the instructions below.

```
sudo nano /etc/network/if-pre-up.d/iptables-config-load.sh
        #!/bin/sh
        /sbin/iptables-restore < /etc/iptables.config
sudo chmod +x /etc/network/if-pre-up.d/iptables-config-load.sh
sudo iptables-restore < /etc/iptables.config
sudo iptables-save 
```

Pi-Hole will not intercept packages from mobile systems, e.g. Android. To solve this the VPN server configuration on RPi is needed.
