# Porst Security Configuratoin
## Source (Udemy: David Bombal Course)

### Lab File Link (pkt): [Here](https://mega.nz/file/SlgCUDrJ#e1vMV8hILSUDGZcNNu2WFO84ubyS70eQWvKQpZVxCJs)
### Scenario: 
![](../images/dbps.PNG)
## **Configure Port Security as follows:**
# **1) Use just one port security command on G1/0/1**
```
Questions: 
a. By default, how MAC addresses are permitted?
b. Verify that only the first host is allowed.
c. What happens when the second host sends traffic into the network?
d. Is the first host's MAC address written to the running configuration?
e. What happens when the switch is power cycled (save config first)? 
```
### **a. By default, how MAC addresses are permitted?**
```
One MAC address.
```
### **b. Verify that only the first host is allowed.**
> By default PC1 and PC2 ip address is set to dhcp. First change it to static. Otherwise when we enable port-Security which PC receive dhcp message first port security will store that pc's mac address. But we want only the PC1 will be receive the ip address via dhcp. So that's why we manually set the ip configuration is static.    
```
SW1#sh port-security
Secure Port MaxSecureAddr CurrentAddr SecurityViolation Security Action
               (Count)       (Count)        (Count)
--------------------------------------------------------------------
     Gig1/0/1        1          0                 0         Shutdown 🟩
----------------------------------------------------------------------
```
```
SW1#sh port-security int g1/0/1
Port Security              : Enabled
Port Status                : Secure-up 🟩
Violation Mode             : Shutdown 🟩
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1 🟩
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 0000.0000.0000:0 🟩
Security Violation Count   : 0
```
```
SW1#sh port-security address
               Secure Mac Address Table
-----------------------------------------------------------------------------
Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                   (mins)
----    -----------       ----                          -----   -------------
-----------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 0
Max Addresses limit in System (excluding one mac per port) : 1024
```
> Now go to PC1 and set the ip configuration as dhcp. Then go to pc1 command prompt.   
```
C:\>ipconfig /renew

   IP Address......................: 10.1.1.5
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: 10.1.1.254
   DNS Server......................: 10.1.1.254
```
> The upper command we see that pc1 is receive a ip address via dhcp. So the sw must store the pc1 mac-address (00C0.1111.1111) for port-security.   
```
SW1#sh port-security address
               Secure Mac Address Table
-----------------------------------------------------------------------------
Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                   (mins)
----    -----------       ----                          -----   -------------
1	00C0.1111.1111	DynamicConfigured	GigabitEthernet1/0/1		-
-----------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 0
Max Addresses limit in System (excluding one mac per port) : 1024
```
```
SW1#sh port-sec int g1/0/1
Port Security              : Enabled
Port Status                : Secure-up 🟩
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 1
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 00C0.1111.1111:1 🟩
Security Violation Count   : 0
```
> Now this time if I want to go to PC2 and set the ip configuration to dhcp and try to allocate a new ip address via dhcp the dhcp request message will be failed. Because the maximum mac-address is 1 and already this is pc1 mac-address. That's why pc2's dhcp message is failed and store the mac-address of pc2 as a violating mac-address. Notice the Porst Status is: **Secure-shutdown**
```
C:\>ipconfig /renew
DHCP request failed. 
```
```
SW1#sh port-sec int g1/0/1
Port Security              : Enabled
Port Status                : Secure-shutdown 🟩
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 00C0.2222.2222:1 🟩
Security Violation Count   : 1 🟩
```
### **c. What happens when the second host sends traffic into the network?**
> The interface will be shutdown.  
```
SW1#sh port-sec 
Secure Port MaxSecureAddr CurrentAddr SecurityViolation Security Action
               (Count)       (Count)        (Count)
--------------------------------------------------------------------
     Gig1/0/1        1          0                 1         Shutdown 🟩
----------------------------------------------------------------------
```
### **d. Is the first host's MAC address written to the running configuration?**
### **The answer is: NO.**
> The g1/0/1 is now shutdown state. Go to pc2 and set the ip config to static and then go to sw1 and issue this command:  
```
SW1(config)#int g1/0/1
SW1(config-if)#shut
SW1(config-if)#no shut
```
> From PC1:  
```
C:\>ipconfig /renew

   IP Address......................: 10.1.1.5
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: 10.1.1.254
   DNS Server......................: 10.1.1.254
```
```
SW1#sh port-sec address
               Secure Mac Address Table
-----------------------------------------------------------------------------
Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                   (mins)
----    -----------       ----                          -----   -------------
1	00C0.1111.1111	DynamicConfigured	GigabitEthernet1/0/1		-
-----------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 0
Max Addresses limit in System (excluding one mac per port) : 1024
```
> Now save the configuration file and reload the switch and check the running configuration:  
```
SW1#wr
Building configuration...
Compressed configuration from 7383 bytes to 3601 bytes[OK]
[OK]
SW1#reload
```
```
SW1#sh run 
interface GigabitEthernet1/0/1
 switchport mode access
 switchport port-security
```
```
SW1#sh port-sec int g1/0/1
Port Security              : Enabled
Port Status                : Secure-up 🟩
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 0000.0000.0000:0 🟩
Security Violation Count   : 0
```
```
SW1#sh port-sec add
               Secure Mac Address Table
-----------------------------------------------------------------------------
Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                   (mins)
----    -----------       ----                          -----   -------------
-----------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 0
Max Addresses limit in System (excluding one mac per port) : 1024
```
> So we can see that all the configuration was removed. Now if we turn on the dhcp of pc2 first then the sw1 will store the mac address of pc2. 

