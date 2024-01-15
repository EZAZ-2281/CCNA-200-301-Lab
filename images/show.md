``````
R1#sh run 
R1#sh start 

R1#sh ip int brief
R1#sh ipv6 int brief 
SW1#sh vlan brief
SW1#sh standby brief

SW1#sh int g0/1
SW1#sh int g0/1 switchport
SW1#sh int status 
SW1#sh int trunk
SW1#sh vtp status

R1#sh run | s dhcp
R1#sh run | include ip route
R1#sh run | section interface
R1#sh run | section ospf
R1#sh run | include access-list

R1#sh ip route 
R1#sh ip route rip
R1#sh ip route eigrp 
R1#sh ip route connected 
R1#sh ip route static
R1#sh ipv6 route 
R1#sh ip protocols

R1#sh ip rip database
R1#sh ip ospf database

R1#sh ip eigrp neighbors 
R1#sh ip ospf neighbors 
R1#sh ipv6 neighbors
SW1#sh cdp neighbors

R1#sh ip eigrp topology 
R1#sh ip ospf int f0/0

Acc1#sh et summary
Acc1#sh etherchannel load-balance 

R1#sh dhcp lease
R1#sh ip dhcp binding

SW1#sh mac address-table
SW1#sh port-security
SW1#sh port-sec int f0/1
SW1#sh port-sec address

SW1#sh ip dhcp snooping
SW1#sh ip dhcp snooping binding 
SW1#sh ip dhcp snooping database 

SW1#sh cdp entry R2 
SW1#sh cdp int g0/1 

SW1#sh power inline
R1#sh policy-map 
R1#sh policy-map int s0/2/0

R1#sh clock
R1#sh clock detail
R1#sh ntp status
R1#sh ntp associations

R1#sh standby
SW1#sh spanning-tree
SW1#sh spanning-tree vlan 10

R1#sh ip nat translations 
R1#clear ip nat translation * 

SW1#sh version 
R3#sh flash
R1#sh host
R1#sh logging
R1#sh controllers s0/0/0
R1#sh access-lists 
R1#undebug all
R1#write erase
SW1#reload 
``````