# Etherchannel Configuration (Source: Udemy)
## Instructor: Neil Anderson  
### Scenario: 
![](../images/26-1%20EtherChannel%20Configuration.PNG)
### **Pkt file:** [Here](https://mega.nz/file/6wJUXYCY#KmU7XoUzHEXSUS6vxC_dC_L_eOk0i68iDJpsFdZeJF4)


# **LACP EtherChannel Configuration.**
### **1) The access layer switches Acc3 and Acc4 both have two FastEthernet uplinks. How much total bandwidth is available between the PCs attached to Acc3 and the PCs attached to Acc4?**
> Spanning tree shuts down all but one uplink on both switches so the total bandwidth available between them is a sinle FastEthernet link - **100 Mbps**. 

### **2) Convert the existing uplinks from Acc3 to CD1 and CD2 to LACP EtherChannel. Configure descriptions on the port channel interfaces to help avoid confusion later.**
```
ACC3: 
Acc3(config)#int range f0/23-24
Acc3(config-if-range)#channel-group 1 mode active 
Acc3(config-if-range)#exit
Acc3(config)#int port-channel 1
Acc3(config-if)#switchport mode trunk 
Acc3(config-if)#switchport trunk native vlan 199
Acc3(config-if)#description Link to CD1

CD1:
CD1(config)#int range f0/23-24
CD1(config-if-range)#channel-group 1 mode active 
CD1(config-if-range)#exit
CD1(config)#int port-channel 1
CD1(config-if)#switchport mode trunk 
CD1(config-if)#switchport trunk native vlan 199
CD1(config-if)#description Link to ACC3
-----------------------------------------------
ACC3: 
Acc3(config)#int range f0/21-22
Acc3(config-if-range)#channel-group 2 mode active 
Acc3(config-if-range)#int port-channel 2
Acc3(config-if)#switchport mode trunk 
Acc3(config-if)#switchport trunk native vlan 199
Acc3(config-if)#description Link to CD2

CD2: 
CD2(config)#int range f0/21-22
CD2(config-if-range)#channel-group 2 mode active 
CD2(config-if-range)#int port-channel 2
CD2(config-if)#switchport mode trunk 
CD2(config-if)#switchport trunk native vlan 199
CD2(config-if)#description Link to ACC3
```
### **3) Verify the EtherChannels come up.**
```
Acc3#sh et summ
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 2
Number of aggregators:           2

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(SU)           LACP   Fa0/23(P) Fa0/24(P) 🟩
2      Po2(SU)           LACP   Fa0/21(P) Fa0/22(P) 🟩
```
```
CD1(config-if)#do sh et summ
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 1
Number of aggregators:           1

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(SU)           LACP   Fa0/23(P) Fa0/24(P) 🟩
```
```
CD2(config-if)#do sh et summ
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 1
Number of aggregators:           1

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

2      Po2(SU)           LACP   Fa0/21(P) Fa0/22(P) 🟩
```
# **PAgP EtherChannel Configuration.**
### **4) Convert the existing uplinks from Acc4 to CD1 and CD2 to PAgP EtherChannel. (Note that in a real world environment you should always use LACP if possible.)**
```
ACC4:
Acc4(config)#int range f0/21-22
Acc4(config-if-range)#channel-group 2 mode desirable 
Acc4(config-if-range)#int port-channel 2
Acc4(config-if)#switchport mode trunk 
Acc4(config-if)#switchport trunk native vlan 199
Acc4(config-if)#description Link to CD1

CD1: 
CD1(config)#int range f0/21-22
CD1(config-if-range)#channel-group 2 mode desirable 
CD1(config-if-range)#int port-channel 2
CD1(config-if)#switchport mode trunk 
CD1(config-if)#switchport trunk native vlan 199
CD1(config-if)#description Link to ACC4
---------------------------------------------------
ACC4: 
Acc4(config)#int range f0/23-24
Acc4(config-if-range)#channel-group 1 mode desirable 
Acc4(config-if-range)#int port-channel 1
Acc4(config-if)#switchport mode trunk 
Acc4(config-if)#switchport trunk native vlan 199
Acc4(config-if)#description Link to CD2

CD2: 
CD2(config)#int range f0/23-24
CD2(config-if-range)#channel-group 1 mode desirable
CD2(config-if-range)#int port-channel 1
CD2(config-if)#switchport mode trunk 
CD2(config-if)#switchport trunk native vlan 199
CD2(config-if)#description Link to ACC4
```
### **5) Verify the EtherChannels come up.**
```
Acc4(config-if)#do sh et summ
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 2
Number of aggregators:           2

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(SU)           PAgP   Fa0/23(P) Fa0/24(P) 🟩
2      Po2(SU)           PAgP   Fa0/21(P) Fa0/22(P) 🟩
```
```
CD2(config-if)#do sh et summ
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 2
Number of aggregators:           2

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(SU)           PAgP   Fa0/23(P) Fa0/24(P) 🟩
2      Po2(SU)           LACP   Fa0/21(P) Fa0/22(P) 🟩
```
# **Static EtherChannel Configuration**
### **6) Convert the existing uplinks between CD1 and CD2 to static EtherChannel.**
```
CD1: 
CD1(config)#int range g0/1-2
CD1(config-if-range)#channel-group 3 mode on 
CD1(config-if-range)#int po 3
CD1(config-if)#sw mode trunk 
CD1(config-if)#sw trunk native vlan 199
CD1(config-if)#desc Link to CD2
CD2: 
CD2(config)#int range g0/1-2
CD2(config-if-range)#channel-group 3 mode on 
CD2(config-if-range)#int po 3
CD2(config-if)#sw mode trunk
CD2(config-if)#sw trunk native vlan 199
CD2(config-if)#desc Link to CD1
```
### **7) Verify the EtherChannel comes up.**
```
CD1(config-if)#do sh et summ
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 3
Number of aggregators:           3

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(SU)           LACP   Fa0/23(P) Fa0/24(P) 
2      Po2(SU)           PAgP   Fa0/21(P) Fa0/22(P) 
3      Po3(SU)           -      Gig0/1(P) Gig0/2(P) 🟩
```
```
CD2(config-if)#do sh et summ
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 3
Number of aggregators:           3

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(SU)           PAgP   Fa0/23(P) Fa0/24(P) 
2      Po2(SU)           LACP   Fa0/21(P) Fa0/22(P) 
3      Po3(SU)           -      Gig0/1(P) Gig0/2(P) 🟩
```
### **8) How much total bandwidth is available between the PCs attached to Acc3 and the PCs attached to Acc4 now?**
>The port channels from the Acc3 and Acc4 switches towards the root bridge CD1 are up and forwarding. Spanning tree shuts down the port channels toward CD2 to prevent a loop. The port channels from Acc3 and Acc4 facing the root bridge comprise two FastEthernet interfaces, so the total bandwidth available between the PCs attached to the different access layer switches is **200 Mbps**.

