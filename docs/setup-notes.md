# 5G SA Core Network Testbed — Setup Notes

## 1. Project Context

This document records the practical setup and validation activities performed while working with a pre-configured 5G Standalone (SA) network testbed.

The testbed used Open5GS as the 5G Core and UERANSIM for simulated gNB and UE operation in an Ubuntu virtual machine.

## 2. Testbed Environment

The practical environment included:

- Ubuntu Linux virtual machine
- Oracle VirtualBox
- Open5GS 5G Core
- UERANSIM
- Wireshark
- tshark
- tcpdump
- Linux system and networking utilities

## 3. Core Network Startup

The Open5GS network functions were started within the Ubuntu environment.

Service status and daemon operation were checked using Linux service-management commands. Logs were also observed to verify successful startup and identify operational issues.

The main functions involved AMF, SMF, UPF, AUSF, UDM, UDR, PCF, NRF and NSSF.

## 4. gNB Startup and NG Setup

The UERANSIM simulated gNB was started after the 5G Core services were operational.

The gNB established an SCTP association with the AMF and performed the NG Setup procedure.

The observed signaling included:

- NG Setup Request
- NG Setup Response
- NGAP signaling
- Successful gNB–AMF connectivity

## 5. UE Registration

The UERANSIM simulated UE was started after successful gNB–AMF connectivity.

The registration procedure included:

1. Cell selection
2. RRC setup
3. Initial UE signaling
4. 5G registration request
5. Authentication
6. Security Mode procedure
7. Registration Accept
8. Registration Complete

The UE successfully reached the registered state.

## 6. PDU Session Establishment

A PDU session was established from the simulated UE.

The procedure involved interaction between the AMF, SMF and UPF.

Successful session establishment was verified through the relevant signaling and by confirming that the UE data tunnel interface became active.

The UE obtained an IPv4 address through:

```text
uesimtun0
## 7. User-Plane Connectivity Validation

The UE data plane was validated using `nr-binder`.

The connectivity test recorded:

- Packets transmitted: 18
- Packets received: 18
- Packet loss: 0%
- Average RTT: approximately 16 ms

This confirmed successful end-to-end user-plane connectivity within the simulated 5G SA testbed.

## 8. Packet Capture and Analysis

Network traffic was captured using tools including:

- tshark
- tcpdump
- Wireshark

The captured traffic was analyzed to understand the signaling and user-plane procedures.

The analysis focused on:

- SCTP
- NGAP
- NR RRC
- NAS
- PFCP
- GTP

## 9. Validation Approach

The testbed was validated at multiple levels:

```text
gNB–AMF Connectivity
        ↓
UE Registration
        ↓
Authentication
        ↓
PDU Session Establishment
        ↓
UE Data Tunnel
        ↓
End-to-End Connectivity
This approach provided protocol-level understanding of 5G SA network operation.

## 10. Practical Learning

The practical work provided hands-on exposure to:

- 5G SA core network architecture
- Open5GS operation
- UERANSIM-based gNB and UE simulation
- Linux-based network troubleshooting
- 5G registration procedures
- PDU session establishment
- User-plane connectivity
- Wireshark packet analysis

The work focused on testbed operation, validation, troubleshooting and protocol analysis rather than development of the Open5GS or UERANSIM source code.
        ↓
Packet-Level Verification
