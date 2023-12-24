# Dynamic Routing Protocol (Source: Udemy)
## Instructor: Neil Anderson  
### **Pkt file:** [Here](https://mega.nz/file/64p2kS4S#l3Ev9ykxUYV6TMuBZLovT13z_FiLZa0qwZF2-QaxWYk)
### Scenario: 
![](../images/anrip.PNG)

## **Routing Protocol Updates**

1)	Enter the commands below on each router to provision a basic RIPv1 configuration and enable RIP on every interface.
```
router rip 
network 10.0.0.0 
no auto-summary
```
```
R1(config)#router rip
R1(config-router)#no auto-summary
R1(config-router)#network 10.0.0.0
```
2)	Debug the routing protocol updates on R1 with the ‘debug ip rip’ command. Observe the updates being sent and received. What kind of traffic is use (unicast, broadcast or multicast)?
- The updates are being sent on the broadcast address 255.255.255.255. All hosts on the subnet must process the packets.
```
R1#debug ip rip
RIP protocol debugging is on
R1#RIP: sending  v1 update to 255.255.255.255 via FastEthernet0/0 (10.0.0.1)
RIP: build update entries
      network 10.0.1.0 metric 1
      network 10.0.2.0 metric 1
      network 10.0.3.0 metric 1
      network 10.1.2.0 metric 3
      network 10.1.3.0 metric 2
```
3)	Enter the commands below to enable RIPv2 on every router.
```
router rip 
version 2
```
```
R1(config)#router rip
R1(config-router)#version 2
```
4)	What kind of traffic is used for the updates now?
- The updates are being sent on the RIPv2 multicast address 224.0.0.9. Only RIPv2 routers will process the packets beyond layer 3.
```
R1#RIP: sending  v2 update to 224.0.0.9 via FastEthernet0/0 (10.0.0.1)
RIP: build update entries
      10.0.1.0/24 via 0.0.0.0, metric 1, tag 0
      10.0.2.0/24 via 0.0.0.0, metric 1, tag 0
      10.0.3.0/24 via 0.0.0.0, metric 1, tag 0
      10.1.2.0/24 via 0.0.0.0, metric 3, tag 0
      10.1.3.0/24 via 0.0.0.0, metric 2, tag 0
```
5)	Turn off all debugging on R1.
```
R1#undebug all
All possible debugging has been turned off
```
6)	Check that RIP routes have been added to R1 and it has a route to every subnet in the lab.
```
R1#sh ip route rip
     10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
R       10.1.0.0/24 [120/1] via 10.0.0.2, 00:00:14, FastEthernet0/0
R       10.1.1.0/24 [120/2] via 10.0.0.2, 00:00:14, FastEthernet0/0
                    [120/2] via 10.0.3.2, 00:00:15, FastEthernet1/1
R       10.1.2.0/24 [120/2] via 10.0.3.2, 00:00:15, FastEthernet1/1
R       10.1.3.0/24 [120/1] via 10.0.3.2, 00:00:15, FastEthernet1/1
```
7)	Why are there two routes to the 10.1.1.0/24 network in the routing table?
- Two paths to 10.1.1.0/24 have an equal metric – a hop count of 2. Both routes are installed in the routing table and the router will perform Equal Cost Load Balancing between the next hops of 10.0.3.2 and 10.0.0.2.

## **[The End]**

## **Comparing Routing Protocols**

