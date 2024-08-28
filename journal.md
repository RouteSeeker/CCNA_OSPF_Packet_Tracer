# OSPF Lab Configuration

![initial Setup](https://github.com/RouteSeeker/CCNA_OSPF_Packet_Tracer/blob/main/assets/screenshots/VOIP_OSPF/01.Initial.PNG)

This journal documents the step-by-step configuration of an OSPF lab using Cisco routers and switches in Packet Tracer. The lab setup includes three routers, multiple VLANs, and telephony services.

## Lab Setup
### Devices
#### Routers:
R1: Cisco 2811 (Telephony enabled)
R2: Cisco 2811 (Telephony enabled)
R3: Cisco 4331
#### Switches:
SW1: Cisco 2960 (Connected to R1)
SW2: Cisco 2960 (Connected to R2)
SW3: Cisco 2960 (Connected to R3)
#### Phones and PCs:
SW1: 4 iPhones (with PCs connected to each), 6 PCs (3 in OPERATIONS VLAN, 3 in FINANCE VLAN)
SW2: 4 iPhones (with PCs connected to each)
SW3: PCs distributed across VLANs

### Network Configuration
R1 (Connected to SW1):

VLAN 50 (SALES): 10.0.50.0/24
VLAN 55 (VOICE): 10.0.55.0/24
R2 (Connected to SW2):

VLAN 40 (MARKETING): 10.0.40.0/24
VLAN 44 (VOICE): 10.0.44.0/24
R3 (Connected to SW3):

VLAN 10 (OPERATIONS): 10.0.10.0/24
VLAN 20 (FINANCE): 10.0.20.0/24

## Configuration Steps
### 1.Router Configuration

The first step involves configuring the three routers with the essential settings. This includes setting hostnames, creating sub-interfaces, and setting up DHCP on each router. Additionally, we will enable telephony services on the two routers connected to IP phones to ensure proper voice communication.

#### R1 Configuration
```
hostname R1
interface Fa0/1
no shut
interface fa0/1.50
encapsulation dot1Q 50
ip address 10.0.50.1 255.255.255.0
exit
interface fa0/1.55
encapsulation dot1Q 55
ip address 10.0.55.1 255.255.255.0
exit

service dhcp
ip dhcp excluded-address 10.0.50.1 10.0.50.10

ip dhcp pool VOICE
network 10.0.55.0 255.255.255.0
default-router 10.0.55.1
option 150 ip 10.0.55.1

ip dhcp pool SALES
network 10.0.50.0 255.255.255.0
default-router 10.0.50.1

telephony-service
max-ephones 10
max-dn 10
auto assign 1 to 10
ip source-address 10.0.55.1 port 2000
exit
ephone-dn 1
number 1001
ephone-dn 2
number 1002
ephone-dn 3
number 1003
ephone-dn 14
number 1004
exit
interface S0/1/0
ip address 203.0.113.1 255.255.255.252
no shutdown
interface S0/1/1
ip address 198.51.100.1 255.255.255.252
no shutdown
```

#### R2 Configuration

```
hostname R2
interface Fa0/1
no shut
interface fa0/1.40
encapsulation dot1Q 40
ip address 10.0.40.1 255.255.255.0
exit
interface fa0/1.44
encapsulation dot1Q 44
ip address 10.0.44.1 255.255.255.0
exit

service dhcp
ip dhcp excluded-address 10.0.40.1 10.0.40.10

ip dhcp pool VOICE
network 10.0.44.0 255.255.255.0
default-router 10.0.44.1
option 150 ip 10.0.44.1

ip dhcp pool MARKETING
network 10.0.40.0 255.255.255.0
default-router 10.0.40.1

telephony-service
max-ephones 10
max-dn 10
auto assign 1 to 10
ip source-address 10.0.44.1 port 2000
exit

ephone-dn 1
number 2001
ephone-dn 2
number 2002
ephone-dn 3
number 2003
ephone-dn 14
number 2004

interface F0/0
ip address 201.85.20.1 255.255.255.252
no shutdown

interface S0/1/0
ip address 203.0.113.2 255.255.255.252
no shutdown
```

#### R3 Configuration

```
hostname R3
interface G0/0/0
no shut
interface G0/0/0.10
encapsulation dot1Q 10
ip address 10.0.10.1 255.255.255.0
exit
interface G0/0/0.20
encapsulation dot1Q 20
ip address 10.0.20.1 255.255.255.0
exit

service dhcp
ip dhcp excluded-address 10.0.10.1 10.0.10.10
ip dhcp excluded-address 10.0.20.1 10.0.20.10

ip dhcp pool OPERATIONS
network 10.0.20.0 255.255.255.0
default-router 10.0.20.1

ip dhcp pool FINANCE
network 10.0.20.0 255.255.255.0
default-router 10.0.20.1

interface G0/0/1
ip address 201.85.20.2 255.255.255.252
no shutdown

interface S0/1/1
ip address 198.51.100.2 255.255.255.252
no shutdown
```
### 2.Switch Configuration
The second step involves configuring the three switches with their respective VLANs. We will also set up the access ports and voice VLANs to ensure proper connectivity for all end devices.

#### SW1 Configuration
```
vlan 50
name SALES
vlan 55
name VOICE-SALES
exit
interface range Fa0/1-4
switchport mode access
switchport access vlan 50
switchport voice vlan 55

interface G0/1
switchport mode trunk

interface vlan 50
ip address 10.0.50.2 255.255.255.0
no shut
```

#### SW2 Configuration

```
vlan
vlan 40
name MARKETING
vlan 44
name VOICE-MARKETING
exit
interface range Fa0/1-4
switchport mode access
switchport access vlan 40
switchport voice vlan 44
```
#### SW3 configuration

```
vlan 10
name OPERATIONS
vlan 20
name FINANCE
exit
interface range Fa0/1-3
switchport mode access
switchport access vlan 10
interface range Fa0/6-8
switchport mode access
switchport access vlan 20
```

![Switch vlan configs](https://github.com/RouteSeeker/CCNA_OSPF_Packet_Tracer/blob/main/assets/screenshots/VOIP_OSPF/02.Switch_VLANs.PNG)

![Switch Trunk](https://github.com/RouteSeeker/CCNA_OSPF_Packet_Tracer/blob/main/assets/screenshots/VOIP_OSPF/03.Switch_Trunk.PNG)

![DHCP confirm](https://github.com/RouteSeeker/CCNA_OSPF_Packet_Tracer/blob/main/assets/screenshots/VOIP_OSPF/04.PC_DHCP.PNG)



### 3.OSPF Configuration

The third step involves configuring OSPF on all routers to advertise their networks. We will also verify that OSPF is functioning correctly and that all routes are properly propagated.

#### R1
```
interface Loopback 0
ip address 1.1.1.1 255.255.255.255

router ospf 1
network 198.51.100.0 0.0.0.3
network 203.0.113.0 0.0.0.3
network 10.0.50.0 0.0.0.255
network 10.0.55.0 0.0.0.255
```

#### R2
```
interface Loopback 0
ip address 2.2.2.2 255.255.255.255

router ospf 1
network 201.85.20.0 0.0.0.3
network 203.0.113.0 0.0.0.3
network 10.0.40.0 0.0.0.255
network 10.0.44.0 0.0.0.255
```
#### R3
```
interface Loopback 0
ip address 3.3.3.3 255.255.255.255

router ospf 1
network 201.85.20.0 0.0.0.3
network 198.51.100.0 0.0.0.3
network 10.0.10.0 0.0.0.255
network 10.0.20.0 0.0.0.255
```
![OSPF Confirmation](https://github.com/RouteSeeker/CCNA_OSPF_Packet_Tracer/blob/main/assets/screenshots/VOIP_OSPF/08.IP_Route.PNG)

![Ping Test](https://github.com/RouteSeeker/CCNA_OSPF_Packet_Tracer/blob/main/assets/screenshots/VOIP_OSPF/09.Ping_Test.PNG)

### 4.Configuring Telephony Peer Services
The final step involves configuring peer dialing on the two IP telephony VLANs to ensure seamless call connectivity between the different VLANs.

#### R1
```
dial-peer voice 10 voip
destination-Pattern 2...
session target ipv4:203.0.113.2
```

#### R2
```
dial-peer voice 10 voip
destination-Pattern 1...
session target ipv4:203.0.113.1
```
![Phone Testing](https://github.com/RouteSeeker/CCNA_OSPF_Packet_Tracer/blob/main/assets/screenshots/VOIP_OSPF/10.Peer_Dial.PNG)

## Conclusion
This lab demonstrates a basic setup for OSPF routing and telephony services in a simulated network environment. Further configurations will include advanced OSPF features and security settings.

