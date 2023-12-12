# QoS Configuration Part-02
## Source (YouTube: Jeremy's IT Lab)
### Video Link: [Here](https://youtu.be/63tD4t8189k?si=oQK6IguLPbxouMuF)
### Lab File Link (pkt): [Here Day-47](https://mega.nz/file/i95nyBJI#ynX5AUZdiLfU93FeD5vk5oZdVGd1AHgfewEvtEbRZyg)
### Scenario:
![](../images/qosp-02.PNG)

```
Watch the video and follow along in the CLI to learn some basic QoS configurations.
*QoS configuration isn't a CCNA exam topic. 

Configure the following QoS settings on R1
and apply them outbound on interface G0/0/0:

1. Mark HTTPS traffic as AF31
*--Provide minimum 10% bandwidth as a priority queue

2. Mark HTTP traffic as AF32
*--Provide minimum 10% bandwidth

3. Mark ICMP traffic as CS2
*--Provide minimum 5% bandwidth

4. Use simulation mode to view the DSCP markings of packets:
   -when pinging jeremysitlab.com from PC1
   -when accessing jeremysitlab.com from PC1 via HTTP
   -when accessing jeremysitlab.com from PC1 via HTTPS
 
```
```
R1(config)#class-map https_map
R1(config-cmap)#match protocol https
R1(config-cmap)#class-map http_map
R1(config-cmap)#match protocol http
R1(config-cmap)#class-map icmp_map
R1(config-cmap)#match protocol icmp
```

```
R1#sh run | section class-map
class-map match-all https_map
 match protocol https
class-map match-all http_map
 match protocol http
class-map match-all icmp_map
 match protocol icmp
```
```
R1(config)#policy-map g0/0/0_out
R1(config-pmap)#class https_map
R1(config-pmap-c)#set ip dscp AF31
R1(config-pmap-c)#priority percent 10

R1(config-pmap-c)#class http_map
R1(config-pmap-c)#set ip dscp AF32
R1(config-pmap-c)#bandwidth percent 10

R1(config-pmap-c)#class icmp_map
R1(config-pmap-c)#set ip dscp cs2
R1(config-pmap-c)#bandwidth percent 5
```
```
R1(config)#int g0/0/0
R1(config-if)#service-policy output g0/0/0_out
```
```
R1#sh run 
interface GigabitEthernet0/0/0
 ip address 172.16.0.1 255.255.255.252
 service-policy output g0/0/0_out
 duplex auto
 speed auto
```
> Now turn on the simulation mode first  

Now to go pc1 and do it:  
```  
ping 10.0.0.100
```
> Check the packet when it arrives to R1. check inbound and outbound pdus. 

Now fo to pc1 browser and do it: 
```
https://10.0.0.100
```
> Check the packet when it arrives to R1. check inbound and outbound pdus. 

Now again go to pc1 browser and do ti: 
```
http://10.0.0.100
```
> Check the packet when it arrives to R1. check inbound and outbound pdus. 
## **[The End]**