8)	View the RIP database on R1.
```
R1#sh ip rip database
10.0.0.0/24    auto-summary
10.0.0.0/24    directly connected, FastEthernet0/0
10.0.1.0/24    auto-summary
10.0.1.0/24    directly connected, FastEthernet0/1
10.0.2.0/24    auto-summary
10.0.2.0/24    directly connected, FastEthernet1/0
10.0.3.0/24    auto-summary
10.0.3.0/24    directly connected, FastEthernet1/1
10.1.0.0/24    auto-summary
10.1.0.0/24
    [1] via 10.0.0.2, 00:00:04, FastEthernet0/0
10.1.1.0/24    auto-summary
10.1.1.0/24
    [2] via 10.0.0.2, 00:00:04, FastEthernet0/0    [2] via 10.0.3.2, 00:00:03, FastEthernet1/1
10.1.2.0/24    auto-summary
10.1.2.0/24
    [2] via 10.0.3.2, 00:00:03, FastEthernet1/1
10.1.3.0/24    auto-summary
10.1.3.0/24
    [1] via 10.0.3.2, 00:00:03, FastEthernet1/1
```
9)	Enter the commands below on each router to provision a basic OSPF configuration and enable OSPF on every interface.
```
router ospf 1
network 10.0.0.0 0.255.255.255 area 0
```
```
R1(config)#router ospf 1
R1(config-router)#network 10.0.0.0 0.255.255.255 area 0
```
10)	Give OSPF time to converge. Are RIP routes included in the routing table on R1 now? Why or why not?
- The RIP routes are replaced by OSPF because its Administrative Distance of 110 is preferred to RIP’s AD of 120.
```
R1#sh ip route 

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C       10.0.0.0/24 is directly connected, FastEthernet0/0
L       10.0.0.1/32 is directly connected, FastEthernet0/0
C       10.0.1.0/24 is directly connected, FastEthernet0/1
L       10.0.1.1/32 is directly connected, FastEthernet0/1
C       10.0.2.0/24 is directly connected, FastEthernet1/0
L       10.0.2.1/32 is directly connected, FastEthernet1/0
C       10.0.3.0/24 is directly connected, FastEthernet1/1
L       10.0.3.1/32 is directly connected, FastEthernet1/1
O       10.1.0.0/24 [110/2] via 10.0.0.2, 00:02:30, FastEthernet0/0
O       10.1.1.0/24 [110/3] via 10.0.0.2, 00:02:30, FastEthernet0/0
O       10.1.2.0/24 [110/4] via 10.0.0.2, 00:02:30, FastEthernet0/0
O       10.1.3.0/24 [110/13] via 10.0.0.2, 00:02:30, FastEthernet0/0
```
11)	Why is there now only one route to the 10.1.1.0/24 network?
- OSPF uses cost as its metric which takes into account interface bandwidth. The interfaces on R5 have a configured bandwidth of 10Mbps. The interfaces along the top path of the network topology all have the default FastEthernet bandwidth of 100Mbps so this route is preferred. All traffic will go via the next hop 10.0.0.2.
```
R5#sh run | section interface
interface FastEthernet0/0
 bandwidth 10000 ✅
 ip address 10.1.3.2 255.255.255.0
 duplex auto
 speed auto
interface FastEthernet0/1
 bandwidth 10000 ✅
 ip address 10.0.3.2 255.255.255.0
 duplex auto
 speed auto
```
12)	Disable interface FastEthernet 0/0 on R2. What do you expect to happen to R1’s routing table?
- OSPF will reconverge. The routes to the 10.1.0.0 networks via R2 will be removed from the routing table and replaced with routes via R5.
```
R2(config)#int f0/0
R2(config-if)#shut

%LINK-5-CHANGED: Interface FastEthernet0/0, changed state to administratively down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to down

23:18:13: %OSPF-5-ADJCHG: Process 1, Nbr 10.0.3.1 on FastEthernet0/0 from FULL to DOWN, Neighbor Down: Interface down or detached ✅
```
13)	Verify your expected changes to R1’s routing table.
```
R1#sh ip route 

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 10 subnets, 2 masks
C       10.0.1.0/24 is directly connected, FastEthernet0/1
L       10.0.1.1/32 is directly connected, FastEthernet0/1
C       10.0.2.0/24 is directly connected, FastEthernet1/0
L       10.0.2.1/32 is directly connected, FastEthernet1/0
C       10.0.3.0/24 is directly connected, FastEthernet1/1
L       10.0.3.1/32 is directly connected, FastEthernet1/1
O       10.1.0.0/24 [110/22] via 10.0.3.2, 00:00:56, FastEthernet1/1
O       10.1.1.0/24 [110/21] via 10.0.3.2, 00:00:56, FastEthernet1/1
O       10.1.2.0/24 [110/21] via 10.0.3.2, 00:00:56, FastEthernet1/1
O       10.1.3.0/24 [110/20] via 10.0.3.2, 00:00:56, FastEthernet1/1
```
14)	Aside from the next hop address, what else has changed on the routing table?
- The new routes have a higher metric. This is why they were not in the routing table when the path via R2 was up.
```
R1#sh ip route 

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 10 subnets, 2 masks
C       10.0.1.0/24 is directly connected, FastEthernet0/1
L       10.0.1.1/32 is directly connected, FastEthernet0/1
C       10.0.2.0/24 is directly connected, FastEthernet1/0
L       10.0.2.1/32 is directly connected, FastEthernet1/0
C       10.0.3.0/24 is directly connected, FastEthernet1/1
L       10.0.3.1/32 is directly connected, FastEthernet1/1
O       10.1.0.0/24 [110/22✅] via 10.0.3.2, 00:00:56, FastEthernet1/1
O       10.1.1.0/24 [110/21✅] via 10.0.3.2, 00:00:56, FastEthernet1/1
O       10.1.2.0/24 [110/21✅] via 10.0.3.2, 00:00:56, FastEthernet1/1
O       10.1.3.0/24 [110/20✅] via 10.0.3.2, 00:00:56, FastEthernet1/1
```
15)	View the OSPF database on R1 with the ‘show ip ospf database’ command. What is different between it and the RIP database? Why?
```
R1#sh ip ospf database
            OSPF Router with ID (10.0.3.1) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
10.1.1.2        10.1.1.2        617         0x80000004 0x003cb6 2
203.0.113.1     203.0.113.1     612         0x80000005 0x00df85 3
10.1.3.2        10.1.3.2        612         0x80000004 0x00844e 2
10.0.3.1        10.0.3.1        185         0x80000007 0x00775c 3
10.1.0.2        10.1.0.2        185         0x80000005 0x00c15c 1

                Net Link States (Area 0)
Link ID         ADV Router      Age         Seq#       Checksum
10.1.0.1        10.1.1.2        622         0x80000001 0x006156
10.1.1.1        203.0.113.1     617         0x80000001 0x00df70
10.0.3.2        10.1.3.2        612         0x80000001 0x00624a
10.1.3.1        203.0.113.1     612         0x80000002 0x002b19
```
- RIP is a Distance Vector routing protocol so it only knows its directly connected neighbors and the lists of networks those neighbors have advertised.
- OSPF is a Link State routing protocol so it knows the state of every link on every router in its area.
## **[The End]**

