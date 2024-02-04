#VPN / WAN3 / Routing

## Installing a VPN with MPWAN3, and Guest Network Only and Specific IPS / Devices:


Requirements:


 - VPN Provider (We'll use ProtonVPN which has a free option)
  
- OpenWrt Router

- openvpn configuration which we'll slighly modify



If you didn't watch our second video please install these modules:


- mwan3
- luci-app-mwan3
- openvpn-ssl
- luci-app-openvpn


### Step 1

- Network - Devices - Add New Interface
- tun0

- Network -> Interfaces -> Add New Interface
- Create Interface for tun0 -> use unmanaged

- Click on save and apply

### Step 2 


- VPN -> Upload your vpn file
- Click save and apply

-edit it


- Change the following lines:

```
dev tun -> dev tun0
auth-user-pass -> auth-user-pass /etc/openvpn/FreeProtonVPN.auth
```

Replace /etc/openvpn/FreeProtonVPN.auth with the location its showing at the bottom

- add these 2 lines right above the ca-certificates:

```
route-nopull
route 0.0.0.0 0.0.0.0 vpn_gateway 20
```

Under the second textbox
put your username/pass

example:

```
david
password
```

click save


- Click enabled on the checkbox

- Click save

- Make sure all options are saved

- click on VPN
- openvpn

- Click the checkbox enabled
- click Save

- Go to logs make sure its connected successfully
- Status -> System logs


example success:

```
Sun Feb  4 16:04:47 2024 daemon.info avahi-daemon[2653]: Joining mDNS multicast group on interface tun0.IPv4 with address 10.96.0.206.
Sun Feb  4 16:04:47 2024 daemon.info avahi-daemon[2653]: New relevant interface tun0.IPv4 for mDNS.
Sun Feb  4 16:04:47 2024 daemon.info avahi-daemon[2653]: Registering new address record for 10.96.0.206 on tun0.IPv4.
Sun Feb  4 16:04:47 2024 daemon.notice openvpn(ProtonFreeVPN)[8821]: /usr/libexec/openvpn-hotplug up ProtonFreeVPN tun0 1500 1624 10.96.0.206 255.255.0.0 init

```

### Step 3 MultiWan Setup

- Network -> MultiWan Manager

- Add Interface tun0
- click on enabled
- click save
- save and apply
- click on member
- add tun0_m1
- select tun0 interface
- save
- save and apply
- click on policy
- add tun_only
- select tun0_m1
- select save
- save and apply

Click on Status -> MultiWan


Tun0 should be green



### Step 4 FireWall Setup

- Network -> Firewall

- Add tun0fire
- covered tun0
- allow source/destination to LAN
- make sure reject/ for input and forward
- enable masquerading
- save and apply

## Step 5 Testing!!


- select Network->MultiWan Manager
- Select Rules
- edit an existing rule to make sure its working
- For example HTTP:
put it to tun_only instead of WAN

- Go to web and put whats my IP addresses
- should see vpn address under whats my iptables

- If you dont try restarting the VPN connection it takes a few 
seconds to take effect
