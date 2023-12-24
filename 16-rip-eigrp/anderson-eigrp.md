# EIGRP Configuration (Source: Udemy)
## Instructor: Neil Anderson  
### **Pkt file:** [Here](https://mega.nz/file/64p2kS4S#l3Ev9ykxUYV6TMuBZLovT13z_FiLZa0qwZF2-QaxWYk)
### Scenario: 
![](../images/anrip.PNG)

## **EIGRP Configuration**

### **1) Enable EIGRP AS 100 on every router. Ensure all networks except 203.0.113.0/24 are advertised in EIGRP.**
- Do it on every router  
```
R1(config)#router eigrp 100
R1(config-router)#network 10.0.0.0
```
### **2) Verify the routers have formed adjacencies with each other.**
```
R1#sh ip route

Gateway of last resort is 10.0.3.2 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C       10.0.0.0/24 is directly connected, FastEthernet0/0
L       10.0.0.1/32 is directly connected, FastEthernet0/0
C       10.0.1.0/24 is directly connected, FastEthernet0/1
L       10.0.1.1/32 is directly connected, FastEthernet0/1
C       10.0.2.0/24 is directly connected, FastEthernet1/0
L       10.0.2.1/32 is directly connected, FastEthernet1/0
C       10.0.3.0/24 is directly connected, FastEthernet1/1
L       10.0.3.1/32 is directly connected, FastEthernet1/1
D       10.1.0.0/24 [90/30720] via 10.0.0.2, 00:01:45, FastEthernet0/0
D       10.1.1.0/24 [90/33280] via 10.0.0.2, 00:01:33, FastEthernet0/0
D       10.1.2.0/24 [90/35840] via 10.0.0.2, 00:01:22, FastEthernet0/0
D       10.1.3.0/24 [90/261120] via 10.0.3.2, 00:01:14, FastEthernet1/1
R    203.0.113.0/24 [120/2] via 10.0.3.2, 00:00:15, FastEthernet1/1
R*   0.0.0.0/0 [120/2] via 10.0.3.2, 00:00:15, FastEthernet1/1
```
### **3) Which routing protocol (RIP or EIGRP) do you expect routes to the 10.x.x.x networks to be learned from in the routing tables?**
- Both RIP and EIGRP are advertising the 10.x.x.x networks. EIGRP has a better (lower) administrative distance of 90 compared to RIP’s AD of 120, so the EIGRP routes will be installed in the router’s routing tables.
### **4) Do you expect to see any routes from the other routing protocol in the routing tables?**
- Only RIP (not EIGRP) is advertising the 203.0.113.0/24 network and injecting the default static route. Those routes will remain unchanged in the routing tables.
### **5) View the routing tables to verify your answers.**
```
R1#sh ip route

Gateway of last resort is 10.0.3.2 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C       10.0.0.0/24 is directly connected, FastEthernet0/0
L       10.0.0.1/32 is directly connected, FastEthernet0/0
C       10.0.1.0/24 is directly connected, FastEthernet0/1
L       10.0.1.1/32 is directly connected, FastEthernet0/1
C       10.0.2.0/24 is directly connected, FastEthernet1/0
L       10.0.2.1/32 is directly connected, FastEthernet1/0
C       10.0.3.0/24 is directly connected, FastEthernet1/1
L       10.0.3.1/32 is directly connected, FastEthernet1/1
D       10.1.0.0/24 [90/30720] via 10.0.0.2, 00:01:45, FastEthernet0/0
D       10.1.1.0/24 [90/33280] via 10.0.0.2, 00:01:33, FastEthernet0/0
D       10.1.2.0/24 [90/35840] via 10.0.0.2, 00:01:22, FastEthernet0/0
D       10.1.3.0/24 [90/261120] via 10.0.3.2, 00:01:14, FastEthernet1/1
R    203.0.113.0/24 [120/2] via 10.0.3.2, 00:00:15, FastEthernet1/1 ✅
R*   0.0.0.0/0 [120/2] via 10.0.3.2, 00:00:15, FastEthernet1/1 ✅
```
## **[The End]**