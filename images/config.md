Configuration command: 
## **VLAN**
```
R1(config)#int f0/0
R1(config-if)#ip add 10.10.10.1 255.255.255.0
R1(config-if)#no shut
R1(config-if)#shut

R1(config)#int f0/0
R1(config-if)#no ip address

SW1(config)#vlan 10
SW1(config-vlan)#name Eng

SW1(config)#int vlan 10
SW1(config-if)#ip address 10.10.10.1 255.255.255.0 
SW1(config-if)#no shut
SW1(config)#no vlan 10

SW1(config)#int range f0/1-2
SW1(config-if-range)#sw mode access
SW1(config-if-range)#sw acc vlan 10

SW1(config)#int g0/1
SW1(config-if)#switchport trunk encap dot1q
SW1(config-if)#switchport mode trunk 
SW1(config-if)#switchport trunk allowed vlan 10,30
SW1(config-if)#sw trunk native vlan 199

SW1(config-if)#switchport nonegotiate 

SW1(config)#vtp domain ezaz
SW1(config)#vtp mode server {client | transparent}

R1(config-subif)#int f0/0.20
R1(config-subif)#encap dot1q 20
R1(config-subif)#encapsulation dot1q 20 native 
R1(config-subif)#ip add 10.10.20.1 255.255.255.0
R1(config-subif)#no shut

R1(config)#no int g0/0.20

SW1(config)#ip routing
SW1(config)#no ip routing 
S1(config)#ip default-gateway 10.1.1.254

SW1(config)#int g1/0/2
SW1(config-if)#no switchport
SW1(config-if)#ip address 10.0.0.193 255.255.255.252

SW1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.194
```
## **Etherchannel**
```
Acc3(config)#int range f0/23-24
Acc3(config-if-range)#channel-group 1 mode active {desirable | on}
Acc3(config-if-range)#exit
Acc3(config)#int port-channel 1
Acc3(config-if)#switchport mode trunk 
Acc3(config-if)#switchport trunk native vlan 199
Acc3(config-if)#description Link to CD1

Switch1(config)#int range g1/0/1-2
Switch1(config-if-range)#no switchport
Switch1(config-if-range)#channel-group 1 mode active
Switch1(config-if-range)#int po 1
Switch1(config-if)#ip add 192.168.0.1 255.255.255.252 
Switch1(config-if)#no shut

ASW1(config)#port-channel load-balance src-dst-ip
```
## **DHCP**
```
R1(config)#int f0/0
R1(config-if)#no shut
R1(config-if)#ip address dhcp

R1(config)#ip dhcp excluded-address 10.10.10.1 10.10.10.10 
R1(config)#ip dhcp pool kibria
R1(dhcp-config)#network 10.10.10.0 255.255.255.0
R1(dhcp-config)#default-router 10.10.10.1
R1(dhcp-config)#dns-server 10.10.20.10
R2(dhcp-config)#domain-name kibria.com 

R1(config)#no ip dhcp excluded-address 10.10.10.1 10.10.10.10 
R1(config)#no ip dhcp pool kibria 

R1(config)#int f0/1 {vlan 10}
R1(config-if)#ip helper-address 10.10.20.10
```
## **Port Security**
```
SW1(config)#int f0/1
SW1(config-if)#switchport mode access
SW1(config-if)#switchport port-security 
SW1(config-if)#switchport port-security maximum 2
SW1(config-if)#switchport port-security mac-address 0000.1111.1111
SW1(config-if)#switchport port-security mac-address sticky
SW1(config-if-range)#switchport port-security aging time 60

SW1(config-if)#int g1/0/3
SW1(config-if)#no switchport port-security mac-address sticky 00C0.3333.3333
SW1(config-if)#shut
SW1(config-if)#no shut

SW1(config)#int g1/0/4
SW1(config-if)#switchport mode access
SW1(config-if)#switchport port-security 
SW1(config-if)#switchport port-security violation restrict {protect | shutdown}
SW1(config-if)#switchport port-security mac-address 00C0.5555.5555
```
## **DHCP Snooping**
```
S1#conf t
S1(config)#ip dhcp snooping 
S1(config)#ip dhcp snooping vlan 1
S1(config)#no ip dhcp snooping information option 
S1(config)#int f0/2
S1(config-if)#ip dhcp snooping trust
```
## **Interface Configuration**
```
Router(config)#hostname R1

SW1(config)#int f0/2
SW1(config-if)#speed 100 {auto} 
SW1(config-if)#duplex full {half | auto}

R1(config)#int f0/0
R1(config)#cdp run
R1(config-if)#no cdp run

R1#copy running-config startup-config 
SW1#write memory 
SW2#write

SW1(config)#no cdp run
SW1(config)#lldp run
SW1(config)#int g0/1
SW1(config-if)#lldp transmit
SW1(config-if)#lldp receive

R1(config)#enable password ccna
R1(config)#service password-encryption
R1(config)#enable secret ccna
```
## **QoS**
```
R1(config)#class-map voice 
R1(config-cmap)#match protocol rtp
R1(config-cmap)#class-map http
R1(config-cmap)#match protocol http
R1(config-cmap)#class-map icmp 
R1(config-cmap)#match protocol icmp 

R1(config)#policy-map mask 
R1(config-pmap)#class voice 
R1(config-pmap-c)#set ip dscp ef
R1(config-pmap-c)#priority 100 {priority percent 10}
R1(config-pmap-c)#class http 
R1(config-pmap-c)#set ip dscp af31
R1(config-pmap-c)#bandwidth 50
R1(config-pmap-c)#class icmp 
R1(config-pmap-c)#set ip dscp af11
R1(config-pmap-c)#bandwidth 25 {bandwidth percent 5}

R1(config)#int s0/1/0
R1(config-if)#service-policy output mask
----------------------------------------
R2(config)#class-map voice 
R2(config-cmap)#match ip dscp ef
R2(config-cmap)#class-map http
R2(config-cmap)#match ip dscp af31
R2(config-cmap)#class-map icmp 
R2(config-cmap)#match ip dscp af11

R2(config)#policy-map remask
R2(config-pmap)#class voice 
R2(config-pmap-c)#set precedence 5
R2(config-pmap-c)#class http 
R2(config-pmap-c)#set precedence 3
R2(config-pmap-c)#class icmp 
R2(config-pmap-c)#set precedence 0

R2(config)#int s0/2/0
R2(config-if)#service-policy input remask 
```
## **DAI**
```
SW1(config)#ip arp inspection 
% Incomplete command.
SW1(config)#ip arp inspection vlan 1 
SW1(config)#ip arp inspection validate dst-mac ip src-mac 
SW1(config)#int range g0/1-2 
SW1(config-if-range)#ip arp inspection trust 
```
## **DNS**
```
R1(config)#ip domain-lookup 
R1(config)#ip name-server 1.1.1.1
R1(config)#ip host R1 192.168.0.254
R1(config)#ip host PC1 192.168.0.1
R1(config)#ip host PC2 192.168.0.2
R1(config)#ip host PC3 192.168.0.3
```
## **SSH SYSLOG**
```
R1(config)#line console 0
R1(config-line)#logging synchronous 
R1(config-line)#exec-timeout 15

R1(config)#line console 0 
R1(config-line)#login
% Login disabled on line 0, until 'password' is set 
R1(config-line)#password Flackbox5

SW2(config)#line console 0 
SW2(config-line)#login local 
SW2(config-line)#exec-timeout 5
------------------------------------------------
R1(config)#line vty 0 15
R1(config-line)#logging synchronous 
R1(config-line)#exec-timeout 15

R1(config)#access-list 1 permit host 10.0.0.10
R1(config)#line vty 0 15 
R1(config-line)#login
R1(config-line)#password Flackbox3 
R1(config-line)#access-class 1 in

SW2(config)#access-list 1 permit host 192.168.1.1 
SW2(config)#line vty 0 15
SW2(config-line)#login local 
SW2(config-line)#exec-timeout 5
SW2(config-line)#transport input ssh 
SW2(config-line)#access-class 1 in 

R1(config)#username admin secret Flackbox4 
R1(config)#line vty 0 15
R1(config-line)#login local

R1(config)#line vty 0 15
R1(config-line)#transport input ssh 
R1(config-line)#exit
R1(config)#ip ssh version 2

R1(config)#ip domain-name flackbox.com 
R1(config)#crypto key generate rsa
------------------------------------------------
C:\> ssh –l admin 10.0.0.1
R1(config)#banner login "Authorised users only"
R1(config)#service timestamps log datetime msec

R1#terminal monitor

R1(config)#logging host 192.168.1.100
R1(config)#logging trap debugging 

R1(config)#logging buffer 8192
```
## **NTP**
```
R2#clock set 12:00:00 Dec 15 2023

R1(config)#clock timezone PST -6
R1(config)#ntp server 10.0.1.100

R1(config)#ntp master
R1(config)#ntp authenticate
R1(config)#ntp authentication-key 1 md5 kibria	
R1(config)#ntp trusted-key 1

R1(config)#ntp update-calendar 
```
## **Static Route** 
```
R1(config)#ip route 10.1.0.0 255.255.255.0 10.0.0.2

R1(config)#no ip route 10.1.0.0 255.255.255.0 10.0.0.2

R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
```
## **RIP EIGRP** 
```
R1(config)#router rip
R1(config-router)#version 2
R1(config-router)#no auto-summary
R1(config-router)#network 10.0.0.0

R1(config)#router eigrp 100
R1(config-router)#no auto-summary
R1(config-router)#network 10.0.0.0 0.255.255.255

R5(config)#no router rip 
R5(config)#no router eigrp 100

R1(config)#int loopback 0
R1(config-if)#ip address 192.168.0.1 255.255.255.255

R1(config)#router eigrp 1 
R1(config-router)#no auto-summary
R1(config-router)#network 10.0.12.0 0.0.0.255
R1(config-router)#network 10.0.13.0 0.0.0.255
R1(config-router)#network 1.1.1.1 0.0.0.0
R1(config-router)#passive-interface loopback 0
R1(config-router)#passive-interface fastethernet1/1

R1(config)#router eigrp 100
R1(config-router)#redistribute static metric 10000 1000 255 1 1500
```
## **OSPF**
```
R1(config)#router ospf 1
R1(config-router)#network 10.0.0.0 0.255.255.255 area 0

R1(config)#router ospf 1
R1(config-router)#auto-cost reference-bandwidth 100000

R1(config)#int f1/1
R1(config-if)#ip ospf cost 1500

R4(config)#ip route 0.0.0.0 0.0.0.0 203.0.113.2
R4(config)#router ospf 1
R4(config-router)#default-information originate

R5(config)#router ospf 1
R5(config-router)#no network 10.0.0.0 0.255.255.255 area 0

R2(config)#router ospf 1
R2(config-router)#area 0 range 10.1.0.0 255.255.0.0
R2(config-router)#area 1 range 10.0.0.0 255.255.0.0

R6(config)#interface FastEthernet0/0 
R6(config-if)#ip ospf priority 100 
R6(config-if)#end
R6#clear ip ospf process

R1(config)#int range f1/0, g0/0, g3/0
R1(config-if-range)#ip ospf 1 area 0
R1(config)#router ospf 1
R1(config-router)#passive-interface l0

R4(config)#int range f1/0, f2/0, g0/0
R4(config-if-range)#ip ospf 1 area 0
R4(config)#router ospf 1
R4(config-router)#passive-interface default
R4(config-router)#no passive-interface f1/0
R4(config-router)#no passive-interface f2/0

R1(config)#int s0/0/0
R1(config-if)#clock rate 128000
R1(config-if)#no shut

R3(config)#int g0/1
R3(config-if)#no ip ospf network point-to-point

R5(config)#int g0/0
R5(config-if)#no ip ospf hello-interval
R5(config-if)#no ip ospf dead-interval
```
## **HSRP**
```
R1(config)#int g0/1
R1(config-if)#standby 1 ip 10.10.10.1
R2(config-if)#standby 1 priority 110
R2(config-if)#standby 1 preempt

C:\>arp -a

DSW1(config)#int vlan 10
DSW1(config-if)#standby version 2
```
## **Access Control List (ACL)**
```
R1(config)#access-list 1 permit 10.0.1.0 0.0.0.255
R1(config)#access-list 1 deny 10.0.2.0 0.0.0.255
R1(config)#int f0/0
R1(config-if)#ip access-group 1 out 

R1(config)#access-list 100 permit tcp 10.0.1.10 0.0.0.0 10.0.0.2 0.0.0.0 eq telnet 
R1(config)#access-list 100 deny tcp 10.0.1.0 0.0.0.255 10.0.0.2 0.0.0.0 eq telnet 
R1(config)#access-list 100 permit ip any any 
R1(config)#int f1/0
R1(config-if)#ip access-group 100 in 

R1(config)#int f1/0
R1(config-if)#no ip access-group 100 in 
----------------------------------------------------------------------------------
R1(config)#ip access-list extended 111
R1(config-ext-nacl)#permit tcp 10.0.1.10 0.0.0.0 10.0.0.2 0.0.0.0 eq telnet
R1(config-ext-nacl)#deny tcp 10.0.1.0 0.0.0.255 10.0.0.2 0.0.0.0 eq telnet 
R1(config-ext-nacl)#permit icmp 10.0.1.11 0.0.0.0 10.0.0.2 0.0.0.0 echo
R1(config-ext-nacl)#deny icmp 10.0.1.0 0.0.0.255 10.0.0.2 0.0.0.0 echo
R1(config-ext-nacl)#permit ip any any 
R1(config-ext-nacl)#exit
R1(config)#int f1/0
R1(config-if)#ip access-group 111 in 

R1(config)#ip access-list extended 111
R1(config-ext-nacl)#no 30
R1(config-ext-nacl)#60 permit ip any any 

R2(config)#ip access-list standard one
R2(config-std-nacl)#permit 172.16.1.1 
R2(config-std-nacl)#permit 172.16.2.1
R2(config-std-nacl)#deny any
R2(config-std-nacl)#exit
R2(config)#int g0/0
R2(config-if)#ip access-group one out

R1(config)#ip access-list extended 100
R1(config-ext-nacl)#deny tcp 172.16.1.0 0.0.0.255 192.168.1.100 0.0.0.0 eq 53
R1(config-ext-nacl)#deny udp 172.16.1.0 0.0.0.255 192.168.1.100 0.0.0.0 eq 53 
R1(config-ext-nacl)#permit ip any any 
R1(config-ext-nacl)#exit
R1(config)#int g0/0
R1(config-if)#ip access-group 100 in 
```
## **NAT**
```
R1(config)#int f0/1
R1(config-if)#ip nat inside 
R1(config-if)#int f0/0
R1(config-if)#ip nat outside
R1(config-if)#exit
R1(config)#ip nat inside source static 10.0.1.10 203.0.113.3

R1(config)#int f1/0
R1(config-if)#ip nat inside
R1(config-if)#int f0/0
R1(config-if)#ip nat outside
R1(config-if)#exit
R1(config)#access-list 1 permit 10.0.2.0 0.0.0.255
R1(config)#ip nat pool POOL1 203.0.113.4 203.0.113.12 netmask 255.255.255.240
R1(config)#ip nat inside source list 1 pool POOL1
R1(config)#ip nat inside source list 1 pool Flackbox overload

R1(config)#int f0/0 
R1(config-if)#shutdown 
R1(config-if)#no ip address
R1(config-if)#ip address dhcp

R1(config)#int f1/0
R1(config-if)#ip nat inside
R1(config-if)#int f0/0
R1(config-if)#ip nat outside
R1(config)#access-list 1 permit 10.0.2.0 0.0.0.255
R1(config)#ip nat inside source list 1 int f0/0 overload
```
## **IPv6**
```
R1(config)#int f0/1
R1(config-if)#ipv6 address 2001:db8:0:0::1/64
R1(config-if)#no shut

R1(config)#int f0/0
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#no shut

R1(config)#ipv6 unicast-routing 

R1(config)#ipv6 route 2001:db8:0:2::/64 2001:db8:0:1::2

R1(config)#int g0/0
R1(config-if)#ipv6 enable
```
## **STP** 
```
CD1(config)#spanning-tree vlan 10 root primary
CD1(config)#spanning-tree vlan 10 root secondary

Acc3(config)#int f0/1
Acc3(config-if)#spanning-tree portfast 
Acc3(config-if)#spanning-tree bpduguard enable

SW4(config)#int f0/2
SW4(config-if)#spanning-tree vlan 1 cost 100

SW1(config)#int f0/1
SW1(config-if)#spanning-tree vlan 1 port-priority 240

SW1(config)#int range f0/2-3	
SW1(config-if-range)#spanning-tree link-type shared {point-to-point}
```
## **SNMP**
```
R1(config)#snmp-server community Flackbox1 ro 
R1(config)#snmp-server community Flackbox2 rw

R1(config)#logging host 10.0.0.100 
R1(config)#logging trap debugging
```
## **Troubleshooting and Recovery**
```
rommon prompt on next reload 0x2120
ignore the startup-config 0x2142
reboot normally on the next reload 0x2102

Router#reload
Router(config)#config-register 0x2102 (2120)

rommon 1 > confreg 0x2102 (2142)
rommon 2 > reset

R3#more flash:live-config

R3#copy flash running-config 

R1(config)#interface GigabitEthernet0/0
R1(config-if)# ip address 10.10.10.1 255.255.255.0 
R1(config-if)# duplex auto
R1(config-if)# speed auto 

R1(config-if)#line con 0
R1(config-line)# exec-timeout 30 0 
R1(config-line)#end

R1#copy run flash
R1#copy start tftp
R1#copy flash tftp
SW1#copy tftp flash

R1#delete flash:c2900-universalk9-mz.SPA.151-4.M4.bin 

rommon 1 > IP_ADDRESS=10.10.10.1
rommon 2 > IP_SUBNET_MASK=255.255.255.0 
rommon 3 > DEFAULT_GATEWAY=10.10.10.1 
rommon 4 > TFTP_SERVER=10.10.10.10
rommon 5 > TFTP_FILE=c2900-universalk9-mz.SPA.151-4.M4.bin rommon 6 > TFTP_DESTINATION=flash:
rommon 7 > TFTP_TIMEOUT=120
rommon 8 > tftpdnld
rommon 9 > reset 

SW1(config)#boot system c2960-lanbasek9-mz.150-2.SE4.bin
```
## **[The End]**