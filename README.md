# Kioptix-Level-1

lets first descover the network to find our victim machine

netdiscover -r 192.168.1.0/24


We find it! Now lets scan for ports and versions

nmap -sS -T4 -p- -A 192.168.1.104