# **Layer 3 EtherChannel Configuration.**
>The Layer 3 switches Switch1, Switch2 and Switch3 are physically separate from the switches you configured earlier in this lab exercise.

### **9) Switch1 and Switch2 are connected together with their GigabitEthernet1/0/1 and 1/0/2 interfaces. Configure these interfaces as a Layer 3 Etherchannel with LACP. Configure IP address 192.168.0.1/30 on Switch1 and 192.168.0.2/30 on Switch2.**
```
Switch1: 
Switch1(config)#int range g1/0/1-2
Switch1(config-if-range)#no switchport
Switch1(config-if-range)#channel-group 1 mode active
Switch1(config-if-range)#int po 1
Switch1(config-if)#ip add 192.168.0.1 255.255.255.252 
Switch1(config-if)#no shut

Switch2: 
Switch2(config)#int range g1/0/1-2
Switch2(config-if-range)#no switchport
Switch2(config-if-range)#channel-group 1 mode active 
Switch2(config-if-range)#int po 1
Switch2(config-if)#ip add 192.168.0.2 255.255.255.252 
Switch2(config-if)#no shut
```
### **10) Switch1 and Switch3 are connected together with their GigabitEthernet1/0/3 and 1/0/4 interfaces. Configure these interfaces as a Layer 3 Etherchannel with LACP. Configure IP address 192.168.0.5/30 on Switch1 and 192.168.0.6/30 on Switch3.**
```
Switch1: 
Switch1(config-if)#int range g1/0/3-4
Switch1(config-if-range)#no switchport
Switch1(config-if-range)#channel-group 2 mode active 
Switch1(config-if-range)#int po 2
Switch1(config-if)#ip add 192.168.0.5 255.255.255.252
Switch1(config-if)#no shut

Switch3: 
Switch3(config)#int range g1/0/3-4
Switch3(config-if-range)#no switchport
Switch3(config-if-range)#channel-group 2 mode active 
Switch3(config-if-range)#int po 2
Switch3(config-if)#ip add 192.168.0.6 255.255.255.252
Switch3(config-if)#no shut
```
### **11) Switch2 and Switch3 are connected together with their GigabitEthernet1/0/5 and 1/0/6 interfaces. Configure these interfaces as a Layer 3 Etherchannel with LACP. Configure IP address 192.168.0.9/30 on Switch2 and 192.168.0.10/30 on Switch3.**
```
Switch2: 
Switch2(config-if)#int range g1/0/5-6
Switch2(config-if-range)#no switchport
Switch2(config-if-range)#channel-group 3 mode active 
Switch2(config-if-range)#int po 3
Switch2(config-if)#ip add 192.168.0.9 255.255.255.252 
Switch2(config-if)#no shut

Switch3: 
Switch3(config-if)#int range g1/0/5-6
Switch3(config-if-range)#no switchport
Switch3(config-if-range)#channel-group 3 mode active 
Switch3(config-if-range)#int po 3
Switch3(config-if)#ip add 192.168.0.10 255.255.255.252
Switch3(config-if)#no shut
```
### **12) Verify the EtherChannels come up.**
```
Switch1(config-if)#do sh et summ
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 2
Number of aggregators:           2

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(RU)           LACP   Gig1/0/1(P) Gig1/0/2(P) 🟩
2      Po2(RU)           LACP   Gig1/0/3(P) Gig1/0/4(P) 🟩
```
> do it in Switch2 and Switch3. 

