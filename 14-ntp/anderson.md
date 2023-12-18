# NTP Network Time Protocol (Source: Udemy)
## Instructor: Neil Anderson  
### **Pkt file:** [Here](https://mega.nz/file/L0QE1bKA#BS0Ve81wMMMdaBamOUpNQ921VYiLObo-nWEA_g6cA6k)
### Scenario: 
![](../images/ntpna.PNG)
### **1. Configure R1 to synchronise its time with the NTP server at 10.0.1.100. Set the timezone as Pacific Standard Time which is 8 hours before UTC.**
```
R1(config)#clock timezone PST -6
R1(config)#ntp server 10.0.1.100
```
### **2. Check the current time on the router and verify it is synchronised with the NTP server.**
```
R1(config)#do sh clock
*18:3:16.449 PST Sun Feb 28 1993
R1(config)#do sh ntp status
Clock is unsynchronized ✅, stratum 16, no reference clock
nominal freq is 250.0000 Hz, actual freq is 249.9990 Hz, precision is 2**24
reference time is 00000000.00000000 (00:00:00.000 UTC Mon Jan 1 1990)
clock offset is 0.00 msec, root delay is 0.00  msec
root dispersion is 0.00 msec, peer dispersion is 0.00 msec.
loopfilter state is 'FSET' (Drift set from file), drift is - 0.000001193 s/s system poll interval is 4, never updated.
```
- Do fast-forward  
```
R1(config)#do sh ntp status
Clock is synchronized ✅, stratum 16, reference is 10.0.1.100
nominal freq is 250.0000 Hz, actual freq is 249.9990 Hz, precision is 2**24
reference time is 22E47AF9.00000077 (17:18:49.119 UTC Tue Oct 6 2054)
clock offset is 0.00 msec, root delay is 0.00  msec
root dispersion is 17.70 msec, peer dispersion is 0.12 msec.
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is - 0.000001193 s/s system poll interval is 4, last update was 8 sec ago.
```
## **[The End]**