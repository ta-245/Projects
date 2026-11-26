# Multi-Routing Networking Project
**Author**: Tide Adewunmi

**Date**: 10/14-15/2026

  
<img width="1074" height="406" alt="image" src="https://github.com/user-attachments/assets/05d45c89-d854-418f-9914-3ed838b5ba2b" />

  
**Objective**: Test my ability to configure 4 subnet blocks to routers given access to the cloud.


With FastEthernet (100 Mbps) with the routers. (It will show green when the cables are connected–even when the routers are not configure)

With GigaEthernet (1 Gbps) wires with the routers (It will be green only when the routers are configured).

| Attribute | FastEthernet (`Fa`) | GigabitEthernet (`Gi`) |
| --------- | ----------------- | -------------------- |
| Max Speed | 100 Mbps          | 1,000 Mbps (1 Gbps)  |
| Cisco CLI Prefix | `interface fastethernet 0/0` | `interface gigabitethernet 0/0` |
| Copper Cable Pairs | Uses only 2 pairs (4 wires) | Requires all 4 pairs (8 wires) |
| Minimum Cable Type | Category 5 (Cat5) | Category 5e (Cat5e) or Cat6 |
| Primary Use Case | Legacy user devices, VoIP phones | Inter-switch trunks, routers, servers |



## For this Lab:
**Base Network**: `192.168.7.0/24`
**Total Base addresses**: `2^{[32 - 24]}` = `2^{8}` = `256` addresses in the base `192.168.7.0/24` network

For 4 subnets, 15 hosts:
`256/4` = `**64 total addresses**` for each subnet

To have a subnet for 15 host addresses, we need:
- 1 network address, 15 usable addresses (including default gateway, routers, switches, client devices), and 1 broadcast network.

So we need at least 17 addresses.

To satisfy, we would use `32 [2^{5}] > 17`

Since we have 25, we have 5 host bits. `32 - 5` = `/27`

Subnet mask: `11111111 11111111 11111111 11100000` [`255.255.255.`[`128+64+32+0+0+0+0+0`] --> `255.255.255.224`]


**Subnet 1**: `192.168.7.0/27`
- With all that, we get Subnet 1 addresses: `192.168.7.0` -- `192.168.7.16` [17 addresses]
- **Network address [First address]**: `192.168.7.0`
- 15 Usable host addresses: 192.168.7.1 -- `192.168.7.15`
- **Broadcast address [Last address]**: `192.168.7.16`

**Subnet 2**: `192.168.7.17/27`
- With all that, we get Subnet 2 addresses: `192.168.7.17` -- `192.168.7.33` [17 addresses]
- **Network address [First address]**: `192.168.7.17`
- 30 Usable host addresses: `192.168.7.18` -- `192.168.7.32`
- **Broadcast address [Last address]**: `192.168.7.33`

**Subnet 3**: `192.168.7.34/27`
- With all that, we get Subnet 3 addresses: `192.168.7.34` -- `192.168.7.50` [17 addresses]
- **Network address [First address]**: `192.168.7.34`
- 30 Usable host addresses: `192.168.7.35` -- `192.168.7.49`
- **Broadcast address [Last address]**: `192.168.7.50`

**Subnet 4**: `192.168.7.51/27`
- With all that, we get Subnet 4 addresses: `192.168.7.51` -- `192.168.7.67` [17 addresses]
- **Network address [First address]**: `192.168.7.51`
- 30 Usable host addresses: `192.168.7.52` -- `192.168.7.66`
- **Broadcast address [Last address]**: `192.168.7.67`





<img width="394" height="466" alt="image" src="https://github.com/user-attachments/assets/65a3643a-881c-4bd2-b1e5-e2c5497c479d" />

**Note**: Since it is the first time `PC_02` pings the switch, it will experience a request time out.


<img width="471" height="410" alt="image" src="https://github.com/user-attachments/assets/88daf1dd-fc32-47be-8dd7-65a776582fd1" />

**Note**: Pinging a switch outside the network, there will be a `100% packet loss` as it is not on this network.



## Configure the Switch
Configure the switch, there are a few key commands to run in the CLI.
- `enable`
- `configure terminal`
- `int vlan1`
- `ip address <IP address> <subnet mask[255.255.X.X form]>`
- `no shutdown`
- `copy running-config startup-config` [When asked Destination filename, just click Enter again and it should tell you ‘Building configuration…’ and ‘[OK!]’] 





Once you run  the `show startup-config`, keep clicking **_Enter_** until the end and you will see the `vlan1` configuration.

Once you run  the `show running-config`, keep clicking **_Enter_** until the end and you will see the `vlan1` configuration.


To see the config successful, do two ways:
- In the terminal, press `end` command until the terminal shows Switch#. Do NOT type end again. Next, type `show int vlan1` command and you get a message shown below.
- You can also go to one of your host systems and in the command prompt, `ping` the IP address of the switch. The first time you run this with your switch, you will experience a `Request time out.`. **Don’t panic!!!** This often happens. The next 3 times, it will receive the probes.
  - Try running it again and you will see that it pings and receives all 4 probes.


## Configure the Router
Configure the switch, there are a few key commands to run in the CLI.
- `enable`
- `configure terminal`
- `hostname <new-router-name>` [To change the name of the router in the terminal]
- `no ip domain-lookup` [To prevent the router from translating typos as fake DNS requests]
- `enable secret <password>` [secure your router]
- `interface <gigabitethernet/fastethernet> <slot/port>` [gigabitethernet 0/0[/0]]
- `ip address <IP address> <subnet mask[255.255.X.X form]>` [for this use the IP address for the default gateway]
- `no shutdown` [to enable the interface]
- `copy running-config startup-config` [When asked Destination filename, just click Enter again and it should tell you ‘Building configuration…’ and ‘[OK!]’] 



