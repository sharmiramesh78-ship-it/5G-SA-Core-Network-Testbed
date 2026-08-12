# 5G SA Protocol Flow Summary

## 1. Overview

This document presents a protocol-level summary of the major procedures studied during the **5G Standalone (SA) Core Network Testbed** practical.

The testbed used **Open5GS** as the 5G Core and **UERANSIM** to simulate the 5G gNB and UE. The analysis focused on understanding how the different network functions and protocols interact during **gNB connection establishment, UE registration, authentication, PDU session establishment, and user-plane data communication**.

Packet captures, system logs, UERANSIM output, and connectivity tests were used to relate the expected 5G procedures to the behavior observed in the practical environment.

---

## 2. Testbed Components

The main components involved in the protocol flow were:

| Component | Function                                               |
| --------- | ------------------------------------------------------ |
| **UE**    | User Equipment; simulated using UERANSIM               |
| **gNB**   | 5G Radio Access Network node; simulated using UERANSIM |
| **AMF**   | Access and Mobility Management Function                |
| **SMF**   | Session Management Function                            |
| **UPF**   | User Plane Function                                    |
| **AUSF**  | Authentication Server Function                         |
| **UDM**   | Unified Data Management                                |
| **UDR**   | Unified Data Repository                                |
| **NRF**   | Network Repository Function                            |
| **PCF**   | Policy Control Function                                |
| **NSSF**  | Network Slice Selection Function                       |

---

## 3. Overall 5G SA Protocol Architecture

The high-level relationship between the UE, gNB, and 5G Core can be represented as:

```text
                         5G SA Network

┌──────────────┐
│      UE      │
│  UERANSIM UE │
└──────┬───────┘
       │
       │  RRC / NAS
       │
       ▼
┌──────────────┐
│     gNB      │
│ UERANSIM gNB │
└──────┬───────┘
       │
       │  N2 / NGAP / SCTP
       │
       ▼
┌──────────────────────────────────────┐
│              Open5GS                 │
│                                      │
│  ┌─────┐     ┌─────┐     ┌─────┐   │
│  │ AMF │────>│ SMF │────>│ UPF │   │
│  └─────┘     └─────┘     └──┬──┘   │
│     │           │            │      │
│   AUSF        PCF          N3/GTP-U │
│     │           │            │      │
│   UDM/UDR     NRF/NSSF       ▼      │
└──────────────────────────────┼───────┘
                               │
                               │ N6
                               ▼
                         Data Network
```

The **control plane** manages registration, authentication, mobility, and session establishment, while the **user plane** carries application data through the UPF.

---

# 4. gNB–AMF Connectivity and NG Setup

Before UE registration, the simulated gNB establishes communication with the AMF.

The gNB uses **SCTP** as the transport protocol for **NGAP** signaling toward the AMF.

The basic sequence is:

```text
UERANSIM gNB                         Open5GS AMF
      │                                   │
      │──── SCTP Association ────────────>│
      │                                   │
      │──── NG Setup Request ────────────>│
      │                                   │
      │<─── NG Setup Response ────────────│
      │                                   │
      │       NG Connection Ready         │
```

### Key protocols

* **SCTP** — transport layer for NGAP
* **NGAP** — signaling between gNB and AMF
* **N2** — logical control-plane interface between the gNB and AMF

Successful NG Setup indicates that the simulated gNB has established the required signaling relationship with the AMF.

---

# 5. UE Access and RRC Procedure

After the simulated gNB becomes operational, the UE performs the initial access procedure.

The UE first identifies and selects the available simulated cell. It then establishes the required **Radio Resource Control (RRC)** connection with the gNB.

The simplified sequence is:

```text
UE                                      gNB
│                                        │
│──── RRC Connection / Setup Request ───>│
│                                        │
│<──────── RRC Setup ────────────────────│
│                                        │
│──── RRC Setup Complete ───────────────>│
│                                        │
│          RRC connection established    │
```

RRC provides the radio-control signaling required before NAS signaling can be exchanged with the 5G Core.

---

# 6. 5G Registration Procedure

Once the RRC connection is established, the UE begins the **5G registration procedure**.

The registration signaling is carried between the UE and AMF through the gNB.

The main stages studied were:

1. Registration Request
2. Initial UE signaling
3. Authentication
4. Security Mode procedure
5. Registration Accept
6. Registration Complete

A simplified representation is:

```text
UE                    gNB                    AMF
│                      │                      │
│── Registration ─────>│                      │
│     Request          │── Initial UE Msg ───>│
│                      │                      │
│                      │<── NAS Signaling ────│
│<──── NAS ────────────│                      │
│                      │                      │
│<──── Authentication / Security ─────────────│
│                      │                      │
│<──── Registration Accept ───────────────────│
│                      │                      │
│──── Registration Complete ─────────────────>│
│                      │                      │
│             UE Registered                  │
```

The registration procedure establishes the UE's access to the 5G Core and prepares the network for subsequent session establishment.

