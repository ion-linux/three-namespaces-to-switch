# Three namespaces attached to a switch (Linux bridge)
<br>
CONNECT marketing, legal and sales namespaces
<br>
<br>
<br>

# Create a switch
It will bridge namespaces.

Create a switch where you will plug the cabels in
```
ip link add SWITCH0 type bridge
```

Check the switch created, look for the name SWITCH
```
ip link 
```

Create a L3 interface on the switch
```
ip addr add 192.168.1.1/24 dev SWITCH0
```

Power on the switch
```
ip link set dev SWITCH0 up
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
ip link add eth0-marketing peer name eth0-marketingtoSW
```

Attach one end to the marketing namespace
```
ip link set eth0-marketing netns marketing
```

Attach the other end to the SWITCH notice the master keyword
```
ip link set eth0-marketingtoSW master SWITCH0
```

Configure L3 address on the interface (end of cable) attached to the namespace:
```
ip -n marketing address add 192.168.1.101/24 dev eth0-marketing
```

Bring UP the interface on the namespace
```
ip -n marketing link set eth0-marketing up
```

Bring UP the interface on the host up
```
ip link set eth0-marketingtoSW up
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
ip link add eth0-legal type veth peer name eth0-legaltoSW
```

Attach one end to legal namespace
```
ip link set eth0-legal netns legal
```

Attach the other end to the SWITCH, notice the master keyword
```
ip link set eth0-legaltoSW master SWITCH0
```

Configure L3 address on the interface (end of cable) attached to the namespace:
```
ip -n legal address add 192.168.1.102/24 dev eth0-legal
```

Bring UP the interface on the namespace
```
ip -n legal link set eth0-legal up
```

Bring UP the interface on the host up
```
ip link set eth0-legaltoSW up
```

Route traffic to 192.168.1.0/24 through the SWITCH0 L3 interface
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
ip link add eth0-sales type veth peer name eth0-salestoSW
```

Attach one end to legal namespace
```
ip link set eth0-sales netns sales
```

Attach the other end to the SWITCH0, notice the master keyword
```
ip link set eth0-salestoSW master SWITCH0
```

Configure L3 address on the interface (end of cable) attached to the namespace:
```
ip -n sales address add 192.168.1.103 dev eth0-sales
```

Bring up the interface on the namespace
```
ip -n sales link set eth0-sales up
```

Bring UP the interface on the host up
```
ip link set eth0-salestoSW up
```

Route traffic to 192.168.1.0/24 through the SWITCH L3 interface
```
ip netns exec sales ip route add default via 192.168.1.1
```

