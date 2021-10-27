# Three namespaces attached to a switch (Linux bridge)
<br>
CONNECT marketing, legal, sales namespaces
<br>
<br>
<br>

# Create a switch
bridges namespaces

Create a switch where you will plug the cabels in
```
ip link add SWITCH type bridge
```

Check the switch created, look for the name SWITCH
```
ip link 
```

Create a L3 interface on the switch
```
ip addr add 192.168.1.1/24 dev SWITCH
```

Power on the switch
```
ip link set dev SWITCH up
```

Route traffic to the internet
```
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -j MASQUERADE
```

<br>
<br>

# Create cables
For each namespace marketing, legal and sales prepare a cable
<br>
<br>

## Marketing
Create a namespace
```
ip netns add marketing
```

Create a cable
```
ip link add eth0-marketing peer name eth0-marketing-to-SWITCH
```

Attach one end to the marketing namespace
```
ip link set eth0-marketing netns
```

Attach the other end to the SWITCH notice the master keyword
```
ip link set eth0-marketing-to-SWITCH master SWITCH
```

Configure L3 address on the interface (end of cable) attached to the namespace:
```
ip -n marketing address add 192.168.1.101 dev eth0-marketing
```

Brig up the interface on the namespace
```
ip -n marketing link set eth0-marketing up
```

Route traffic to 192.168.1.0/24 through the SWITCH L3 interface
```
ip netns exec marketing ip route add default via 192.168.1.1
```

<br>
<br>

## Legal
Create a namespace
```
ip netns add legal
```

Create a cable
```
ip link add eth0-legal peer name-eth0-legal-to-SWITCH
```

Attach one end to legal namespace
```
ip link set eth0-legal netns
```

Attach the other end to the SWITCH, notice the master keyword
```
ip link set eth0-legal-to-SWITCH master SWITCH
```

Configure L3 address on the interface (end of cable) attached to the namespace:
```
ip -n legal address add 192.168.1.102 dev eth0-legal
```

Brig up the interface on the namespace
```
ip -n legal link set eth0-legal up
```

Route traffic to 192.168.1.0/24 through the SWITCH L3 interface
```
ip netns exec legal ip route add default via 192.168.1.1
```
<br>
<br>

## Sales
Create a namespace
```
ip netns add sales
```

Create a cable
```
ip link add eth0-sales peer name-eth0-sales-to-SWITCH
```

Attach one end to legal namespace
```
ip link set eth0-sales netns
```

Attach the other end to the SWITCH, notice the master keyword
```
ip link set eth0-sales-to-SWITCH master SWITCH
```

Configure L3 address on the interface (end of cable) attached to the namespace:
```
ip -n sales address add 192.168.1.103 dev eth0-sales
```

Brig up the interface on the namespace
```
ip -n sales link set eth0-sales up
```
Route traffic to 192.168.1.0/24 through the SWITCH L3 interface
```
ip netns exec sales ip route add default via 192.168.1.1
```

