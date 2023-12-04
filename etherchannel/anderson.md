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