### **e. What happens when the switch is power cycled (save config first)?**
```
A different mac-address could be learnt. We are not restricting mac-address on this port we are only restricting the number of mac-address allowed on that port. If the switch reboots then a different mac-address could be learnt on that port and the original mac-address coule be seen as a violating mac-address.
```
# **2) Enable port security on G1/0/3 and automatically add the MAC address to the running configuration.**
```
Question: 
-> What happens when the second host sends traffic?
-> What happens when the switch is power cycled (don't save config)? 
```
### **First configure port-Security using mac-address sticky.**
```
SW1(config)#int g1/0/3
SW1(config-if)#switchport mode access
SW1(config-if)#switchport port-security 
SW1(config-if)#switchport port-security mac-address sticky
```
```
SW1(config-if)#do sh port-sec
Secure Port MaxSecureAddr CurrentAddr SecurityViolation Security Action
               (Count)       (Count)        (Count)
--------------------------------------------------------------------
     Gig1/0/3        1          0                 0         Shutdown
----------------------------------------------------------------------
```
```
SW1(config-if)#do sh port-sec int g1/0/3
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 0000.0000.0000:0
Security Violation Count   : 0
```
### **Now save the running-configuration file to the startup-configuration.**
```
SW1#wr
```
```
SW1#sh run 
interface GigabitEthernet1/0/3
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
```
### **Now allocate IP address.** 
> Go to PC3:  
```
C:\>ipconfig /renew

   IP Address......................: 10.1.1.7
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: 10.1.1.254
   DNS Server......................: 10.1.1.254
```
```
SW1(config-if)#do sh port-sec int g1/0/3
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 1
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 1
Last Source Address:Vlan   : 00C0.3333.3333:1 🟩
Security Violation Count   : 0
```
```
SW1#sh run 
interface GigabitEthernet1/0/3
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 switchport port-security mac-address sticky 00C0.3333.3333
!
```
### **Now do some violation.**
> Now go to PC4:  
```
C:\>ipconfig /renew
DHCP request failed. 
```
```
SW1(config-if)#do sh port-sec int g1/0/3
Port Security              : Enabled
Port Status                : Secure-shutdown
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 1
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 1
Last Source Address:Vlan   : 00C0.4444.4444:1
Security Violation Count   : 1 🟩
```
### **Notice the port is shut-down because of the violation.** 
```
SW1#sh run 
interface GigabitEthernet1/0/3
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 switchport port-security mac-address sticky 00C0.3333.3333
 shutdown 🟩
!
```
### **Now up the port by removing the sticky mac-address.**
```
SW1(config-if)#int g1/0/3
SW1(config-if)#no switchport port-security mac-address sticky 00C0.3333.3333
SW1(config-if)#shut
SW1(config-if)#no shut
```
```
SW1#sh run 
interface GigabitEthernet1/0/3
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 🟩
!
```
### **Now this time if we want to allocate ip for the PC4 this mac address will be added in the running-configuration file.**
> Now go to PC4:  
```
C:\>ipconfig /renew

   IP Address......................: 10.1.1.5
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: 10.1.1.254
   DNS Server......................: 10.1.1.254
```
```
SW1#sh run 
interface GigabitEthernet1/0/3
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 switchport port-security mac-address sticky 00C0.4444.4444 🟩
```
```
SW1(config-if)#int g1/0/3
SW1(config-if)#no switchport port-security mac-address sticky 00C0.4444.4444
SW1(config-if)#shut
SW1(config-if)#no shut
```
> Now go to PC3:  
```
C:\>ipconfig /renew

   IP Address......................: 10.1.1.7
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: 10.1.1.254
   DNS Server......................: 10.1.1.254
```
```
SW1#sh run 
interface GigabitEthernet1/0/3
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 switchport port-security mac-address sticky 00C0.3333.3333
```
```
SW1#wr 
SW1#sh start 
interface GigabitEthernet1/0/3
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 switchport port-security mac-address sticky 00C0.3333.3333
```
```
SW1#reload 
SW1#sh run 
interface GigabitEthernet1/0/3
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 switchport port-security mac-address sticky 00C0.3333.3333
```
```
So what's the point of using Sticky Mac addresses? What does it do and what doesn't do? Well, Sticky Mac addresses save you the trouble of finding the Mac addresses of devices and writing that to the running configuration on the last port. We're going to have to manually configure the Mac addresses. So that means that you need to find out what the Mac addresses are of your devices.Sticky saves you that trouble because it automatically learns the Mac address and then writes it to the running configuration. But that means that the first device that sends traffic into the network needs to be the device that you trust. In other words, it needs to be the device that you want connected to that port. If another device sends traffic, that device's Mac address will be written to the running configuration and not the Mac address of the device that you actually want on that port. Secondly, sticky configuration is written to the running config of the switch. You have to save the switch configuration if you want to make that permanent.
```


