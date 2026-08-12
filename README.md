# 5G Standalone (SA) Core Network Testbed — Open5GS + UERANSIM

## Overview

This project documents the deployment, validation, and protocol-level analysis of a **5G Standalone (SA) network testbed** using **Open5GS** as the 5G Core and **UERANSIM** as the simulated 5G RAN and UE.

The practical work was completed as part of an electronics engineering course under the **PEP Program**. The testbed was operated in an **Ubuntu 22.04 virtual machine using VirtualBox**. The work involved operating the 5G Core, simulated gNB and UE, validating registration and PDU session establishment, and analyzing signaling and user-plane traffic using **Wireshark**, `tshark`, and `tcpdump`.

This repository documents the practical deployment, configuration understanding, validation, troubleshooting, and protocol analysis performed during the project.

Open5GS and UERANSIM are external open-source projects and are not redistributed as part of this repository.

---

## Project Objectives

The main objectives of this project were to:

* Understand the architecture and operation of a 5G Standalone network.
* Deploy and operate an Open5GS 5G Core.
* Connect a simulated gNB and UE using UERANSIM.
* Understand 5G UE registration and authentication procedures.
* Establish and verify a PDU session.
* Analyze 5G control-plane and user-plane protocols.
* Validate data connectivity through the UE data tunnel.
* Capture and inspect network traffic using packet-analysis tools.
* Correlate observed packets with corresponding 5G signaling procedures.
* Gain practical experience with Linux-based virtualized networking environments.

---

## System Architecture

The testbed consists of an **Open5GS 5G Core** and a simulated **5G RAN/UE environment using UERANSIM**.

### 5G Core — Open5GS

| Network Function | Role                                |
| ---------------- | ----------------------------------- |
| AMF              | Access and Mobility Management      |
| SMF              | PDU Session and Session Management  |
| UPF              | User-plane packet forwarding        |
| UDM / UDR        | Subscriber and user data management |
| AUSF             | Authentication                      |
| PCF              | Policy control                      |
| NRF              | Network Function discovery          |
| NSSF             | Network slice selection             |
| SCP              | Service communication proxy         |

### RAN / UE — UERANSIM

* `nr-gnb` — Simulated 5G gNB
* `nr-ue` — Simulated 5G UE
* `nr-cli` — Runtime gNB/UE monitoring
* `nr-binder` — Process binding to the UE data tunnel for connectivity testing

---

## High-Level Architecture

```text
                    5G Standalone Testbed

┌──────────────────────────────────────────────┐
│                  UERANSIM                    │
│                                              │
│      Simulated gNB          Simulated UE     │
│           │                       │           │
└───────────┼───────────────────────┼───────────┘
            │                       │
           N2                    RRC / NAS
            │                       │
            ▼                       │
┌──────────────────────────────────────────────┐
│                  Open5GS                      │
│                                              │
│      AMF ───── SMF ───── UPF                 │
│       │         │          │                 │
│      AUSF      PCF      User Plane           │
│       │         │                            │
│    UDM/UDR    NRF/NSSF                       │
│                                              │
└──────────────────────────────────────────────┘
```

---

## My Contribution

As part of the practical work, I performed activities covering testbed preparation, 5G Core operation, simulated RAN/UE connectivity, validation, and packet-level analysis.

### 1. Testbed Environment Preparation

* Worked with the provided Ubuntu-based testbed environment.
* Prepared the virtualization environment required for the practical.
* Operated the testbed using **VirtualBox**.
* Used Ubuntu 22.04 for the Linux-based network environment.
* Worked with Linux networking and service-management tools.

### 2. 5G Core Operation

* Started and monitored the Open5GS network functions.
* Used Linux `systemctl` commands to manage services.
* Inspected service status and logs during testing.
* Observed the interaction between major 5G Core network functions.
* Troubleshot service and connectivity issues encountered during the practical.

### 3. gNB and Core Connectivity

* Started the UERANSIM simulated gNB.
* Verified connectivity between the simulated gNB and AMF.
* Observed the SCTP association used for NG signaling.
* Inspected NGAP signaling.
* Validated the NG Setup procedure.

### 4. UE Registration and Authentication

The simulated UE was used to study the registration procedure, including:

* PLMN search and cell selection
* RRC setup
* Initial UE signaling
* 5G registration
* Authentication
* Security Mode procedure
* Registration Accept
* Registration Complete

The signaling was correlated with packet captures and available system logs.

### 5. PDU Session Establishment

* Initiated a PDU session from the simulated UE.
* Observed interaction between the AMF, SMF, and UPF.
* Studied the session-establishment signaling.
* Observed PFCP-related session control between the SMF and UPF.
* Verified the UE data tunnel became active.

### 6. User-Plane Validation

* Used `nr-binder` to associate a network application with the UE data tunnel.
* Performed connectivity testing through the simulated UE.
* Verified user-plane packet transmission.
* Studied GTP-based user-plane traffic.

### 7. Packet Capture and Protocol Analysis

Traffic was captured and analyzed using:

* Wireshark
* `tshark`
* `tcpdump`

The analysis focused on:

* NGAP
* NR RRC
* NAS
* SCTP
* PFCP
* GTP

Wireshark packet inspection and flow analysis were used to correlate signaling messages with the corresponding 5G procedures.

---

## Verified Protocol Flow

The overall signaling sequence studied during the testbed validation can be summarized as:

