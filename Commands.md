### VLAN 
#### Router: 
```
int g0/0.5
encapsulation dot1Q 5
ip add 10.200.48.1 255.255.254.0
no shut 
exit
```
#### Switch:
```
vlan 5
name Production
vlan 10
name Engineering
vlan 15
name Admin
vlan 20
name Staff
vlan 25 
name DeviceManager
vlan 888
name Native
vlan 999
name Blackhole
exit

int range f0/5-10
switchport mode access 
switchport access vlan 5
no shut
exit

int g0/1 
switchport mode trunk 
switchport nonegotiate
switchport trunk native vlan 888
switchport trunk allowed vlan 5,10,15,20,25,888
no shut 
exit


```
### HSRP 
Router 
```
standby 15(Group) ip (Standby ip/ip of the virtual router)
standby priority 110 (chooses what router is picked)
standby preempt (cause a router election to happen again )
```
switch
``` 
just enter the correct standby ip for the default gateway
```
### PortSecurity 
```
switch(config-if)# switchport port-security
switch(config-if)# switchport port-security maximum <max-mac-addresses>
switchport port-security violation 
	restrict drops packets and creates sys log message
	protect just drops them 
	shutdown shuts down the interface 
switch(config-if)# switchport port-security aging time <seconds>
switchport port-security mac-address sticky 
	saves the mac addresses 
	

```


### DHCP 
```
DHCPv4
///DHCP config commands3
ip dhcp excluded-address 192.168.1.1 192.168.10.9
ip dhcp excluded-address 192.168.10.254
ip dhcp pool LAN-POOL-1
network 192.168.10.0 255.255.255.255.0 
default-router 192.168.10.1
dns-server 192.168.11.5
domain-name example.com
end 
lease

/// verification commands
show running-config | section dhcp
show ip dhcp binding
show ip dhcp server statistics 

/// Forwarding broadcast requests 
int g0/0/0
  ip helper-address 192.168.11.6
  end 
  show ip int g0/0/0


DHCPv6

ipv6 unicast-routing
ipv6 dhcp pool IPV6-STATELESS
dns-server 2001:db8:acad:1::254
domain-name example.com
exit

int g0/0/1
ipv6 add 2001:db8:face:20ee::1/64
ipv6 add fe80::12 link-local
ipv6 nd other-config-flag
ipv6 dhcp server IPV6-STATELESS
no shut
end


STATEFUL

Server
ipv6 unicast-routing
ipv6 dhcp pool IPV6-STATEFUL
address prefix 2001:db8:acad:1::/64
dns-server 2001:4860:4860::8888
domain-name example.com

int g0/0/1
ipv6 add 2001:db8:face:20ee::1/64
ipv6 add fe80::12 link-local
ipv6 nd managed-config-flag
ipv6 dhcp server IPV6-STATEFUL



SLAAC ONLY 
```
### Etherchannel 
```
int range f0/3-4
channel-group 1 mode active
exit 
int port-channel 1 
switchport mode trunk
switchport nonegotiate
switchport trunk native vlan 888
switchport trunk allowed vlan 5,10,15,20,25,888

exit



for the mode there is PagP and LACP

Pagp: 
- On - This mode forces the interface to channel without PAgP. 

- PAgP desirable - This PAgP mode places an interface in an active negotiating state in which the interface initiates negotiations with other interfaces by sending PAgP packets.

- PAgP auto - This PAgP mode places an interface in a passive negotiating state in which the interface responds to the PAgP packets that it receives but does not initiate PAgP negotiation.

LACP:
- On - This mode forces the interface to channel without LACP. 

-active - This LACP mode places a port in an active negotiating state.

- LACP passive - This LACP mode places a port in a passive negotiating state.

```
### SSH 
```
ip ssh version 2 
ip domain-name capstone.com
crypto key generate rsa 
1024
username admin secret cisco12345
line vty 0 15 
login local 
transport input ssh 
exit
```
### IP ROUTING
Default routes
ipv6 route ::/0 g0/1
ip route 0.0.0.0 0.0.0.0 g0/1

Recursive is nexthop IP
Direct is the interface
