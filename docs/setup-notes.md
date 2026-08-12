# 5G SA Core Network Testbed — Setup Notes

## 1. Project Context

This document records the practical setup, operation, validation, troubleshooting, and protocol-analysis activities performed on a **5G Standalone (SA) network testbed**.

The testbed used **Open5GS** as the 5G Core and **UERANSIM** to provide a simulated 5G gNB and UE. The environment was operated inside an **Ubuntu 22.04 virtual machine using Oracle VirtualBox**.

The practical work focused on understanding 5G network architecture and validating the complete path from gNB connectivity and UE registration to PDU session establishment and user-plane communication.

**Student:** SHARMI R

---

## 2. Testbed Environment

The practical environment consisted of:

| Component             | Technology             |
| --------------------- | ---------------------- |
| Operating System      | Ubuntu 22.04           |
| Virtualization        | Oracle VirtualBox      |
| 5G Core               | Open5GS                |
| RAN / UE Simulator    | UERANSIM               |
| Packet Analysis       | Wireshark              |
| Command-Line Analysis | tshark                 |
| Packet Capture        | tcpdump                |
| Network Utilities     | Linux networking tools |
| Service Management    | systemd / systemctl    |

The testbed was operated as a virtualized laboratory environment, allowing the 5G Core, simulated RAN, and UE components to be configured and tested within the Ubuntu system.

---

## 3. Open5GS Core Network Startup

The Open5GS 5G Core was prepared and the required network functions were started within the Ubuntu environment.

Service status was checked using Linux service-management commands, and logs were monitored during operation to verify service availability and investigate configuration or connectivity issues.

The major 5G Core functions involved in the practical were:

* **AMF** — Access and Mobility Management Function
* **SMF** — Session Management Function
* **UPF** — User Plane Function
* **AUSF** — Authentication Server Function
* **UDM** — Unified Data Management
* **UDR** — Unified Data Repository
* **PCF** — Policy Control Function
* **NRF** — Network Repository Function
* **NSSF** — Network Slice Selection Function

The Core Network functions provided the control-plane and user-plane services required for UE registration and PDU session establishment.

---

## 4. UERANSIM gNB Startup

After the Open5GS services were operational, the UERANSIM simulated gNB was started.

The gNB configuration included the parameters required for communication with the 5G Core, including:

* PLMN configuration
* Tracking Area Code (TAC)
* AMF address
* SCTP communication parameters
* gNB identification parameters

The gNB established an **SCTP association** with the AMF and performed the **NG Setup** procedure.

### Observed NG Setup

The main signaling sequence was:

```text id="f5c3b9"
UERANSIM gNB
      │
      │  SCTP Association
      ▼
     AMF
      │
      │  NG Setup Request
      ▼
     AMF
      │
      │  NG Setup Response
      ▼
UERANSIM gNB
```

Successful completion of the NG Setup procedure confirmed connectivity between the simulated gNB and the AMF.

---

## 5. UE Configuration and Startup

The UERANSIM simulated UE was configured using subscriber information corresponding to the subscriber provisioned in the Open5GS Core.

The UE configuration involved parameters such as:

* SUPI / IMSI
* Authentication information
* PLMN
* DNN
* UE and gNB configuration parameters

The simulated UE was then started using UERANSIM.

The UE interacted with the simulated gNB and 5G Core to perform registration and establish network connectivity.

---

## 6. UE Registration and Authentication

The UE registration procedure was monitored through UERANSIM output, Open5GS logs, and packet captures.

The major stages studied were:

1. Cell selection
2. RRC setup
3. Initial UE signaling
4. 5G Registration Request
5. Authentication procedure
6. Security Mode procedure
7. Registration Accept
8. Registration Complete

The registration process demonstrated the interaction between the UE, simulated gNB, and 5G Core during network access and authentication.

The UE successfully reached the **registered state** during validation.

---

## 7. PDU Session Establishment

After successful UE registration, a **PDU session** was established.

The procedure involved coordination between:

* UE
* gNB
* AMF
* SMF
* UPF

The session establishment included NAS and NGAP signaling, together with **PFCP-based control between the SMF and UPF**.

Successful establishment was verified through the observed signaling and the activation of the UE data tunnel.

The UE data interface used for user-plane communication was:

```text id="d3n7hx"
uesimtun0
```

The presence of the UE tunnel provided the interface required for testing end-to-end user-plane connectivity.

---

## 8. User-Plane Connectivity Validation

User-plane connectivity was validated through the UE data tunnel using `nr-binder`.

A connectivity test was performed to verify that packets could travel through the simulated 5G network.

### Connectivity Test Results