## **Routing Protocol Metrics and Administrative Distance**

16)	Enter the command below to remove OSPF on every router
```
no router ospf 1
```
```
R1(config)#no router ospf 1
```
17)	Will R1 still have connectivity to R4?
- Yes. RIP is still running so RIP routes will replace the removed OSPF routes in the routing table.
```
R1#sh ip route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 10 subnets, 2 masks
C       10.0.1.0/24 is directly connected, FastEthernet0/1
L       10.0.1.1/32 is directly connected, FastEthernet0/1
C       10.0.2.0/24 is directly connected, FastEthernet1/0
L       10.0.2.1/32 is directly connected, FastEthernet1/0
C       10.0.3.0/24 is directly connected, FastEthernet1/1
L       10.0.3.1/32 is directly connected, FastEthernet1/1
R       10.1.0.0/24 [120/3] via 10.0.3.2, 00:00:28, FastEthernet1/1
R       10.1.1.0/24 [120/2] via 10.0.3.2, 00:00:28, FastEthernet1/1
R       10.1.2.0/24 [120/2] via 10.0.3.2, 00:00:28, FastEthernet1/1
R       10.1.3.0/24 [120/1] via 10.0.3.2, 00:00:28, FastEthernet1/1
```
18)	What is the metric to the 10.1.1.0/24 network on R1?
```
R       10.1.1.0/24 [120/2✅] via 10.0.3.2, 00:00:28, FastEthernet1/1
A hop count of 2. 
```
19)	Why is there only one route on R1 to the 10.1.1.0/24 network now?
```
Interface FastEthernet 0/0 on R2 is still shut down so no routes go through it.
```
20)	Make the required change so that there are two routes to the 10.1.1.0/24 network in the routing table on R1.
```
R2(config)#int f0/0
R2(config-if)#no shut
```
```
R1#sh ip route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C       10.0.0.0/24 is directly connected, FastEthernet0/0
L       10.0.0.1/32 is directly connected, FastEthernet0/0
C       10.0.1.0/24 is directly connected, FastEthernet0/1
L       10.0.1.1/32 is directly connected, FastEthernet0/1
C       10.0.2.0/24 is directly connected, FastEthernet1/0
L       10.0.2.1/32 is directly connected, FastEthernet1/0
C       10.0.3.0/24 is directly connected, FastEthernet1/1
L       10.0.3.1/32 is directly connected, FastEthernet1/1
R       10.1.0.0/24 [120/1] via 10.0.0.2, 00:00:09, FastEthernet0/0
R       10.1.1.0/24 [120/2] via 10.0.3.2, 00:00:11, FastEthernet1/1 ✅
                    [120/2] via 10.0.0.2, 00:00:09, FastEthernet0/0 ✅
R       10.1.2.0/24 [120/2] via 10.0.3.2, 00:00:11, FastEthernet1/1
R       10.1.3.0/24 [120/1] via 10.0.3.2, 00:00:11, FastEthernet1/1
```
21)	Enter the commands below on each router to provision a basic EIGRP configuration and enable EIGRP on every interface.
```
router eigrp 100 
no auto-summary
network 10.0.0.0 0.255.255.255
```
```
R1(config)#router eigrp 100
R1(config-router)#no auto-summary
R1(config-router)#network 10.0.0.0 0.255.255.255
```
22)	What changes do you expect to see in the routing tables? Why?
- The RIP routes will be replaced by EIGRP because its Administrative Distance of 90 is preferred to RIP’s AD of 120.
23)	Verify the changes to the routing table on R1.
```
R1#sh ip route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C       10.0.0.0/24 is directly connected, FastEthernet0/0
L       10.0.0.1/32 is directly connected, FastEthernet0/0
C       10.0.1.0/24 is directly connected, FastEthernet0/1
L       10.0.1.1/32 is directly connected, FastEthernet0/1
C       10.0.2.0/24 is directly connected, FastEthernet1/0
L       10.0.2.1/32 is directly connected, FastEthernet1/0
C       10.0.3.0/24 is directly connected, FastEthernet1/1
L       10.0.3.1/32 is directly connected, FastEthernet1/1
D       10.1.0.0/24 [90/30720] via 10.0.0.2, 00:00:46, FastEthernet0/0 ✅
D       10.1.1.0/24 [90/33280] via 10.0.0.2, 00:00:38, FastEthernet0/0 ✅
D       10.1.2.0/24 [90/35840] via 10.0.0.2, 00:00:32, FastEthernet0/0 ✅
D       10.1.3.0/24 [90/261120] via 10.0.3.2, 00:00:28, FastEthernet1/1 ✅
```
24)	What is the metric to the 10.1.1.0/24 network on R1?
```
A composite metric of 33280.
```
25)	Why is there only one route to the 10.1.1.0/24 network on R1?
```
R5#sh run | section interface
interface FastEthernet0/0
 bandwidth 10000 ✅
 ip address 10.1.3.2 255.255.255.0
 duplex auto
 speed auto
interface FastEthernet0/1
 bandwidth 10000 ✅
 ip address 10.0.3.2 255.255.255.0
 duplex auto
 speed auto
```
- EIGRP uses a composite metric which takes into account interface bandwidth and delay. The interfaces on R5 have a configured bandwidth of 10Mbps. The interfaces along the top path of the network topology all have the default FastEthernet bandwidth of 100Mbps so this route is preferred. All traffic will go via the next hop 10.0.0.2.
26)	Disable RIP and EIGRP on R5 with the commands below.
```
R5(config)#no router rip 
R5(config)#no router eigrp 100
```
27)	Configure the network so that there is still connectivity between all subnets if the link between R1 and R2 goes down. Accomplish this with six commands. Do not enable EIGRP on R5 but note that the routing protocol is expected to be enabled there in the future.
- Floating static routes need to be added as a backup to the EIGRP routes. We want to ensure EIGRP routes are preferred when available so set the AD to  be higher than EIGRP’s AD of 90.
```
R1(config)#ip route 10.1.0.0 255.255.0.0 10.0.3.2 95
R2(config)#ip route 10.0.0.0 255.0.0.0 10.1.0.1 95
R3(config)#ip route 10.0.0.0 255.0.0.0 10.1.1.1 95
R4(config)#ip route 10.0.0.0 255.0.0.0 10.1.3.2 95
R5(config)#ip route 10.0.0.0 255.255.0.0 10.0.3.1 95
R5(config)#ip route 10.1.0.0 255.255.0.0 10.1.3.1 95
```
- R5 is not running EIGRP so it is not currently necessary to set the Administrative Distance for its routes to 95. It is required to prevent the floating static routes from being preferred when EIGRP is enabled in the future however.
- Summary routes need to be used to accomplish the task in six commands.
28)	What changes do you expect to see to the routing table on R1?
- The summary route will be added to the routing table but not used because it has a prefix length of /16, compared to the EIGRP routes which have a longer prefix length of /24.
- If individual floating static routes had been added for each of the /24 destination networks then these would not have appeared in the routing table (unless a link went down) because EIGRP has a better Administrative Distance.

