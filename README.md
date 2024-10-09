## Overview

This lab demonstrates how to configure a **Standard Access Control List (ACL)** on Cisco routers using Packet Tracer. The objective is to restrict the access of a specific host (PC-3) to the local network while allowing another host (PC-4) to access external networks freely. The network topology features three routers connected via serial links, Frame Relay, and multiple switches that connect to various end devices.

---

## Lab Topology

- **PC-3 (192.168.3.3)**: Represents the kiosk station that needs its access restricted to the local network.
- **PC-4 (192.168.3.4)**: Represents another host located in the New York branch office.
- **Server-1 (192.168.1.2)** and **Server-2 (192.168.3.2)**: Hosts in local networks providing resources.
- **R1, R2**: Routers connected through Frame Relay to route traffic between the networks.
- **Frame Relay**: Connects the two routers (R1 and R2).
- **Switches**: Provide connectivity between routers, servers, and PCs.

---

## Objectives

1. **Limit access**: Block the kiosk station (PC-3) from accessing external networks while ensuring that it can still communicate within its local subnet.
2. **Allow access**: Ensure PC-4 and other devices on the network (e.g., servers) have full access to external networks.
3. **Standard ACL**: Configure a Standard ACL on Router 2 to meet the above requirements.

---

## Network IP Addressing

| Device         | Interface      | IP Address      | Subnet Mask       |
|----------------|----------------|-----------------|-------------------|
| **PC-3**       | Fa0/2          | 192.168.3.3     | 255.255.255.0     |
| **PC-4**       | Fa0/3          | 192.168.3.4     | 255.255.255.0     |
| **Server-1**   | Fa0/1          | 192.168.1.2     | 255.255.255.0     |
| **Server-2**   | Fa0/1          | 192.168.3.2     | 255.255.255.0     |
| **R1**         | Serial 0/0/0   | 192.168.2.1     | 255.255.255.0     |
| **R2**         | Serial 0/0/0   | 192.168.2.2     | 255.255.255.0     |
| **Internet**   | Fa0/1          | 172.16.1.2      | 255.255.255.0     |

---

## Steps to Configure the Lab

### 1. Router and Interface Configuration

The routers and interfaces have already been pre-configured to allow basic routing between the connected networks. Here is a summary of the setup:

- **Router 1 (R1)**: Connected to the internal network `192.168.1.0/24` and external network via Frame Relay.
- **Router 2 (R2)**: Connected to the internal network `192.168.3.0/24` and Frame Relay.

### 2. Standard ACL Configuration on Router 2

The task is to create a Standard ACL on Router 2 to:
- Block traffic from **PC-3 (192.168.3.3)** to the external network (`172.16.1.0/24`).
- Allow traffic from **PC-4 (192.168.3.4)** and other devices to external networks.

#### Steps:

1. **Access Router 2** and create a Standard ACL.
   
   ```bash
   R2(config)# access-list 1 deny 192.168.3.3 0.0.0.0
   R2(config)# access-list 1 permit any
   ```

   - `access-list 1 deny 192.168.3.3`: This rule blocks traffic from PC-3.
   - `access-list 1 permit any`: This rule permits traffic from any other device.

2. **Apply the ACL** to the outgoing interface on Router 2. The interface connecting Router 2 to the external network is **Serial 0/1/0** (with IP `192.168.2.2`).

   ```bash
   R2(config)# interface Serial 0/0/0
   R2(config-if)# ip access-group 1 out
   ```

   This ensures that traffic going out from Router 2 towards the external network is filtered based on the ACL rules.

### 3. Testing the ACL Configuration

After applying the ACL to Router 2, test the network connectivity to ensure the ACL works as expected.

#### Testing PC-3 (192.168.3.3)

1. **Verify that PC-3 CAN ping PC-4 (192.168.3.4)**:
   - Since both devices are in the same local network (`192.168.3.0/24`), PC-3 should still be able to ping PC-4.
   - Command on **PC-3**:
     ```bash
     ping 192.168.3.4
     ```

2. **Verify that PC-3 CANNOT ping PC-1 (192.168.1.3)**:
   - PC-3 should be blocked from accessing the `192.168.1.0/24` network due to the ACL.
   - Command on **PC-3**:
     ```bash
     ping 192.168.1.3
     ```