### **13) Configure Switch1, Switch2 and Switch3 to advertise the IP subnets configured on their Etherchannel interfaces in OSPF Area 0.**
```
Switch1(config-if)#ip routing 
Switch1(config)#router ospf 1
Switch1(config-router)#network 192.168.0.0 0.0.0.255 area 0

Switch2(config-if)#ip routing 
Switch2(config)#router ospf 1
Switch2(config-router)#network 192.168.0.0 0.0.0.255 area 0

Switch3(config-if)#ip routing 
Switch3(config)#router ospf 1
Switch3(config-router)#network 192.168.0.0 0.0.0.255 area 0
```
### **14) Verify the OSPF adjacencies are formed successfully.**
```
Switch1#sh ip ospf nei


Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.0.10      1   FULL/DR         00:00:35    192.168.0.6     Port-channel2
192.168.0.9       1   FULL/DR         00:00:30    192.168.0.2     Port-channel1
```
> do it also Switch2 and Switch3.

### **15) Verify Switch1, Switch2 and Switch3 have routes to all configured networks in their routing tables.**
```
Switch1#sh ip route 
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     192.168.0.0/30 is subnetted, 3 subnets
C       192.168.0.0 is directly connected, Port-channel1
C       192.168.0.4 is directly connected, Port-channel2
O       192.168.0.8 [110/2] via 192.168.0.2, 00:00:59, Port-channel1 🟩
                    [110/2] via 192.168.0.6, 00:00:59, Port-channel2 🟩
```					
### **16) Which physical ports on which switches do you expect the Spanning Tree protocol to disable? Verify this.**
```
Switch1#sh span

No spanning tree instance exists.
```
>Spanning Tree only runs on Layer 2 interface. It will not run on or shut any of the ports down as they are all Layer-3 ports. The Layer-3 switches routing tables will handle path selection, redundancy and load balancing. 

### **[The End]**