## Issues with Network and Fixes
I might have to restructure the entire subnet. When setting up the router (`R1`), configuring the `interface gigabitethernet 0/1`, I got an IP address overlap message [`192.168.7.0 overlaps with GigabitEthernet0/0`]. This is a common mistake with beginners (like myself). This error is because I am mapping them on the same network [`255.255.255.224`]. 

To fix this, I need them on two different routes. Because I calculated this network to be 32 addresses to fit 15 usable addresses for each subnet, I need to set them to be in the 32 addresses, not the 17 addresses so it need to be: 
- **Subnet 1:** `192.168.7.0` - `192.168.7.31` [32 addresses]
- **Subnet 2**: `192.168.7.32` - `192.168.7.63` [32 addresses]
- **Subnet 3**: `192.168.7.64` - `192.168.7.95` [32 addresses]
- **Subnet 4**: `192.168.7.96` - `192.168.7.127` [32 addresses]

So, now I need to reconfigure everything in each subnet block and try again. Default gateway, switches, and hosts IP addresses must change to reflect the proper subnetting of the network and the router. It will be done so accordingly.

**Subnet 1**: `192.168.7.0/27`
- With all that, we get Subnet 1 addresses: `192.168.7.0` -- `192.168.7.31` [32 addresses]
- **Network address [First address]**: `192.168.7.0`
- 30 Usable host addresses: `192.168.7.1` -- `192.168.7.30`
- **Broadcast address [Last address]**: `192.168.7.31`

<img width="673" height="225" alt="image" src="https://github.com/user-attachments/assets/212e8314-e80d-446a-87c9-d4f8ec62cf16" />


**Subnet 2**: `192.168.7.32/27`
- With all that, we get Subnet 2 addresses: `192.168.7.32` -- `192.168.7.63` [32 addresses]
- **Network address [First address]**: `192.168.7.32`
- 30 Usable host addresses: `192.168.7.33` -- `192.168.7.62`
- **Broadcast address [Last address]**: `192.168.7.63`

<img width="538" height="235" alt="image" src="https://github.com/user-attachments/assets/dbfa0f86-76da-4f49-abb8-5262c53ffc33" />


**Subnet 3**: `192.168.7.64/27`
- With all that, we get Subnet 3 addresses: `192.168.7.64` -- `192.168.7.95` [32 addresses]
- **Network address [First address]**: `192.168.7.64`
- 30 Usable host addresses: `192.168.7.65` -- `192.168.7.94`
- **Broadcast address [Last address]**: `192.168.7.95`

<img width="587" height="238" alt="image" src="https://github.com/user-attachments/assets/ce1f39c8-dc81-4a42-839c-167d49e187a6" />


**Subnet 4**: `192.168.7.96/27`
- With all that, we get Subnet 4 addresses: `192.168.7.96` -- `192.168.7.127` [32 addresses]
- **Network address [First address]**: `192.168.7.96`
- 30 Usable host addresses: `192.168.7.97` -- `192.168.7.126`
- **Broadcast address [Last address]**: `192.168.7.127`

<img width="536" height="210" alt="image" src="https://github.com/user-attachments/assets/d742b138-b249-4bad-8675-a73ee7fb15df" />


### Optional [Add and configure a Server]
Should I want to add a server to the mix, I just need to add the server, connect it to a switch or router. 

Should you choose a switch, pick the subnet block switch to connect it to. Next, configure the server [HTTP, DNS, etc], and make sure whichever server it is, it is selected [On] and others are deselected [Off]. Within the subnet block, configure the IP, subnet mask, and default gateway of the server. Next, you can make any other changes to it. To see if it is connected, `ping` other devices in the subnet block.

**Web Server**: For HTTP, you can modify the documents, then hop on a host device on the network, go to the Web Browser and in the URL box, type the IP address of the Web server, hit Enter and see the website come to life. After this, if you have any other features you want to add to the website, type on.


**DNS**: Deselect the default HTTP and HTTPS in Services and select DNS.
- Anshika's [Configure DNS Server On Cisco Packet Tracer](https://medium.com/@z6157881/configure-dns-server-on-cisco-packet-tracer-e7c412b3b3dd) for reference.
- Next, take note of all the PCs on your subnet block
  - Once you have assigned an IP address to the server, be sure to configure the DNS Server on each PC to the IP address of the DNS server.

    <img width="321" height="167" alt="image" src="https://github.com/user-attachments/assets/1379040e-5ad5-4474-88e1-88d9970c81a5" />

- **[Optional]**: Go to the _Services_ on the server and select `DNS`. You may add the hostnames for the hosts on the subnet block, including a hostname for the DNS server (and any other servers in your network) and their IP addresses [in Address]. After this, select _Add_ and the information is displayed in the table below. This can improve readability in your network.
- Finally, to check the connection between the hosts and the DNS server, ping the DNS server (either by the IP address or the hostname from the dns server). If it is successful, then the DNS is operational. I used one of the hosts to `ping` the Web Server and that is the result below.

<img width="442" height="196" alt="image" src="https://github.com/user-attachments/assets/b7f027b9-6221-4d5c-a2fe-1cb6e507107b" />