---

# 7. Authentication and Security

Authentication is an important part of the 5G registration procedure.

The AMF coordinates with the relevant Core Network functions to authenticate the subscriber and establish the required security context.

The main functions involved include:

* **AMF**
* **AUSF**
* **UDM**
* **UDR**

After authentication, the **Security Mode procedure** is used to activate the appropriate security mechanisms for NAS signaling.

The practical analysis focused on identifying the signaling associated with these procedures rather than implementing the underlying authentication algorithms.

---

# 8. Registration Completion

Following successful authentication and security procedures, the network sends a **Registration Accept** message to the UE.

The UE then sends **Registration Complete**.

At this point, the UE has successfully completed the registration procedure and can proceed toward establishing a data session.

The simplified sequence is:

```text
Authentication
      │
      ▼
Security Mode
      │
      ▼
Registration Accept
      │
      ▼
Registration Complete
      │
      ▼
UE Registered
```

---

# 9. PDU Session Establishment

After successful registration, the UE requests a **PDU Session** to obtain user-plane connectivity.

The session establishment involves the AMF, SMF, and UPF.

The main sequence is:

1. UE sends a PDU Session Establishment Request.
2. The gNB transports the signaling toward the AMF.
3. The AMF communicates with the SMF.
4. The SMF performs session-management procedures.
5. The SMF selects/configures the UPF.
6. PFCP signaling is exchanged between the SMF and UPF.
7. User-plane resources are established.
8. The UE receives the PDU Session Establishment Accept.
9. The UE data tunnel becomes active.

---

# 10. PDU Session Signaling Flow

A simplified session-establishment flow is:

```text
UE          gNB          AMF          SMF          UPF
│            │            │            │            │
│── PDU ────>│            │            │            │
│  Session   │            │            │            │
│  Request   │            │            │            │
│            │── NAS ────>│            │            │
│            │            │── Session ─>│            │
│            │            │   Request  │            │
│            │            │            │            │
│            │            │            │── PFCP ───>│
│            │            │            │            │
│            │            │            │<─ PFCP ────│
│            │            │            │            │
│            │<──── Resource Setup ────────────────│
│            │            │            │            │
│<──── PDU Session Establishment Accept ────────────│
│            │            │            │            │
│══════════════ UE Data Tunnel Active ══════════════│
```

The exact signaling sequence may contain additional messages and procedures depending on the configuration and implementation.

---

# 11. SMF–UPF Interaction

The **SMF** is responsible for session management, while the **UPF** handles user-plane packet forwarding.

The SMF communicates with the UPF using **PFCP** over the N4 interface.

The simplified relationship is:

```text
                SMF
                 │
                 │ N4 / PFCP
                 │
                 ▼
                UPF
                 │
                 │ N3 / GTP-U
                 ▼
                gNB
```

PFCP is used to establish and control the user-plane forwarding rules required for the PDU session.

---

# 12. User-Plane Data Flow

Once the PDU session has been established, user-plane traffic can flow between the UE and the external data network.

The primary path is:

```text
UE
 │
 │ User Data
 ▼
gNB
 │
 │ N3
 │ GTP-U
 ▼
UPF
 │
 │ N6
 ▼
Data Network
```

In the UERANSIM environment, the UE data path was associated with the:

```text
uesimtun0
```

interface.

The user-plane tunnel allows applications associated with the simulated UE to exchange data through the 5G network.

---

# 13. Important Protocols

| Protocol  | Role in the Testbed                                        |
| --------- | ---------------------------------------------------------- |
| **RRC**   | UE–gNB radio-control signaling                             |
| **NAS**   | UE–Core registration and session signaling                 |
| **NGAP**  | gNB–AMF signaling                                          |
| **SCTP**  | Transport for NGAP                                         |
| **PFCP**  | SMF–UPF session and forwarding control                     |
| **GTP-U** | User-plane tunneling                                       |
| **SBI**   | Service-based communication between Core Network functions |

---

# 14. Important 5G Interfaces

| Interface | Connection         | Purpose                          |
| --------- | ------------------ | -------------------------------- |
| **N1**    | UE ↔ AMF           | NAS signaling                    |
| **N2**    | gNB ↔ AMF          | Control-plane signaling          |
| **N3**    | gNB ↔ UPF          | User-plane traffic               |
| **N4**    | SMF ↔ UPF          | PFCP session control             |
| **N6**    | UPF ↔ Data Network | External data connectivity       |
| **SBI**   | 5G Core NFs        | Service-based Core communication |

---

# 15. Control Plane and User Plane

A key concept studied during the project was the separation between the **control plane** and **user plane**.

### Control Plane

The control plane manages network access and sessions.

It includes:

* UE registration
* Authentication
* Security procedures
* Mobility management
* PDU session management
* Network-function coordination

A simplified control-plane path is:

```text
UE
 │
 │ NAS
 ▼
gNB
 │
 │ NGAP / SCTP
 ▼
AMF
 │
 │ SBI
 ▼
5G Core Functions
 │
 └── SMF
```