29)	Verify the changes to the routing table on R1.
```
R1#sh ip route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 13 subnets, 3 masks
C       10.0.0.0/24 is directly connected, FastEthernet0/0
L       10.0.0.1/32 is directly connected, FastEthernet0/0
C       10.0.1.0/24 is directly connected, FastEthernet0/1
L       10.0.1.1/32 is directly connected, FastEthernet0/1
C       10.0.2.0/24 is directly connected, FastEthernet1/0
L       10.0.2.1/32 is directly connected, FastEthernet1/0
C       10.0.3.0/24 is directly connected, FastEthernet1/1
L       10.0.3.1/32 is directly connected, FastEthernet1/1
S       10.1.0.0/16 [95/0] via 10.0.3.2
D       10.1.0.0/24 [90/30720] via 10.0.0.2, 00:15:41, FastEthernet0/0
D       10.1.1.0/24 [90/33280] via 10.0.0.2, 00:15:33, FastEthernet0/0
D       10.1.2.0/24 [90/35840] via 10.0.0.2, 00:15:27, FastEthernet0/0
D       10.1.3.0/24 [90/266240] via 10.0.0.2, 00:11:47, FastEthernet0/0
```
30)	Verify that traffic from PC1 to PC3 still goes via R2.
```
C:\>ping 10.1.2.1

Pinging 10.1.2.1 with 32 bytes of data:

Reply from 10.1.2.1: bytes=32 time<1ms TTL=252
Reply from 10.1.2.1: bytes=32 time=2ms TTL=252
Reply from 10.1.2.1: bytes=32 time<1ms TTL=252
Reply from 10.1.2.1: bytes=32 time=10ms TTL=252

Ping statistics for 10.1.2.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 10ms, Average = 3ms
```
```
C:\>traceroute 10.1.2.1
Invalid Command.
```
```
C:\>tracert 10.1.2.1

Tracing route to 10.1.2.1 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.0.1.1
  2   0 ms      0 ms      0 ms      10.0.0.2 ✅
  3   0 ms      1 ms      0 ms      10.1.0.1
  4   0 ms      0 ms      0 ms      10.1.2.1

Trace complete.
```
31)	Shut down interface FastEthernet 0/0 on R2.
```
R2(config)#int f0/0
R2(config-if)#shut
```
32)	What changes do you expect to see on R1’s routing table?
- The EIGRP routes will be removed.
33)	Verify the changes to the routing table on R1.
```
R1#sh ip route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 7 subnets, 3 masks
C       10.0.1.0/24 is directly connected, FastEthernet0/1
L       10.0.1.1/32 is directly connected, FastEthernet0/1
C       10.0.2.0/24 is directly connected, FastEthernet1/0
L       10.0.2.1/32 is directly connected, FastEthernet1/0
C       10.0.3.0/24 is directly connected, FastEthernet1/1
L       10.0.3.1/32 is directly connected, FastEthernet1/1
S       10.1.0.0/16 [95/0] via 10.0.3.2
```
34)	Verify connectivity between PC1 and PC3.
```
C:\>ping 10.1.2.10

Pinging 10.1.2.10 with 32 bytes of data:

Reply from 10.1.2.10: bytes=32 time<1ms TTL=125
Reply from 10.1.2.10: bytes=32 time=1ms TTL=125
Reply from 10.1.2.10: bytes=32 time<1ms TTL=125
Reply from 10.1.2.10: bytes=32 time<1ms TTL=125

Ping statistics for 10.1.2.10:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms
```
35)	Verify the traffic goes via R5.
```
C:\>tracert 10.1.2.10

Tracing route to 10.1.2.10 over a maximum of 30 hops: 

  1   0 ms      7 ms      0 ms      10.0.1.1
  2   0 ms      0 ms      0 ms      10.0.3.2
  3   0 ms      0 ms      0 ms      10.1.3.1
  4   5 ms      0 ms      0 ms      10.1.2.10

Trace complete.
```
36)	Bring interface FastEthernet 0/0 on R2 back up.
```
R2(config)#int f0/0
R2(config-if)#no shut
```
37)	Enter the commands below on R5 to provision a basic EIGRP configuration and enable EIGRP on every interface.
```
R5(config)#router eigrp 100 
R5(config-router)#no auto-summary
R5(config-router)#network 10.0.0.0 0.255.255.255
```
## **[The End]**

