# NBN Settings in OPNSense

#### **NOTE:** *WHEN WRITING THIS GUIDE, IT WAS BASED ON [OPNSENSE v24.7.6](https://opnsense.org/download/)*


# Table of Contents

***First download the required image file from the OPNSense file and follow [those steps](https://docs.opnsense.org/manual/install.html) to install in on your device.***


1. <a href="#dynamic-settings">Dynamic Settings</a>
    - <a href="#wan-interface-dynamic">WAN Interface (Dynamic)</a>
    - <a href="#interface-assignement-dynamic">Interface Assignement (Dynamic)</a>
2. <a href="#pppoe-settings">PPPoE Settings</a>
    - <a href="#creating-vlan-pppoe">Creating VLAN (PPPoE)</a>
    - <a href="#point-to-point-interface-pppoe">Point to Point Interface (PPPoE)</a>
    - <a href="#wan-interface-pppoe">WAN Interface (PPPoE)</a>
    - <a href="#interface-assignment-pppoe">Interface Assignment (PPPoE)</a>
3. <a href="#lan-interface-general">LAN Interface (General)</a>



# Dynamic Settings

Those steps are followed when the ISP router is configured with no Usename/Password, mainly becuase those are being configured at the ISP end rather that at your end, _i.e. Superloop NBN in Australia_


## WAN Interface (Dynamic)

Open OPNSense main UI page by navigating to 192.168.1.1, then from there navigate to *Interfaces-->[WAN]*. In the screen, fill in as following:

_*Basic Configuration*_
- Make sure the interface is enabled
- *Description* : WAN

_*Generic configuration*_
- Enable both the *Block private networks* and *Block bogon networks*
- *IPv4 Configuration Type* : choose *DHCP*
- *IPv6 Configuration Type* : choose *DHCP6*
- *MTU* : 1500
- *MSS* : 1492

Leave everything as it is no changes

![dynamic-dhcp-client-configuration](/screenshots/dynamic-dhcp-configuration.png)


When done click on *Save*



## Interface Assignement (Dynamic)

On the same page, head to *Interfaces-->Assignments* and select the following:
- *WAN* : select the desired WAN network adapter port (inbound)
- *LAN* : sleect the LAN network adapter port (outbound)

When done click on *Save*

![dynamic-interface-assignment](/screenshots/dynamic-interface-assignment.jpeg)



# PPPoE Settings

Those steps are followed when the ISP router is configured to have PPPoE settings with Username/Password, _i.e. iiNET NBN in Australia_



## Creating VLAN (PPPoE)

Open OPNSense main UI page by navigating to 192.168.1.1, then from there navigate to *Interfaces-->Other Types-->VLAN*. On that page, click on the orange *+* button on the right side to add a new one

![ppoe-vlan](/screenshots/pppoe-vlan.png)


In the screen, fill in as follows:
- *Device* : vlan0.1
- *Parent* : choose the ethernet port adapter where the NBN will be connected to (inbound WAN)
- *VLAN Tag*: set it to 2 
- *VLAN Priority*: set it to Best Effort (0, default)
- *Description*: anything that tells what this VLAN will be used for. I prefer setting it to the ISP name

When done click on *Save*

![ppoe-add-vlan](/screenshots/pppoe-add-vlan.png)



## Point-to-Point Interface (PPPoE)

Navigate to *Interfaces-->Point-to-Point-->Devices*. On that page, click on the orange *+* button on the right side to add a new one and fill in as following:

![pppoe-ptp](/screenshots/pppoe-ptp.png)


_PPPs configuration_
- *Link Type* : PPPoE
- *Link interface(s)*: select the previously created VLAN, i.e. _vlan0.1_
- *Description* : anything that tells what this P2P will be used for. I prefer setting it to the ISP name
- *Username*: your ISP username here
- *Password*: your ISP password here and confirm it in the next textbox
- Leave everything else empty/disabled

When done click on *Save*

![pppoe-add-ptp](/screenshots/pppoe-add-ptp.png)



## WAN Interface (PPPoE)

Navigate to *Interfaces-->[WAN]* and fill in as following:
_*Basic configuration*_
- Make sure the interface is enabled
- *Description* : WAN

_*Generic configuration*_
- Enable both *Block private networks* and *Block bogone networks* 
- *IPv4 Configuration Type* : PPPoE
- *IPv6 Configuration Type* : None
- *MTU* : 1500
- *MSS* : 1492

![pppoe-wan-basic-general](/screenshots/pppoe-wan-basic-general.png)


_*PPPoE configuration*_
- You will notice that the *Modem Port* is set to the previously created VLAN, i.e. *_vlan0.1_*
- *Username*: your ISP username here
- *Password*: your ISP password here and confirm it in the next textbox
- Leave eveything else empty/disabled

When done click on *Save*

![ppoe-wan-pppoe-config](/screenshots/ppoe-wan-pppoe-config.png)



## Interface Assignment (PPPoE)

On the same page, head to *Interface-->Assignments* and select the following:
- *LAN* : sleect the outbond (LAN) network adapter port
- *WAN* : select the previously created PPPoE interface that has both the _vlan0.1_ and the ISP user next to it

When done click on *Save*

![pppoe-interface-assignment](/screenshots/pppoe-interface-assignment.png)



## LAN Interface (General)

Navigate to *Interfaces-->[LAN]* and fill in as following:
_*Generic configuration*_
- Make sure the interface is enabled
- *Description* : LAN
- *IPv4 Configuration Type* : Static IPv4
- *IPv6 Configuration Type* : Track Interface
- Leave *MAC Address*, *MTU* and *MSS* blank
- *Speed and Duplex*: Default (no preference, typically autoselect)

![lan-general](/screenshots/lan-general.png)


_*Static IPv4 Configuration*_
- *IPv4 Address* : _192.168.1.1_ and select _24_
- *IPv4 gateway rules* : Disabled

_*Track IPv6 Interface*_
- Keep *IPv6 Interface* as Nothing selected
- *IPv6 Prefix ID* : leave it empty

When done click on *Save*

![lan-ip-settings](/screenshots/lan-ip-settings.png)



## License
This document guide is licensed under the CC0 1.0 Universal license. The terms of the license are detailed in [LICENSE](/LICENSE)
