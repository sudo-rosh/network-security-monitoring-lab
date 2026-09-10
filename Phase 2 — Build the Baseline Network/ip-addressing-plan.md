# Phase 2 — Build the Baseline Network

## 1. Device Inventory

| Device | Quantity | Purpose |
|---|:---:|---|
| Server | 5 | Blood Analyzer, DNA Sequencer, Patient Database, Web Portal, DHCP Server |
| PC | 7 | Lab Tech PC, Doctor PC 01, Doctor PC 02, Admin Workstation, Reception PC, Billing PC, External PC representing other hospitals |
| Printer | 1 | Network Printer |
| Switch | 5 | Connect devices within the organization's network |
| Router | 1 | Default gateway and routing between networks |

## 2. Baseline Topology in Packet Tracer

![[network-security-monitoring-lab-topology 1.png]]


> [!NOTE]
> **Lab Tech PC connectivity**
>
> The Lab Tech PC has only one Ethernet interface, so it cannot directly connect to both the Blood Analyzer and DNA Sequencer. A switch provides the additional Ethernet ports required to connect all three devices on the same local network.

---

# 3. IP Addressing Plan

| IP Address      | Device             |
| --------------- | ------------------ |
| **General**     |                    |
| `10.10.10.5`    | Hospital Router    |
| `10.10.10.6`    | Hospital Switch    |
| **Lab Tech**    |                    |
| `10.10.20.10`   | Lab Switch         |
| `10.10.20.15`   | Lab Tech PC        |
| `10.10.20.16`   | Blood Analyzer     |
| `10.10.20.17`   | DNA Sequencer      |
| **Doctor**      |                    |
| `10.10.30.10`   | Doctor Switch      |
| `10.10.30.15`   | Doctor PC 1        |
| `10.10.30.16`   | Doctor PC 2        |
| **IT**          |                    |
| `10.10.40.10`   | IT Switch          |
| `192.168.1.10`  | DMZ Switch         |
| `10.10.40.15`   | DHCP Server        |
| `10.10.40.16`   | Admin Workstation  |
| `10.10.40.17`   | Patient Database   |
| `192.168.1.15`  | Web Portal         |
| **Front Desk**  |                    |
| `10.10.50.10`   | Front Desk Switch  |
| `10.10.50.15`   | Reception PC       |
| `10.10.50.16`   | Billing PC         |
| `10.10.50.17`   | Network Printer    |
| **External**    |                    |
| `192.168.10.10` | Other Hospitals PC |

> [!INFO] Switch Management IPs
> Switches in the network are assigned **management IP addresses** so administrators can communicate with and manage the switch itself.
>
> A Layer 2 switch does not require IP addresses on its physical ports to forward network traffic. Instead, the management IP is assigned to a **Switch Virtual Interface (SVI)**.

## 4. Assigning Addresses to Network Devices and Hosts

### 4.1 Hospital Router

```text
Router#enable

Router#config t

Enter configuration commands, one per line. End with CNTL/Z.

Router(config)#interface giga

Router(config)#interface gigabitEthernet 0/0

Router(config-if)#ip address 10.10.10.6 255.255.255.0

Router(config-if)#no shutdown

Router(config-if)#exit

Router(config)#exit

Router#

%SYS-5-CONFIG_I: Configured from console by console
```

### 4.2 Hospital Switch

```text
Switch#config t

Enter configuration commands, one per line. End with CNTL/Z.

Switch(config)#interface vlan 1

Switch(config-if)#ip address 10.10.10.6 255.255.255.0

Switch(config-if)#no shutdown

Switch(config-if)#exit

Switch(config)#ip default-gateway 10.10.10.5

Switch(config)#exit

Switch#

%SYS-5-CONFIG_I: Configured from console by console
```

> [!INFO] Why does a switch use `interface vlan 1`?
> A Layer 2 switch forwards traffic through its physical Ethernet ports. These ports do **not** require IP addresses for normal switching.
>
> To manage the switch itself, an IP address is assigned to a **Switch Virtual Interface (SVI)**:
>
> ```text
> interface vlan 1
> ip address 10.10.10.5 255.255.255.0
> ```
>
> > [!WARNING] Remember
> > Physical ports carry network traffic. The SVI provides an IP address for managing the switch.

### 4.3 Department Switches

```text
Switch>enable

Switch#config t

Enter configuration commands, one per line. End with CNTL/Z.

Switch(config)#interface vlan 1

Switch(config-if)#ip address 10.10.x.10 255.255.255.0

Switch(config-if)#no shutdown

Switch(config-if)#

%LINK-3-UPDOWN: Interface Vlan1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

Switch(config-if)#exit
```

### 4.4 Servers

![[dhcp-ip-configuration.png]]

### 4.5 Printer

![[network-printer-ip-configuration.png]]

## 5. PCs

![[pc-ip-configuration.png]]

> [!NOTE]
> All PCs are assigned **static IP addresses** during Phase 2. Once the DHCP server is configured, the PCs will receive their IP addresses through DHCP.

---

# 6. Basic Network Connectivity

The following connectivity tests were performed to establish the baseline state of the network.

|   # | Source                            | Destination                       | Result | Packet Loss |
| --: | --------------------------------- | --------------------------------- | :----: | ----------: |
|   1 | Lab Tech PC (`10.10.20.15`)       | Blood Analyzer (`10.10.20.16`)    | ✅Pass  |          0% |
|   2 | Lab Tech PC (`10.10.20.15`)       | DNA Sequencer (`10.10.20.17`)     | ✅Pass  |          0% |
|   3 | Lab Tech PC (`10.10.20.15`)       | Patient Database (`10.10.40.17`)  | ❌Fail  |        100% |
|   4 | Doctor PC 1 (`10.10.30.15`)       | Doctor PC 2 (`10.10.30.16`)       | ✅Pass  |          0% |
|   5 | Doctor PC 1 (`10.10.30.15`)       | Patient Database (`10.10.40.17`)  | ❌Fail  |        100% |
|   6 | Reception PC (`10.10.50.15`)      | Billing PC (`10.10.50.16`)        | ✅Pass  |          0% |
|   7 | Reception PC (`10.10.50.15`)      | Patient Database (`10.10.40.17`)  | ❌Fail  |        100% |
|   8 | Admin Workstation (`10.10.40.16`) | Patient Database (`10.10.40.17`)  | ✅Pass  |          0% |
|   9 | Admin Workstation (`10.10.40.16`) | Web Portal (`192.168.1.15`)       | ❌Fail  |        100% |
|  10 | Lab Tech PC (`10.10.20.15`)       | Lab Switch (`10.10.20.10`)        | ✅Pass  |          0% |
|  11 | Doctor PC 1 (`10.10.30.15`)       | Doctor Switch (`10.10.30.10`)     | ✅Pass  |          0% |
|  12 | Reception PC (`10.10.50.15`)      | Front Desk Switch (`10.10.50.10`) | ✅Pass  |          0% |
|  13 | Admin Workstation (`10.10.40.16`) | DHCP Server (`10.10.40.15`)       | ✅Pass  |          0% |

> [!NOTE] Baseline Observation
> Devices on the **same subnet** successfully communicated with one another.
>
> Communication between **different subnets** failed because inter-network routing has not yet been configured.

