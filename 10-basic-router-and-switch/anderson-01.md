# Switch Management (Source: Udemy)
## Instructor: Neil Anderson  
### **Pkt file:** [Here](https://mega.nz/file/L0QE1bKA#BS0Ve81wMMMdaBamOUpNQ921VYiLObo-nWEA_g6cA6k)
### Scenario: 
![](../images/ntpna.PNG)

### **1. Configure SW2 with IP address 10.0.1.50 for management on VLAN 1. Ensure the switch has connectivity to other IP subnets.**

```
SW2(config)#int vlan 1
SW2(config-if)#ip address 10.0.1.50 255.255.255.0 
SW2(config-if)#no shut
SW2(config-if)#ip default-gateway 10.0.1.1
```
```
(Note that it is best practice to NOT use VLAN 1 for any production traffic in a real world network and we would normally have a separate dedicated IP subnet for management traffic. We are using VLAN 1 in our lab environment to simplify the topology).
```
- Also watch this video if necessary:  

https://github.com/EZAZ-2281/CCNA-200-301-Lab/assets/81481142/a47a7761-2f3c-41d9-9094-b39c9951ba12


## **[The End]**