## **Loopback Interfaces**

38)	Configure loopback interface 0 on each router. Assign the IP address 192.168.0.x/32, where ‘x’ is the router number (for example 192.168.0.3/32 on R3.)
```
R1(config)#int loopback 0
R1(config-if)#ip address 192.168.0.1 255.255.255.255
R2(config)#int loopback 0
R2(config-if)#ip address 192.168.0.2 255.255.255.255
R3(config)#int loopback 0
R3(config-if)#ip address 192.168.0.3 255.255.255.255
R4(config)#int loopback 0
R4(config-if)#ip address 192.168.0.4 255.255.255.255
R5(config)#int loopback 0
R5(config-if)#ip address 192.168.0.5 255.255.255.255
```
39)	Is there connectivity to the loopback interfaces from the PCs? Why or why not?
- There is no connectivity from the PCs to the loopback interfaces because they are not in the routing tables of the routers (apart from the local loopback interface on each router). The loopback interfaces are not in the routing tables because they are in the 192.168.0.0/24 range which has not been included in the routing protocol.
```
R1#sh ip route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 13 subnets, 3 masks
C       10.0.0.0/24 is directly connected, FastEthernet0/0
L       10.0.0.1/32 is directly connected, FastEthernet0/0
C       10.0.1.0/24 is directly connected, FastEthernet0/1
L       10.0.1.1/32 is directly connected, FastEthernet0/1
C       10.0.2.0/24 is directly connected, FastEthernet1/0
L       10.0.2.1/32 is directly connected, FastEthernet1/0
C       10.0.3.0/24 is directly connected, FastEthernet1/1
L       10.0.3.1/32 is directly connected, FastEthernet1/1
S       10.1.0.0/16 [95/0] via 10.0.3.2
D       10.1.0.0/24 [90/30720] via 10.0.0.2, 00:06:45, FastEthernet0/0
D       10.1.1.0/24 [90/33280] via 10.0.0.2, 00:06:45, FastEthernet0/0
D       10.1.2.0/24 [90/35840] via 10.0.0.2, 00:06:45, FastEthernet0/0
D       10.1.3.0/24 [90/261120] via 10.0.3.2, 00:05:52, FastEthernet1/1
     192.168.0.0/32 is subnetted, 1 subnets
C       192.168.0.1/32 is directly connected, Loopback0 ✅
```
40)	Enter the commands below on each router to include the loopback interfaces in EIGRP.
```
R1(config)#router eigrp 100
R1(config-router)#network 192.168.0.0 0.0.0.255
```
41)	Verify the loopback interfaces are in the routing table on R1.
```
R1#sh ip route eigrp
     10.0.0.0/8 is variably subnetted, 13 subnets, 3 masks
D       10.1.0.0/24 [90/30720] via 10.0.0.2, 00:08:47, FastEthernet0/0
D       10.1.1.0/24 [90/33280] via 10.0.0.2, 00:08:47, FastEthernet0/0
D       10.1.2.0/24 [90/35840] via 10.0.0.2, 00:08:47, FastEthernet0/0
D       10.1.3.0/24 [90/261120] via 10.0.3.2, 00:07:54, FastEthernet1/1
     192.168.0.0/32 is subnetted, 5 subnets
D       192.168.0.2 [90/156160] via 10.0.0.2, 00:00:47, FastEthernet0/0
D       192.168.0.3 [90/158720] via 10.0.0.2, 00:00:40, FastEthernet0/0
D       192.168.0.4 [90/161280] via 10.0.0.2, 00:00:33, FastEthernet0/0
D       192.168.0.5 [90/386560] via 10.0.3.2, 00:00:26, FastEthernet1/1
```
42)	Verify connectivity from PC1 to the loopback interface on R5.
```
C:\>ping 192.168.0.5

Pinging 192.168.0.5 with 32 bytes of data:

Reply from 192.168.0.5: bytes=32 time<1ms TTL=254
Reply from 192.168.0.5: bytes=32 time=1ms TTL=254
Reply from 192.168.0.5: bytes=32 time=1ms TTL=254
Reply from 192.168.0.5: bytes=32 time=14ms TTL=254

Ping statistics for 192.168.0.5:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 14ms, Average = 4ms
```
## **[The End]**