### User Plane

The user plane carries actual application and data traffic.

The simplified path is:

```text
UE
 │
 ▼
gNB
 │
 │ GTP-U
 ▼
UPF
 │
 ▼
Data Network
```

This separation is fundamental to the architecture of a 5G Standalone network.

---

# 16. Packet Capture and Protocol Analysis

Network traffic was captured and analyzed using:

* **Wireshark**
* **tshark**
* **tcpdump**

The purpose of packet analysis was to correlate observed traffic with the expected 5G procedures.

The analysis focused on:

### SCTP

Used to transport NGAP signaling between the simulated gNB and AMF.

### NGAP

Used to carry signaling between the gNB and AMF, including UE-related signaling and NG setup procedures.

### RRC

Used for radio-control communication between the UE and gNB.

### NAS

Used for UE registration, authentication-related signaling, and session-management procedures.

### PFCP

Used for session and forwarding control between the SMF and UPF.

### GTP-U

Used to transport user-plane data between the gNB and UPF.

---

# 17. End-to-End Protocol Sequence

The complete high-level sequence studied during the practical can be summarized as:

```text
                 Open5GS Core Startup
                         │
                         ▼
                    gNB Startup
                         │
                         ▼
                 SCTP Association
                         │
                         ▼
                      NG Setup
                         │
                         ▼
                  UE Cell Selection
                         │
                         ▼
                    RRC Setup
                         │
                         ▼
                 5G Registration
                         │
                         ▼
                   Authentication
                         │
                         ▼
                   Security Mode
                         │
                         ▼
               Registration Complete
                         │
                         ▼
              PDU Session Request
                         │
                         ▼
                AMF–SMF Interaction
                         │
                         ▼
               SMF–UPF PFCP Setup
                         │
                         ▼
                 User-Plane Setup
                         │
                         ▼
                  UE Data Tunnel
                         │
                         ▼
                  GTP-U Traffic
                         │
                         ▼
               End-to-End Connectivity
```

This sequence illustrates how the control-plane procedures ultimately enable the user-plane data path.

---

# 18. Practical Validation

The protocol flow was validated using multiple sources of evidence, including:

* Open5GS service status
* Open5GS logs
* UERANSIM output
* UE tunnel status
* Connectivity testing
* Wireshark packet inspection
* `tshark`
* `tcpdump`

The practical validation demonstrated:

| Validation                | Outcome    |
| ------------------------- | ---------- |
| gNB–AMF Connectivity      | Successful |
| NG Setup                  | Successful |
| UE Registration           | Successful |
| Authentication            | Successful |
| PDU Session Establishment | Successful |
| UE Data Tunnel            | Active     |
| User-Plane Connectivity   | Successful |
| Packet Capture            | Completed  |
| Protocol Analysis         | Completed  |

The user-plane connectivity test recorded:

* **Packets transmitted:** 18
* **Packets received:** 18
* **Packet loss:** 0%
* **Average RTT:** approximately 16 ms

These measurements provided additional evidence that the simulated user-plane path was operational during the practical session.

---

# 19. Key Observations

The practical analysis demonstrated several important relationships within the 5G SA architecture:

1. **SCTP and NGAP** provide the signaling connection between the gNB and AMF.
2. **RRC** provides radio-control communication between the UE and gNB.
3. **NAS** supports UE registration and session-related signaling.
4. **AMF** manages access and mobility-related procedures.
5. **SMF** manages PDU sessions and coordinates user-plane configuration.
6. **UPF** forwards user-plane traffic.
7. **PFCP** provides control between the SMF and UPF.
8. **GTP-U** carries user-plane traffic between the gNB and UPF.
9. The successful UE data tunnel demonstrates the transition from control-plane session establishment to user-plane communication.

---

# 20. Key Learning Outcome

A major learning outcome from the practical was understanding that successful network connectivity is the result of several coordinated procedures rather than a single operation.

The progression:

**NG Setup → RRC → Registration → Authentication → Security → PDU Session Establishment → PFCP → GTP-U → User Data**

shows how multiple protocols and network functions cooperate to provide end-to-end connectivity in a 5G Standalone network.

Packet-level analysis made it possible to connect theoretical 5G architecture concepts with actual signaling and traffic observed in the testbed.

---

# 21. Conclusion

The 5G SA protocol analysis provided practical insight into the operation of a simulated 5G Standalone network using Open5GS and UERANSIM.

The study covered the complete high-level path from **gNB–AMF connectivity and UE registration to PDU session establishment and user-plane communication**.

The combination of system logs, UERANSIM output, packet captures, Wireshark analysis, and connectivity measurements provided a multi-level approach to validating the testbed.

Overall, the practical strengthened my understanding of **5G Core architecture, RRC and NAS procedures, NGAP signaling, SCTP transport, PFCP session control, GTP-U user-plane tunneling, Linux networking, and protocol-level troubleshooting**.
