The baseline network was divided into logical network segments using VLANs. Each department or network function was assigned to its own VLAN and subnet.

|Segment|VLAN|IP Address|Device|
|---|--:|---|---|
|**General**|**VLAN 10**|`10.10.10.5`|Hospital Router|
|||`10.10.10.6`|Hospital Switch|
|**Lab Tech**|**VLAN 20**|`10.10.20.10`|Lab Switch|
|||`10.10.20.15`|Lab Tech PC|
|||`10.10.20.16`|Blood Analyzer|
|||`10.10.20.17`|DNA Sequencer|
|**Doctor**|**VLAN 30**|`10.10.30.10`|Doctor Switch|
|||`10.10.30.15`|Doctor PC 1|
|||`10.10.30.16`|Doctor PC 2|
|**IT**|**VLAN 40**|`10.10.40.10`|IT Switch|
|||`10.10.40.15`|DHCP Server|
|||`10.10.40.16`|Admin Workstation|
|||`10.10.40.17`|Patient Database|
|**Front Desk**|**VLAN 50**|`10.10.50.10`|Front Desk Switch|
|||`10.10.50.15`|Reception PC|
|||`10.10.50.16`|Billing PC|
|||`10.10.50.17`|Network Printer|
|**DMZ**|**VLAN 60**|`192.168.1.10`|DMZ Switch|
|||`192.168.1.15`|Web Portal|
|**External**|**No VLAN**|`192.168.10.10`|Other Hospitals PC|

The External network represents a separate network outside the internal VLAN structure and is used to represent external hospitals accessing the organization's Web Portal.

---

## Configuring VLANs

Ports connected to end devices were configured as access ports and assigned to their respective VLANs.

Example:

```text
interface fa0/1
switchport mode access
switchport access vlan 20
```

The same configuration approach was applied to the required access ports for VLANs 20, 30, 40, 50, and 60.

### Trunk Port Configuration

Uplink ports between switches, as well as the router-facing port on the Hospital Switch, were configured as trunk ports.

Example:

```text
interface fa0/4
switchport mode trunk
```

Trunking allows traffic from multiple VLANs to travel across a single physical link.

### Switch Management SVI

Each switch was configured with a management SVI within its assigned VLAN.

Example:

```text
interface vlan 20
ip address 10.10.20.10 255.255.255.0
no shutdown
```

The corresponding SVI configuration was applied to each switch using its assigned management VLAN and management IP address.

### Verification

The VLAN and trunk configurations were verified using:

```text
show vlan brief
show interfaces trunk
```

---

## Implementing Inter-VLAN Communication

Inter-VLAN communication was implemented using **router-on-a-stick** on the Hospital Router.

### Router Subinterfaces

Subinterfaces were created on `GigabitEthernet0/0` for VLANs 10, 20, 30, 40, 50, and 60.

Each subinterface was configured with:

- 802.1Q VLAN encapsulation
    
- An IP address serving as the default gateway for the VLAN
    
- `no shutdown`
    

The configured gateways were:

|VLAN|Gateway|
|--:|---|
|10|`10.10.10.5`|
|20|`10.10.20.1`|
|30|`10.10.30.1`|
|40|`10.10.40.1`|
|50|`10.10.50.1`|
|60|`192.168.1.1`|

The IP address was removed from the physical `GigabitEthernet0/0` interface because the gateway addresses are assigned to the VLAN subinterfaces.

### Default Gateways

Default gateways were configured on end devices and switches so that they could communicate with devices outside their local subnet.

### Verification

The router interfaces were verified using:

```text
show ip interface brief
```

All six VLAN subinterfaces were shown as **up/up**.

![[router-ip-interface.png]]

Inter-VLAN communication was then tested between the following segments:

- Lab Tech → Doctor
    
- Lab Tech → IT
    
- Lab Tech → Front Desk
    

![[inter-connectivity-test.png]]

The successful tests confirmed that traffic could be routed between the VLANs through the Hospital Router.

At this stage, the VLANs provide logical segmentation, but inter-VLAN routing still permits communication between segments. The specific traffic that should be allowed or restricted will be enforced through security controls in Phase 4.

---
