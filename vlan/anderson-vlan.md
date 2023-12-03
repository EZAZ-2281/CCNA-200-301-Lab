# VLAN Configuration (Source: Udemy)
## Instructor: Neil Anderson  
### Scenario: 
![](../images/22-1%20VLAN%20and%20Inter-VLAN%20Routing%20Configuration.jpg)
### **Pkt file:** [Here](https://mega.nz/folder/KgojHATA#B7nVO6XfEG9wSY7KC-6gFw)

<div style="text-align:center; font-size:30px; font-weight:bold; color:red;">
  VTP, Access and Trunk Ports
</div>

### **1) All routers and switches are in a factory default state. View the VLAN database on SW1 to verify no VLANs have been added.**
```
SW1#sh vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active  
```
### **2) View the default switchport status on the link from SW1 to SW2.**
```
SW1#sh int g0/1 switchport 
Name: Gig0/1
Switchport: Enabled
Administrative Mode: dynamic auto 🟩 
Operational Mode: static access 🟩
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: native
Negotiation of Trunking: On
Access Mode VLAN: 1 (default)
Trunking Native Mode VLAN: 1 (default)
Voice VLAN: none
Administrative private-vlan host-association: none
Administrative private-vlan mapping: none
Administrative private-vlan trunk native VLAN: none
Administrative private-vlan trunk encapsulation: dot1q
Administrative private-vlan trunk normal VLANs: none
Administrative private-vlan trunk private VLANs: none
Operational private-vlan: none
Trunking VLANs Enabled: All
Pruning VLANs Enabled: 2-1001
Capture Mode Disabled
Capture VLANs Allowed: ALL
Protected: false
Unknown unicast blocked: disabled
Unknown multicast blocked: disabled
Appliance trust: none
(The trunking mode is dynamic auto and interface is static access mode. We need to change it to trunk mode) 
```
### **3) Configure the links between switches as trunks.**
```
SW1(config)#int g0/1
SW1(config-if)#switchport trunk ?
  allowed  Set allowed VLAN characteristics when interface is in trunking mode
  native   Set trunking native characteristics when interface is in trunking
           mode
SW1(config-if)#switchport mode trunk 
```
```
SW2(config)#int g0/1
SW2(config-if)#switchport trunk ?
  allowed        Set allowed VLAN characteristics when interface is in trunking
                 mode
  encapsulation  Set trunking encapsulation when interface is in trunking mode
  native         Set trunking native characteristics when interface is in
                 trunking mode
SW2(config-if)#switchport trunk encap dot1q
SW2(config-if)#switchport mode trunk 

SW2(config-if)#int g0/2
SW2(config-if)#switchport trunk encap dot1q
SW2(config-if)#sw mode trunk 
```
```
SW3(config)#int g0/2
SW3(config-if)#switchport trunk ?
  allowed  Set allowed VLAN characteristics when interface is in trunking mode
  native   Set trunking native characteristics when interface is in trunking
           mode
SW3(config-if)#switchport mode trunk 
```
### **4) Configure SW1 as a VTP Server in the VTP domain ezaz.**
```
SW1(config)#vtp domain ezaz
Changing VTP domain name from NULL to ezaz
SW1(config)#vtp mode server
Device mode already VTP SERVER.
```
### **5) SW2 must not synchronise its VLAN database with SW1.**
```
SW2(config)#vtp mode ?
  client       Set the device to client mode.
  server       Set the device to server mode.
  transparent  Set the device to transparent mode.
SW2(config)#vtp mode transparent
Setting device to VTP TRANSPARENT mode.
```
### **6) SW3 must learn VLAN information from SW1. VLANs should not be edited on SW3.**
```
SW3(config)#vtp domain ezaz
Changing VTP domain name from NULL to ezaz
SW3(config)#vtp mode client
Setting device to VTP CLIENT mode.
```
### **7) Add the Eng, Sales and Native VLANs on all switches.**
```
SW1(config)#vlan 10
SW1(config-vlan)#name Eng
SW1(config-vlan)#vlan 20
SW1(config-vlan)#name Sales
SW1(config-vlan)#vlan 199
SW1(config-vlan)#name Native
-------------------------
SW2(config)#vlan 10
SW2(config-vlan)#name Eng
SW2(config-vlan)#vlan 20
SW2(config-vlan)#name Sales
SW2(config-vlan)#vlan 199
SW2(config-vlan)#name Native
```
### **8) Verify the VLANs are in the database on each switch.**
```
SW3(config)#do sh vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1
10   Eng                              active    
20   Sales                            active    
199  Native                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active   
```
### **9) Configure the trunk links to use VLAN 199 as the native VLAN for better security.**
```
SW1(config)#int g0/1
SW1(config-if)#sw trunk native  vlan 199
------------------------
SW2(config)#int g0/1
SW2(config-if)#sw trunk native vlan 199
SW2(config-if)#int g0/2
SW2(config-if)#sw trunk native vlan 199
------------------------
SW3(config)#int g0/2
SW3(config-if)#sw trunk native vlan 199
```
### **10) Configure the switchports connected to the PCs with the correct VLAN configuration.**
```
SW1(config)#int range f0/1-2
SW1(config-if-range)#sw mode access
SW1(config-if-range)#sw acc vlan 10
SW1(config-if-range)#int f0/3
SW1(config-if)#sw mode acc
SW1(config-if)#sw acc vlan 20
-------------------------------
SW3(config)#int range f0/1-2
SW3(config-if-range)#sw mode acc
SW3(config-if-range)#sw acc vlan 20
SW3(config-if-range)#int f0/3
SW3(config-if)#sw mode acc
SW3(config-if)#sw acc vlan 10
```
### **11) Verify the Eng1 PC has connectivity to Eng3.**
```
C:\>ping 10.10.10.12

Pinging 10.10.10.12 with 32 bytes of data:

Reply from 10.10.10.12: bytes=32 time=1ms TTL=128
Reply from 10.10.10.12: bytes=32 time<1ms TTL=128
Reply from 10.10.10.12: bytes=32 time<1ms TTL=128
Reply from 10.10.10.12: bytes=32 time<1ms TTL=128

Ping statistics for 10.10.10.12:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms
```
### **12) Verify Sales1 has connectivity to Sales3.**
```
C:\>ping 10.10.20.12

Pinging 10.10.20.12 with 32 bytes of data:

Reply from 10.10.20.12: bytes=32 time=1ms TTL=128
Reply from 10.10.20.12: bytes=32 time<1ms TTL=128
Reply from 10.10.20.12: bytes=32 time<1ms TTL=128
Reply from 10.10.20.12: bytes=32 time<1ms TTL=128

Ping statistics for 10.10.20.12:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms
```