## **Adjacencies and Passive Interfaces**

43)	Enter the command below to verify that R1 has established EIGRP adjacencies with R2 and R5.
```
R1#sh ip eigrp neighbors 
IP-EIGRP neighbors for process 100
H   Address         Interface      Hold Uptime    SRTT   RTO   Q   Seq
                                   (sec)          (ms)        Cnt  Num
0   10.0.0.2        Fa0/0          13   00:10:39  40     1000  0   38
1   10.0.3.2        Fa1/1          14   00:09:45  40     1000  0   29
```
44)	Verify that traffic from R5 to the directly connected interfaces on R1 goes via the FastEthernet 0/1 interface.
```
R5#sh ip route eigrp
     10.0.0.0/8 is variably subnetted, 12 subnets, 3 masks
D       10.0.0.0/24 [90/261120] via 10.0.3.1, 00:10:54, FastEthernet0/1 ✅
D       10.0.1.0/24 [90/261120] via 10.0.3.1, 00:10:54, FastEthernet0/1 ✅
D       10.0.2.0/24 [90/261120] via 10.0.3.1, 00:10:54, FastEthernet0/1 ✅
D       10.1.0.0/24 [90/263680] via 10.1.3.1, 00:10:54, FastEthernet0/0
                    [90/263680] via 10.0.3.1, 00:10:54, FastEthernet0/1
D       10.1.1.0/24 [90/261120] via 10.1.3.1, 00:10:54, FastEthernet0/0
D       10.1.2.0/24 [90/261120] via 10.1.3.1, 00:10:54, FastEthernet0/0
     192.168.0.0/32 is subnetted, 5 subnets
D       192.168.0.1 [90/386560] via 10.0.3.1, 00:03:54, FastEthernet0/1
D       192.168.0.2 [90/389120] via 10.0.3.1, 00:03:47, FastEthernet0/1
D       192.168.0.3 [90/389120] via 10.1.3.1, 00:03:40, FastEthernet0/0
D       192.168.0.4 [90/386560] via 10.1.3.1, 00:03:33, FastEthernet0/0
```
45)	Enter the commands below to configure the loopback interface and the link to R5 as passive interfaces on R1.
```
R1(config-if)#router eigrp 100
R1(config-router)#passive-interface loopback0 
R1(config-router)#passive-interface fastethernet1/1
```
46)	What changes do you expect to see in the routing table on R5 and why?
- The EIGRP adjacency between R1 and R5 will go down. All EIGRP routes via R1 will be removed from the routing table and replaced with routes via R4.
Configuring the loopback interface as a passive interface on R1 does not affect any routing tables but is a best practice.
```
%DUAL-5-NBRCHANGE: IP-EIGRP 100: Neighbor 10.0.3.2 (FastEthernet1/1) is down: holding time expired
```
47)	Verify the expected changes to the routing table on R5.
```
R5#sh ip route eigrp
%DUAL-5-NBRCHANGE: IP-EIGRP 100: Neighbor 10.0.3.1 (FastEthernet0/1) is down: K-value mismatch

     10.0.0.0/8 is variably subnetted, 12 subnets, 3 masks
D       10.0.0.0/24 [90/266240] via 10.1.3.1, 00:14:30, FastEthernet0/0 ✅
D       10.0.1.0/24 [90/268800] via 10.1.3.1, 00:14:30, FastEthernet0/0 ✅
D       10.0.2.0/24 [90/268800] via 10.1.3.1, 00:14:30, FastEthernet0/0 ✅
D       10.1.0.0/24 [90/263680] via 10.1.3.1, 00:14:30, FastEthernet0/0
D       10.1.1.0/24 [90/261120] via 10.1.3.1, 00:14:30, FastEthernet0/0
D       10.1.2.0/24 [90/261120] via 10.1.3.1, 00:14:30, FastEthernet0/0
     192.168.0.0/32 is subnetted, 5 subnets
D       192.168.0.1 [90/394240] via 10.1.3.1, 00:07:30, FastEthernet0/0 ✅
D       192.168.0.2 [90/391680] via 10.1.3.1, 00:07:23, FastEthernet0/0
D       192.168.0.3 [90/389120] via 10.1.3.1, 00:07:16, FastEthernet0/0
D       192.168.0.4 [90/386560] via 10.1.3.1, 00:07:09, FastEthernet0/0
```
## **[The End]**