```text
UE              gNB              AMF             SMF / UPF
│                │                │                  │
│                │── NG Setup ──>│                  │
│                │<─ NG Setup ───│                  │
│                │                │                  │
│<── MIB / SIB1 ─│                │                  │
│                │                │                  │
│── RRC Setup ──>│                │                  │
│<─ RRC Setup ───│                │                  │
│                │                │                  │
│── Registration ────────────────>│                  │
│                │── Initial UE ─>│                  │
│                │                │                  │
│                │<── NAS / Authentication ─────────│
│<──────── Authentication / Security Mode ──────────│
│                │                │                  │
│<──────────── Registration Accept ──────────────────│
│────────────── Registration Complete ──────────────>│
│                │                │                  │
│── PDU Session Request ─────────>│                  │
│                │── NAS / NGAP ─>│── Session Setup >│
│                │                │── PFCP ─────────>│
│                │                │<─ PFCP Response ─│
│                │<─ Resource Setup ─────────────────│
│<─ PDU Session Accept ───────────│                  │
│                │                │                  │
│══════════════ UE Data Tunnel Active ═══════════════│
│                │                │                  │
│══════════════ End-to-End User Traffic ═══════════>│
```

---

## Results

The testbed was evaluated at both the **control-plane** and **user-plane** levels.

### Validation Summary

| Validation                | Result                            |
| ------------------------- | --------------------------------- |
| gNB–AMF NG Setup          | Successful                        |
| UE Registration           | Successful                        |
| Authentication            | Successful                        |
| PDU Session Establishment | Successful                        |
| UE Data Tunnel            | Active                            |
| Assigned UE IP            | Verified during practical session |
| Connectivity Test         | Verified during practical session |
| Packet Capture            | Performed                         |
| Protocol Analysis         | Performed                         |

> **Note:** Quantitative values such as the exact UE IP address, packet loss, and average RTT should be added only when confirmed from the actual practical-session output or packet captures.

---

## Protocols and Interfaces Studied

| Protocol / Interface | Purpose                                   |
| -------------------- | ----------------------------------------- |
| SCTP                 | Transport for NGAP signaling              |
| NGAP                 | gNB–AMF signaling                         |
| NR RRC               | UE–gNB radio control signaling            |
| NAS                  | UE–5G Core mobility and session signaling |
| PFCP                 | SMF–UPF session control                   |
| GTP                  | User-plane tunneling                      |
| N2                   | gNB–AMF control-plane interface           |
| N3                   | gNB–UPF user-plane interface              |

---

## Tools and Technologies

* **Open5GS** — 5G Core network implementation
* **UERANSIM** — 5G RAN and UE simulator
* **Ubuntu 22.04** — Linux operating environment
* **VirtualBox** — Virtualization platform
* **Wireshark** — Packet analysis
* **tshark** — Command-line packet analysis
* **tcpdump** — Packet capture
* **systemd / systemctl** — Linux service management
* **Linux networking tools** — Network configuration and troubleshooting

---

## Repository Structure

```text
5G-SA-Core-Network-Testbed/
│
├── README.md
│
├── docs/
│   └── setup-notes.md
│
└── analysis/
    └── protocol-flow-summary.md
```

### Directory Purpose

**`docs/`**

Contains environment preparation, configuration, and setup notes.

**`analysis/`**

Contains protocol-flow analysis and packet-level observations.

**`README.md`**

Contains the project overview, architecture, practical contribution, validation results, and technical summary.

Additional directories such as `captures/` or `screenshots/` should only be added when actual project evidence is available and appropriate to publish.

---

## What I Learned

This project provided practical exposure to:

* 5G Standalone network architecture
* Roles of major 5G Core network functions
* gNB–AMF NGAP signaling
* RRC and NAS procedures
* 5G registration and authentication
* Security procedures
* PDU session establishment
* SMF–UPF interaction through PFCP
* GTP-based user-plane communication
* Linux service management
* Linux networking and troubleshooting
* Virtualized network testbed operation
* Packet capture and protocol analysis using Wireshark

A key learning outcome was understanding how **protocol-level evidence can be used to validate network behavior instead of relying only on application-level connectivity**.

The project also helped connect theoretical 5G architecture concepts with practical signaling, packet capture, and network troubleshooting.

---

## Acknowledgements

This practical work used open-source technologies including:

* [Open5GS](https://github.com/open5gs/open5gs)
* [UERANSIM](https://github.com/aligungr/UERANSIM)

Open5GS and UERANSIM are independent open-source projects.

This repository documents my coursework-based deployment, experimentation, observations, and analysis rather than redistributing their source code.

---

## Future Work

Potential extensions of this project include:

* Multi-UE experimentation
* Additional mobility and handover scenarios
* Deeper user-plane traffic analysis
* Further 5G Core experimentation
* Additional network slicing experiments
* Integration with MATLAB-based communication-system simulations
* Digital modulation and BER analysis
* Connecting physical-layer concepts with network-level experimentation
* More detailed performance measurements

---

## Project Context

| Field              | Details                                         |
| ------------------ | ----------------------------------------------- |
| Student            | **SHARMI R**                                    |
| Course             | PEP                                             |
| Program            | EEC                                             |
| Domain             | 5G / Wireless Communication / Networking        |
| Project Type       | Hands-on Testbed Deployment & Protocol Analysis |
| Core               | Open5GS                                         |
| RAN / UE Simulator | UERANSIM                                        |
| Operating System   | Ubuntu 22.04                                    |
| Virtualization     | VirtualBox                                      |

---

## Conclusion

This project provided hands-on experience with the deployment and validation of a **5G Standalone Core Network testbed** using Open5GS and UERANSIM.

The practical work covered the complete path from **5G Core operation and gNB connectivity to UE registration, authentication, PDU session establishment, and user-plane data communication**.

Packet captures and protocol analysis provided additional evidence for understanding how 5G signaling procedures operate across different network functions and interfaces.

Overall, the project strengthened my practical understanding of **5G Core networks, wireless communication protocols, Linux networking, virtualization, and packet-level network analysis**.