| Parameter           |     Observed Result |
| ------------------- | ------------------: |
| Packets Transmitted |                  18 |
| Packets Received    |                  18 |
| Packet Loss         |                  0% |
| Average RTT         | Approximately 16 ms |

The results indicate successful packet transmission through the simulated UE data path.

The **0% packet loss** and successful packet reception provided practical evidence that the user-plane tunnel was operational during the test.

---

## 9. Packet Capture and Protocol Analysis

Packet-level traffic was captured and analyzed using:

* **Wireshark**
* **tshark**
* **tcpdump**

The purpose of the analysis was to correlate observed packets with the corresponding 5G procedures.

### Protocols Studied

| Protocol | Purpose                                |
| -------- | -------------------------------------- |
| SCTP     | Transport for NGAP signaling           |
| NGAP     | gNB–AMF control-plane signaling        |
| NR RRC   | UE–gNB radio control signaling         |
| NAS      | UE–Core mobility and session signaling |
| PFCP     | SMF–UPF session control                |
| GTP      | User-plane tunneling                   |

The packet analysis helped identify the relationship between control-plane signaling and the resulting user-plane connectivity.

---

## 10. Validation Methodology

The testbed was validated progressively rather than relying only on an application-level connectivity test.

```text id="q7m9nd"
Open5GS Core Services
          │
          ▼
gNB–AMF Connectivity
          │
          ▼
NG Setup
          │
          ▼
UE Registration
          │
          ▼
Authentication
          │
          ▼
PDU Session Establishment
          │
          ▼
UE Data Tunnel
          │
          ▼
End-to-End Connectivity
          │
          ▼
Packet-Level Verification
```

This layered validation approach helped identify whether each stage of the 5G SA connection was functioning correctly.

---

## 11. Troubleshooting and Verification

During the practical work, troubleshooting and verification were performed using multiple sources of information rather than relying on a single tool.

The following were used:

* Linux service status
* Open5GS service logs
* UERANSIM logs
* Linux network configuration
* SCTP connectivity checks
* Packet captures
* Wireshark protocol inspection
* `tcpdump`
* `tshark`

This helped distinguish between issues related to:

* Core network services
* gNB–AMF connectivity
* UE registration
* Session establishment
* User-plane connectivity

The troubleshooting process provided practical experience in diagnosing problems across different layers of a virtualized 5G network.

---

## 12. Validation Outcome

The main validation objectives were successfully achieved during the practical session.

| Validation Stage          | Outcome    |
| ------------------------- | ---------- |
| Open5GS Core Operation    | Verified   |
| gNB–AMF Connectivity      | Successful |
| NG Setup                  | Successful |
| UE Registration           | Successful |
| Authentication            | Successful |
| PDU Session Establishment | Successful |
| UE Data Tunnel            | Active     |
| User-Plane Connectivity   | Successful |
| Packet Capture            | Completed  |
| Protocol Analysis         | Completed  |

The successful validation of these stages demonstrated the functional operation of the simulated 5G Standalone testbed.

---

## 13. Practical Learning

The practical work provided hands-on exposure to:

* 5G Standalone Core architecture
* Open5GS network-function operation
* UERANSIM gNB and UE simulation
* gNB–AMF communication
* SCTP and NGAP signaling
* RRC and NAS procedures
* 5G registration and authentication
* PDU session establishment
* SMF–UPF interaction
* PFCP session control
* GTP-based user-plane communication
* Linux service management
* Linux networking
* Virtualized network testbeds
* Packet capture and Wireshark analysis
* Network troubleshooting

A major learning outcome was understanding how **control-plane signaling, Core Network functions, and user-plane traffic work together to provide end-to-end connectivity in a 5G SA network**.

The practical also demonstrated the importance of using packet-level evidence and system logs to validate network behavior.

---

## 14. Scope of the Work

This project focused on **deployment, operation, validation, troubleshooting, and protocol-level analysis** of the testbed.

The project did not involve development of the Open5GS or UERANSIM source code. These are external open-source technologies used as part of the practical environment.

The repository documents the practical work, observations, configurations, validation results, and analysis performed during the project.

---

## 15. Conclusion

The setup and validation activities provided practical experience with a complete simulated **5G Standalone network environment**.

The successful progression from **Core Network startup → gNB connectivity → UE registration → authentication → PDU session establishment → UE data tunnel → end-to-end connectivity** demonstrated the operation of the testbed across both control-plane and user-plane functions.

Packet capture and protocol analysis further helped connect theoretical 5G concepts with actual signaling and traffic observed in the test environment.

This practical work strengthened my understanding of **5G Core networks, wireless communication protocols, Linux networking, virtualization, troubleshooting, and packet-level network analysis**.