# **3) Enable port security on G1/0/4 by manually specifying PC5's MAC address. Drop other traffic and send log messages when a violation occurs.**
```
SW1(config)#int g1/0/4
SW1(config-if)#switchport mode access
SW1(config-if)#switchport port-security 
SW1(config-if)#switchport port-security violation restrict
SW1(config-if)#switchport port-security mac-address 00C0.5555.5555
```
```
SW1#sh run 
interface GigabitEthernet1/0/4
 switchport mode access
 switchport port-security
 switchport port-security violation restrict  🟩
 switchport port-security mac-address 00C0.5555.5555 🟩
!
```
> Go to PC5:  
```
C:\>ipconfig /renew

   IP Address......................: 10.1.1.4
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: 10.1.1.254
   DNS Server......................: 10.1.1.254
```
```
SW1#sh port-security int g1/0/4
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Restrict
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1 🟩
Total MAC Addresses        : 1 🟩
Configured MAC Addresses   : 1
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 00C0.5555.5555:1 🟩
Security Violation Count   : 0 🟩
```
Go to PC6:    
```
C:\>ipconfig /renew
DHCP request failed. 
```
```
SW1#sh port-security int g1/0/4
Port Security              : Enabled
Port Status                : Secure-up 🟩
Violation Mode             : Restrict
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 1
Configured MAC Addresses   : 1
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 00C0.6666.6666:1
Security Violation Count   : 2 🟩
```

# **4) Increase the number of devices allowed on G1/0/1 to 2 and verify that both PCs receive IP addresses from the DHCP server.**
```
SW1(config)#int g1/0/1
SW1(config-if)#switchport port-security
SW1(config-if)#switchport port-security maximum 2
SW1(config-if)#shut
SW1(config-if)#no shut
```
```
SW1(config-if)#do sh port-sec
Secure Port MaxSecureAddr CurrentAddr SecurityViolation Security Action
               (Count)       (Count)        (Count)
--------------------------------------------------------------------
     Gig1/0/1        2🟩        0                 0         Shutdown
     Gig1/0/3        1          1                 0         Shutdown
     Gig1/0/4        1          1                 0         Restrict
----------------------------------------------------------------------
```
> Now go to PC1 and PC2 and issue this same command in both PC.  
```
C:\>ipconfig /renew 
```
> Now check the port-security status of that interface  
```
SW1(config-if)#do sh port-sec int g1/0/1
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 2 🟩
Total MAC Addresses        : 2 🟩
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 00C0.2222.2222:1 🟩
Security Violation Count   : 0 🟩
```
```
SW1(config-if)#do sh port-sec
Secure Port MaxSecureAddr CurrentAddr SecurityViolation Security Action
               (Count)       (Count)        (Count)
--------------------------------------------------------------------
     Gig1/0/1        2          2                 0         Shutdown 🟩
     Gig1/0/3        1          1                 0         Shutdown
     Gig1/0/4        1          1                 0         Restrict
----------------------------------------------------------------------
```
```
SW1#sh run 
interface GigabitEthernet1/0/1
 switchport mode access
 switchport port-security
 switchport port-security maximum 2
```
```
SW1#wr
```
> Now go to PC1 and change its mac address and issue ipconfig /renew command then check the violation status:  
```
C:\>ipconfig /renew 
DHCP request failed. 
```
```
SW1#sh port-security int g1/0/1
Port Security              : Enabled
Port Status                : Secure-shutdown 🟩
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 2 🟩
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 00C0.1111.9999:1 🟩
Security Violation Count   : 1 🟩
```

## **[The End]**
 