3. **Verify that PC-3 CANNOT ping PC-2 (192.168.1.4)**:
   - Similar to PC-1, PC-3 should not be able to communicate with PC-2 in the `192.168.1.0/24` network.
   - Command on **PC-3**:
     ```bash
     ping 192.168.1.4
     ```

4. **Verify that PC-3 CANNOT ping Router 1's Fa0/0 Interface (192.168.1.1)**:
   - Fa0/0 interface on Router 1 is within the restricted network, so PC-3 should not be able to ping it.
   - Command on **PC-3**:
     ```bash
     ping 192.168.1.1
     ```

5. **Verify that PC-3 CANNOT ping the Internet (172.16.1.2)**:
   - The ACL should block PC-3 from reaching external networks like the Internet.
   - Command on **PC-3**:
     ```bash
     ping 172.16.1.2
     ```

#### Testing PC-4 (192.168.3.4)

1. **Verify that PC-4 CAN ping PC-1 (192.168.1.3)**:
   - PC-4 should be able to reach the `192.168.1.0/24` network as it is not restricted by the ACL.
   - Command on **PC-4**:
     ```bash
     ping 192.168.1.3
     ```

2. **Verify that PC-4 CAN ping PC-2 (192.168.1.4)**:
   - Similar to PC-1, PC-4 should be able to communicate with PC-2 in the `192.168.1.0/24` network.
   - Command on **PC-4**:
     ```bash
     ping 192.168.1.4
     ```

3. **Verify that PC-4 CAN ping Router 1's Fa0/0 Interface (192.168.1.1)**:
   - PC-4 should have full access to the local and external networks, including Router 1's Fa0/0 interface.
   - Command on **PC-4**:
     ```bash
     ping 192.168.1.1
     ```

4. **Verify that PC-4 CAN ping the Internet (172.16.1.2)**:
   - Since PC-4 is not restricted, it should be able to ping the Internet.
   - Command on **PC-4**:
     ```bash
     ping 172.16.1.2
     ```

---

### Summary of Tests

| Test                                      | Expected Result |
|-------------------------------------------|-----------------|
| PC-3 can ping PC-4                        | **Pass**        |
| PC-3 cannot ping PC-1                     | **Pass**        |
| PC-3 cannot ping PC-2                     | **Pass**        |
| PC-3 cannot ping Router 1 Fa0/0 interface | **Pass**        |
| PC-3 cannot ping the Internet             | **Pass**        |
| PC-4 can ping PC-1                        | **Pass**        |
| PC-4 can ping PC-2                        | **Pass**        |
| PC-4 can ping Router 1 Fa0/0 interface    | **Pass**        |
| PC-4 can ping the Internet                | **Pass**        |

---

## Verification

You can verify the ACL behavior using the following commands on **Router 2**:

1. **Show the Access List** to view the current ACL and check if it's being applied correctly:
   
   ```bash
   R2# show access-lists
   ```

   This command will display the ACL's rules and the number of matches (hits) for each rule.

2. **Check Interface Status** to ensure the ACL is applied to the correct interface:
   
   ```bash
   R2# show ip interface Serial 0/0/0
   ```

   This command will confirm if the ACL has been applied in the correct direction (`out`).

---

## Conclusion

In this lab, we successfully implemented a **Standard Access Control List (ACL)** to restrict access for a specific host (PC-3) while allowing other hosts (like PC-4) full access to external networks. This configuration demonstrates how to enforce simple security policies using ACLs in Cisco networks.

Feel free to extend this lab by experimenting with more complex ACLs, such as extended ACLs, which allow you to filter traffic based on additional criteria like protocols or destination addresses.

---

## Troubleshooting

- **ACL blocking unintended traffic**: Ensure that the ACL rules are written in the correct order. Deny rules should come before permit rules in a standard ACL.
- **Wrong Interface**: Make sure the ACL is applied to the correct interface in the right direction (inbound or outbound). In this case, it's applied **outbound** on the external-facing interface of Router 2.
- **Ping fails for all hosts**: Check IP connectivity and routing configurations to ensure basic network communication works before applying ACLs.

---

## Files

- **Packet Tracer Lab File**: [Download here](STD-ACL.pka)

---

Happy Networking!
