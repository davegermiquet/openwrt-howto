# Part 1: Multi-Net Dual WAN With ChromeCast and Shared devices on a separate network

## Equipment:


### 1 WAN Ethernet Router with 2 Ethernet Ports or 2 WAN Connections

### 2 OpenWRT Routers
### 3 Ethernet Cables
 
First Create 1 network on each router with different netmask and segment
    - Example
              192.168.5.1/24
              192.168.6.1/24to factory defaults


Setup Network on Routers:

-  Create 1 network on each router with different netmask and segment
    - Example
              192.168.6.1/24
              192.168.5.1/24


1. Network Interfaces 
    - Edit LAN  ipv4
        put 192.168.5.1
    
2. Repeat for second router:
    - Edit lan ipv4
         put 192.168.6.1

3. Enable wifi on both routers so you can do the work the rest wirelessly

    - Dont forget to enable encryption and name your wifi:

    - In my example i have 3 wifi networks:
       - 1rst router: HomeNetwork 5ghz
                      GuestNetwork 2.4 ghz

        - 2nd Router:  InternetOfThings
                       InternetOfThings

        Remember enable security

From now on I'll name the router HomeNetwork, and InternetOfThings


4. Install the necessary modules on both routers:
         - System -> Software -> CLick on update lists
            - mwan3
            - luci-app-mwan3
            - Avahi-utils 
            - avahi-dbus 
            - openvpn-ssl
            - luci-app-openvpn
            - mdns-repeater

            Only on internetofthings install this:

            - smcroute

            
6.  Setup one port to be segregated to hook the 2 networks up:

    On InternetOfThings go to the following:

    - Go To Network -> Interfaces
       - Add New interface: 
         - Use Lan 1 Port and name it HOMELAN
           - Use static IP
           - 192.168.5.2
    - Go to Network Interfaces
      go to devices 
      edit br-lan
      remove LAN 1 port from that interface
    - Click on Add device configuration
    - add LAN1  make sure it has a different mac addresses


7. Create firewall On InternetOfThings

Network -> Firewall -> Add

Name: HomeLAN
Input Accept
output Accept
forward Accept

Covered Networks HomeLAN
Allowed Forward LAN
Allow forward LAN

Now make sure its connected:

Go to network -> diagnostics ->
under ipv4 ping

put 192.168.5.1 and click on ipv4 it should come back as the following:

64 bytes from 192.168.5.1: seq=0 ttl=64 time=0.098 ms

7. On HomeNetwork do the following:

Add routing to 192.168.6.1 for 192.168.5.0 network:

Click On Network -> routing
Add -> target 192.168.6.0/24
add gateway 192.168.5.2 type unicast

On InternetOfThings 

Add -> Target 192.168.5.0/24
and gateway 192.168.5.1


- Network now works.

8. Setup Avahi for MultiCast Devices:

Setup Avahi on Home networt:

- ssh -l root 192.168.5.1

vi /etc/avahi/avahi-daemon.conf

put contents:
`[server]
use-ipv4=yes
use-ipv6=no
check-response-ttl=no
use-iff-running=no

[publish]
publish-addresses=yes
publish-hinfo=yes
publish-workstation=yes
publish-domain=yes

[reflector]
enable-reflector=yes
reflect-ipv=no

[rlimits]
rlimit-core=0
rlimit-data=4194304
rlimit-fsize=0
rlimit-nofile=30
rlimit-stack=4194304
rlimit-nproc=3`
# 
repeat:

192.168.6.1:

Homenetwork:

iptables -t mangle -A PREROUTING -i br-lan -d 239.255.255.250 -j TTL --ttl-inc 1



(internetofthings)

ssh -l root 192.168.6.1 

iptables -t mangle -A PREROUTING -i br-lan -d 239.255.255.250 -j TTL --ttl-inc 1
iptables -t mangle -A PREROUTING -i lan1  -d 239.255.255.250 -j TTL --ttl-inc 1

edit /etc/smcroute.conf
and put these contents:
`
phyint br-lan enable
phyint lan1 enable 
mgroup from br-lan group 239.255.255.250
mgroup from lan1 group 239.255.255.250
mroute from br-lan group 239.255.255.250 to lan1
mroute from lan1 group 239.255.255.250 to br-lan
`


-- You should now have 2 internel networks, 2 wans and chromecast and shared things work:


9: Setup Firewall Rules for chromecast:

On Home Network:

System -> Local Startup

iptables -t mangle -A PREROUTING -i br-lan -d 239.255.255.250 -j TTL --ttl-inc 1


On Internetof things:

iptables -t mangle -A PREROUTING -i br-lan -d 239.255.255.250 -j TTL --ttl-inc 1

If lan 1 port was your connected internetofthings network:

iptables -t mangle -A PREROUTING -i lan1  -d 239.255.255.250 -j TTL --ttl-inc 1


Make sure plugs are connecterd from port X to 1 on internet of th ings routers
(For ease of my demonstation it was 1 to 1)

Now Log on to HomeNetwork:

And you can chomecast and other things to ther internal network

disable ipv6:

Part 2:

VPN and Guest Network:
