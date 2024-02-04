# Guest Network On OpenVPN:



## First Step 

- Enable Wifi On Guest NetWork you want to use:
  My Example is 2.4ghz Band
- Under Network disable lan Network
- Set Password...
- Enable it

## Second Step

- Create interface on Network -> Interfaces ->>
- Add New GuestLank
- Make IP 192.168.7.1

- Under DHCP Server
-> ADvanced settings:
-> DHCP Options:

6,10.8.0.1

where 10.8.0.1 is the ip address of your name server for VPN


- Create device on network -> Devices ->
- Choose the enabled wireless you just turned on
- give it a new mac address (possibly not needed)


## Create Firewall

- Network -> Firewall
- Add GUESTFIRE
- Allowed tun0 FOrward destination zones


- Covered networks GUEST LAN
- All Accept for input,output,forward
- Traffic Rules
- Block Router port 80 (drop input)
- Block Router prot 443 (Drop input)
- block  guest lan and to device port 80 and 443



Test yoru DNS Leakage...

https://www.dnsleaktest.com/results.html




# MWAN Multi Rules

- GUESTVPN
- Source addres 192.168.7.0/24
destination 0.0.0.0
policy assigned tun0






