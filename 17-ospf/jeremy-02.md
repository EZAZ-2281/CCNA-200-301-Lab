# **OSPF Configuration Part-02**
## Source (YouTube: Jeremy's IT Lab)
### Video Link: [Here](https://youtu.be/UEyQW-EcnY8?si=E0B_P1kVRgef_Chy)
### Lab File Link (pkt): [Here Day-27](https://mega.nz/file/G04zyTxA#IUbqyFjXB85sbo8HI4wDMvi2h9Gv7j1WeRq7pRugRn4)
### Scenario:
![](../images/ospfj.PNG)

1. Configure the appropriate hostnames and IP addresses on each device.  Enable router interfaces. (You don't have to configure ISPR1)
```
R1(config)#int f1/0
R1(config-if)#ip address 10.0.13.1 255.255.255.252
R1(config-if)#no shut
R1(config-if)#int g0/0
R1(config-if)#ip address 10.0.12.1 255.255.255.252
R1(config-if)#no shut
R1(config-if)#int g3/0
R1(config-if)#ip address 203.0.113.1 255.255.255.252
R1(config-if)#no shut
------------------------------------------------------
R2(config)#int f1/0
R2(config-if)#ip address 10.0.24.1 255.255.255.252
R2(config-if)#no shut
R2(config-if)#int g0/0
R2(config-if)#ip address 10.0.12.2 255.255.255.252
R2(config-if)#no shut
------------------------------------------------------
R3(config)#int f1/0
R3(config-if)#ip address 10.0.13.2 255.255.255.252
R3(config-if)#no shut
R3(config-if)#int f2/0
R3(config-if)#ip address 10.0.34.1 255.255.255.252
R3(config-if)#no shut
------------------------------------------------------
R4(config)#int f1/0
R4(config-if)#ip address 10.0.24.2 255.255.255.252
R4(config-if)#no shut
R4(config-if)#int f2/0
R4(config-if)#ip address 10.0.34.2 255.255.255.252
R4(config-if)#no shut
R4(config-if)#int g0/0
R4(config-if)#ip address 192.168.4.254 255.255.255.0 
R4(config-if)#no shut
```
2. Configure a loopback interface on each router (1.1.1.1/32 for R1, 2.2.2.2/32 for R2, etc.)
```
R1(config)#int l0
R1(config-if)#ip address 1.1.1.1 255.255.255.255
R1(config-if)#no shut
------------------------------------------------------
R2(config)#int l0
R2(config-if)#ip address 3.3.3.3 255.255.255.255
R2(config-if)#no shut 
------------------------------------------------------
R3(config)#int l0
R3(config-if)#ip address 3.3.3.3 255.255.255.255
R3(config-if)#no shut 
------------------------------------------------------
R4(config)#int l0
R4(config-if)#ip address 2.2.2.2 255.255.255.255
R4(config-if)#no shut 
```
3. Enable OSPF directly on each interface of the routers. Configure passive interfaces as appropriate.
```
R1(config)#int range f1/0, g0/0, g3/0
R1(config-if-range)#ip ospf 1 area 0
R1(config)#router ospf 1
R1(config-router)#passive-interface l0
--------------------------------------------
R2(config)#router ospf 1
R2(config-router)#passive-interface l0
R2(config)#int range f1/0, g0/0
R2(config-if-range)#ip ospf 1 area 0
--------------------------------------------
R3(config)#router ospf 1
R3(config-router)#passive-interface l0
R3(config)#int range f1/0, f2/0
R3(config-if-range)#ip ospf 1 area 0
--------------------------------------------
R4(config)#int range f1/0, f2/0, g0/0
R4(config-if-range)#ip ospf 1 area 0
R4(config)#router ospf 1
R4(config-router)#passive-interface default
R4(config-router)#no passive-interface f1/0
R4(config-router)#no passive-interface f2/0
```
```
R1#sh ip protocols

Routing Protocol is "ospf 1"
  Outgoing update filter list for all interfaces is not set 
  Incoming update filter list for all interfaces is not set 
  Router ID 1.1.1.1 ✅
  Number of areas in this router is 1. 1 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
  Passive Interface(s): ✅
    Loopback0
  Routing Information Sources:  
    Gateway         Distance      Last Update 
    1.1.1.1              110      00:06:34
    2.2.2.2              110      00:02:36
    3.3.3.3              110      00:02:35
    4.4.4.4              110      00:02:06
  Distance: (default is 110)
```
```
R1#sh ip ospf nei


Neighbor ID     Pri   State           Dead Time   Address         Interface
3.3.3.3           1   FULL/BDR        00:00:30    10.0.13.2       FastEthernet1/0
2.2.2.2           1   FULL/BDR        00:00:39    10.0.12.2       GigabitEthernet0/0
```
4. Configure the reference bandwidth on each router so a FastEthernet interface has a cost of 100.
```
R1(config)#router ospf 1
R1(config-router)#auto-cost reference-bandwidth 10000
```
```
R1#sh ip ospf int 

FastEthernet1/0 is up, line protocol is up
  Internet address is 10.0.13.1/30, Area 0
  Process ID 1, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 100 ✅
GigabitEthernet0/0 is up, line protocol is up
  Internet address is 10.0.12.1/30, Area 0
  Process ID 1, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 10 ✅
GigabitEthernet3/0 is up, line protocol is up
  Internet address is 203.0.113.1/30, Area 0
  Process ID 1, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 10 ✅
```
5. Configure R1 as an ASBR that advertises a default route in to the OSPF domain.
```
R1(config)#ip route 0.0.0.0 0.0.0.0 203.0.113.2
R1(config)#router ospf 1
R1(config-router)#default-information originate
```
6. Check the routing tables of R4.  What default route(s) were added?
```
R4#sh ip route ospf
     10.0.0.0/30 is subnetted, 4 subnets
O       10.0.12.0 [110/2] via 10.0.24.1, 00:06:49, FastEthernet1/0
O       10.0.13.0 [110/2] via 10.0.34.1, 00:06:49, FastEthernet2/0
     203.0.113.0/30 is subnetted, 1 subnets
O       203.0.113.0 [110/12] via 10.0.24.1, 00:03:02, FastEthernet1/0
                    [110/12] via 10.0.34.1, 00:03:02, FastEthernet2/0
O*E2 0.0.0.0/0 [110/1] via 10.0.24.1, 00:00:54, FastEthernet1/0 ✅
O*E2 0.0.0.0/0 [110/1] via 10.0.34.1, 00:00:54, FastEthernet2/0 ✅
```
7. Use Simulation mode to view the OSPF Hello messages being sent by the routers. What fields are included in the Hello message?
- Watch this video: 

https://github.com/EZAZ-2281/CCNA-200-301-Lab/assets/81481142/e151e3c7-12ee-482d-a12e-e989e62ba05a

## **[The End]**