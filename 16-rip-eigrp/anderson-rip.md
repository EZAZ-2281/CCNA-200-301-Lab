# RIP Configuration (Source: Udemy)
## Instructor: Neil Anderson  
### **Pkt file:** [Here](https://mega.nz/file/64p2kS4S#l3Ev9ykxUYV6TMuBZLovT13z_FiLZa0qwZF2-QaxWYk)
### Scenario: 
![](../images/anrip.PNG)

## **RIP Configuration**

1)	Enable RIPv2 on every router. Ensure all networks except 203.0.113.0/24 are advertised. Do not perform any summarisation.
- Do it from R1 to R5: 
```
R1(config)#router rip
R1(config-router)#version 2
R1(config-router)#no auto-summary
R1(config-router)#network 10.0.0.0 
```
2)	Verify all networks are in the router’s routing tables.
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
R       10.1.0.0/24 [120/1] via 10.0.0.2, 00:00:17, FastEthernet0/0
R       10.1.1.0/24 [120/2] via 10.0.0.2, 00:00:17, FastEthernet0/0
                    [120/2] via 10.0.3.2, 00:00:20, FastEthernet1/1
R       10.1.2.0/24 [120/2] via 10.0.3.2, 00:00:20, FastEthernet1/1
R       10.1.3.0/24 [120/1] via 10.0.3.2, 00:00:20, FastEthernet1/1
```
3)	Verify that routing is working by checking that PC1 has connectivity to PC3.
- From PC1 to PC3. 
```
C:\>ping 10.1.2.10

Pinging 10.1.2.10 with 32 bytes of data:

Request timed out.
Reply from 10.1.2.10: bytes=32 time=1ms TTL=125
Reply from 10.1.2.10: bytes=32 time<1ms TTL=125
Reply from 10.1.2.10: bytes=32 time<1ms TTL=125

Ping statistics for 10.1.2.10:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms
```
4)	Ensure that all routers have a route to the 203.0.113.0/24 network. Internal routes must not advertised to the Service Provider at 203.0.113.2.
- The 203.0.113.0/24 network must be added to the RIP process on R4, and interface FastEthernet 1/1 facing the service provider configured as a passive interface to avoid sending out internal network information.
```
R4(config)#router rip
R4(config-router)#network 203.0.113.0
R4(config-router)#passive-interface f1/1
```
5)	Verify that all routers have a path to the 203.0.113.0/24 network.
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
R       10.1.0.0/24 [120/1] via 10.0.0.2, 00:00:00, FastEthernet0/0
R       10.1.1.0/24 [120/2] via 10.0.0.2, 00:00:00, FastEthernet0/0
                    [120/2] via 10.0.3.2, 00:00:13, FastEthernet1/1
R       10.1.2.0/24 [120/2] via 10.0.3.2, 00:00:13, FastEthernet1/1
R       10.1.3.0/24 [120/1] via 10.0.3.2, 00:00:13, FastEthernet1/1
R    203.0.113.0/24 [120/2] via 10.0.3.2, 00:00:13, FastEthernet1/1 ✅
```
6)	Configure a default static route on R4 to the Internet via the service provider at 203.0.113.2
```
R4(config)#ip route 0.0.0.0 0.0.0.0 203.0.113.2
```
7)	Ensure that all other routers learn via RIP how to reach the Internet.
- Inject the default static route into RIP on R4.
```
R4(config)#router rip
R4(config-router)#default-information originate
```
8)	Verify all routers have a route to the Internet.
```
R1#sh ip route

Gateway of last resort is 10.0.3.2 to network 0.0.0.0 ✅

     10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C       10.0.0.0/24 is directly connected, FastEthernet0/0
L       10.0.0.1/32 is directly connected, FastEthernet0/0
C       10.0.1.0/24 is directly connected, FastEthernet0/1
L       10.0.1.1/32 is directly connected, FastEthernet0/1
C       10.0.2.0/24 is directly connected, FastEthernet1/0
L       10.0.2.1/32 is directly connected, FastEthernet1/0
C       10.0.3.0/24 is directly connected, FastEthernet1/1
L       10.0.3.1/32 is directly connected, FastEthernet1/1
R       10.1.0.0/24 [120/1] via 10.0.0.2, 00:00:22, FastEthernet0/0
R       10.1.1.0/24 [120/2] via 10.0.0.2, 00:00:22, FastEthernet0/0
                    [120/2] via 10.0.3.2, 00:00:09, FastEthernet1/1
R       10.1.2.0/24 [120/2] via 10.0.3.2, 00:00:09, FastEthernet1/1
R       10.1.3.0/24 [120/1] via 10.0.3.2, 00:00:09, FastEthernet1/1
R    203.0.113.0/24 [120/2] via 10.0.3.2, 00:00:09, FastEthernet1/1
R*   0.0.0.0/0 [120/2] via 10.0.3.2, 00:00:09, FastEthernet1/1 ✅
```
## **[The End]**