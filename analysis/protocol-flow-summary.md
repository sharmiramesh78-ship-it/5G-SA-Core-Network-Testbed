# 5G SA Protocol Flow Summary

## Overview

This document summarizes the main protocol interactions in a 5G Standalone (SA) core network.

## Main Components

- UE — User Equipment
- gNB — 5G Radio Access Network
- AMF — Access and Mobility Management Function
- SMF — Session Management Function
- UPF — User Plane Function
- UDM — Unified Data Management
- AUSF — Authentication Server Function

## Registration Flow

1. The UE connects to the gNB.
2. The UE sends a registration request.
3. The gNB forwards the request to the AMF.
4. The AMF performs authentication and security procedures.
5. Subscriber information is obtained from the core network functions.
6. The UE is successfully registered with the 5G core.

## PDU Session Establishment

1. The UE requests a PDU session.
2. The AMF forwards the session request to the SMF.
3. The SMF selects and configures the UPF.
4. The UPF establishes the user-plane path.
5. The PDU session is established.
6. User traffic can flow between the UE and the data network through the UPF.

## Protocols and Interfaces

| Interface | Purpose |
|---|---|
| N1 | UE to AMF signaling |
| N2 | gNB to AMF signaling |
| N3 | gNB to UPF user-plane traffic |
| N4 | SMF to UPF control |
| N6 | UPF to Data Network |
| SBI | Communication between 5G Core Network Functions |

## Conclusion

The 5G SA architecture separates control-plane and user-plane functions. Registration establishes secure access to the network, while PDU session establishment creates the path required for user data communication.
