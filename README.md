# Intelligent Anti-Jamming Wireless Communication System Using Star-to-Mesh Hybrid Architecture

> A hybrid star-to-mesh network architecture that detects jamming attacks in real time and dynamically reconfigures topology to eliminate single-point failure and enable self-healing communication — simulated and evaluated in NS-3 with NetAnim visualization.

---

## Table of Contents

- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [System Architecture](#system-architecture)
- [Methodology](#methodology)
- [Tech Stack](#tech-stack)
- [Results](#results)
- [Discussion](#discussion)
- [Conclusion & Future Work](#conclusion--future-work)
- [Repository Structure](#repository-structure)
- [Getting Started](#getting-started)
- [References](#references)
- [Author](#author)

---

## Overview

Wireless sensor networks and IoT deployments overwhelmingly rely on **star topology** — every node talks directly to a central gateway — because it's simple, low-latency, and low-overhead. But that efficiency comes at a cost: if the gateway or its channel is jammed, the entire network goes down. Most existing anti-jamming research (frequency hopping, spread spectrum, power adaptation, RL-based channel selection) works at the signal or MAC layer and doesn't address this structural, topology-level weakness — and many of these methods are too computationally or energy-expensive for resource-constrained IoT nodes.

This project proposes and evaluates an **Intelligent Anti-Jamming Algorithm (IAJA)** that keeps the network in efficient star mode under normal conditions, continuously monitors signal quality, and — upon detecting jamming — dynamically reconfigures the network into a **mesh topology**, enabling sensor nodes to route around the compromised gateway via peer-to-peer multi-hop paths. The system was implemented and evaluated using the **NS-3 network simulator**, with **NetAnim** used for visualization.

---

## Problem Statement

- **Star topology** is efficient but has a single point of failure: jam the gateway, lose the network.
- **Full mesh topology** avoids that failure mode but adds routing overhead, energy cost, and complexity that most IoT nodes can't afford to run at all times.
- **Signal-level anti-jamming techniques** (frequency hopping, spread spectrum, RL-based channel selection) improve resilience but ignore the topology-level vulnerability and are often too resource-intensive for constrained sensor nodes.

The goal: combine the efficiency of star topology during normal operation with the resilience of mesh topology only when needed, using lightweight, real-time jamming detection to decide when to switch.

---

## System Architecture

The simulated network consists of three components:

- **A central gateway node** — coordinates communication and runs centralized jamming detection under normal (star) operation
- **Multiple wireless sensor nodes** — communicate directly with the gateway in star mode; reroute peer-to-peer through neighboring nodes in mesh mode
- **A jammer node** — used to simulate interference for testing detection and recovery behavior

The gateway continuously monitors three signal quality metrics to assess link health:

- **Packet Delivery Ratio (PDR)**
- **Delay (latency)**
- **Throughput**

When these metrics cross predefined thresholds, the gateway triggers the IAJA, which reconfigures the network from star to mesh, enabling adaptive multi-hop routing around the affected link/node.

---

## Methodology

The system operates as an event-driven pipeline with the following phases:

1. **System Initialization** — nodes and gateway are configured with communication protocols, network parameters, and detection thresholds tied to PDR, Delay, and Throughput.
2. **Normal Communication Phase (Star Mode)** — sensor nodes communicate directly with the gateway; low latency, low overhead, recommended for interference-free conditions.
3. **Continuous Monitoring for Jamming Activity** — the gateway continuously evaluates PDR, Delay, and Throughput against thresholds to catch abnormal degradation.
4. **Jamming Detection and Decision Phase** — if metrics stay within thresholds, the network remains in star mode; if not, a jamming event is flagged and a mitigation response is triggered.
5. **Immediate Response: Star-to-Mesh Switching** — the network reconfigures into mesh topology, enabling multi-hop peer-to-peer routing that removes dependence on the (potentially jammed) gateway.
6. **Protocol Adaptation and Channel Hopping** — routing and channel-selection adjustments are applied to avoid congested/interfered frequencies and stabilize packet delivery.
7. **Communication Recovery Phase** — the network stabilizes on mesh paths while continuous monitoring remains active to catch further interference.

This flow — initialization → star mode → monitoring → detection → mesh switch → protocol adaptation → recovery — was modeled as the core methodology diagram of the system.

---

## Tech Stack

| Component | Tool |
|---|---|
| Network simulation | NS-3 |
| Visualization | NetAnim |
| Performance analysis | PDR, Delay, Throughput metrics (custom threshold-based detection logic) |

---

## Results

**Table I — Baseline performance and recovery thresholds**

| Metric | Baseline Value | Recovery Threshold (90%) | Recovery Time |
|---|---|---|---|
| Throughput | 1000 kbps | 900 kbps | 9s |
| PDR | 1.0 | 0.9 | 6s |
| Delay | 3.7 ms | < 4.0 ms | 5s |

Under normal (interference-free) conditions, the network sustains near-ideal throughput, delay, and packet delivery. Recovery thresholds were set at 90% of baseline values, consistent with common practice in resilience/anti-jamming evaluation, to benchmark when the system should trigger its response.

**Table II — Performance before vs. after jamming**

| Metric | Baseline Phase | Disturbed Phase | Improvement (post-recovery) |
|---|---|---|---|
| Throughput | 1000 kbps | 520 kbps | +92% |
| PDR | 1.0 | 0.92 | +8.7% |
| Delay | 3.7 ms | 7.8 ms | −52.6% |
| Recovery Time | — | 9s | Fast |

Jamming causes a sharp drop in throughput and a smaller dip in PDR, alongside increased delay. After the star-to-mesh switch is triggered, the network recovers quickly — with a modest, acceptable increase in latency traded off against a large gain in dependability and continuity.

**Table III — Star topology vs. hybrid star-to-mesh under jamming**

| Parameter | Star Topology (Before Jamming) | Hybrid Star-to-Mesh (After Jamming) |
|---|---|---|
| Packet Delivery Ratio | High | High |
| Latency | Low | Slightly higher during switching |
| Recovery Time | Not applicable | Very low |
| Routing Flexibility | None | High |
| Single-Point Failure | Present | Eliminated |
| Network Reliability | Moderate | High |
| Communication Continuity | Interrupted under attack | Maintained |

*(Figures 2–6 from the paper — throughput/delay per flow under jamming, PDR vs. time, throughput vs. time, and delay comparison with/without jammer — can be added here as images once exported from your NS-3/NetAnim runs.)*

---

## Discussion

The star topology performs well under interference-free conditions — low latency, high PDR — but its dependence on a single centralized gateway makes it fully vulnerable to jamming: disrupt the gateway, and the whole network goes down. Real-time monitoring of PDR, Delay, and Throughput enables accurate jamming detection without heavy computational overhead, and the dynamic star-to-mesh switch maintains communication continuity by letting sensor nodes route around the compromised link via multi-hop paths.

The trade-off is a modest increase in latency during and immediately after topology switching — but given the scale of the reliability and continuity gains, and the fast recovery time observed, this trade-off is favorable. Overall, the results support topology-level defense (rather than only signal-level countermeasures) as a scalable, low-complexity anti-jamming strategy suited to resource-constrained IoT and wireless sensor networks.

---

## Conclusion & Future Work

This project demonstrates that combining lightweight, real-time jamming detection (via PDR/Delay/Throughput monitoring) with dynamic star-to-mesh topology reconfiguration eliminates the single-point-of-failure weakness of conventional star-based IoT networks, while keeping computational and energy overhead low. NS-3/NetAnim simulation results show improved network stability, reduced recovery time, and sustained packet delivery under jamming conditions.

**Planned future work:**
- Evaluate the system in larger-scale, more realistic deployment scenarios
- Incorporate lightweight learning-based decision logic to complement threshold-based detection

---

## Repository Structure

```
├── ns3-simulation/       # NS-3 simulation scripts (topology, jamming scenarios)
├── netanim/              # NetAnim visualization configs/outputs
├── analysis/             # Scripts used to generate performance plots (PDR, throughput, delay)
├── results/              # Output figures and tables (Fig.2–Fig.6, Table I–III)
├── paper/                # IEEE paper (PDF) and related documentation
└── README.md
```

*(Update this to match your actual folder layout once you push the code — see your Delivery-service-webportal repo for a flatter alternative if you prefer files at root level instead of nested folders.)*

---

## Getting Started

```bash
# Clone the repository
git clone https://github.com/Kaviya-118/<repo-name>.git

# Run the NS-3 simulation
# (from your NS-3 installation directory)
./ns3 run scratch/<your-simulation-script>

# Visualize with NetAnim
netanim
# then open the generated .xml trace file
```

*(Replace with your actual NS-3 script name and run instructions once finalized.)*

---

## References

1. H. Pirayesh and H. Zeng, "Jamming attacks and anti-jamming strategies in wireless networks: A comprehensive survey," *IEEE Communications Surveys & Tutorials*, vol. 24, no. 2, pp. 767–809, 2022.
2. P. Wan, Z. Du, Y. Yang, et al., "Fast detection of burst jamming for delay-sensitive Internet of Things applications," *IEEE Internet of Things Journal*, vol. 8, no. 11, pp. 8815–8826, 2021.
3. M. Hanegraaf, S. Sciancalepore, and G. Oligeri, "Weak-jamming detection in IEEE 802.11 networks: Techniques, scenarios, and mobility," arXiv preprint, May 2025.
4. P. Mykytyn, R. Chitauro, Z. Dyka, and P. Langendoerfer, "Channel state information analysis for jamming attack detection in static and dynamic UAV networks: An experimental study," arXiv preprint, Apr. 2025.
5. F. T. Zahra, Y. S. Bostanci, and M. Soyturk, "Real-time jamming detection in wireless IoT networks," *IEEE Access*, vol. 11, pp. 1–12, 2023.
6. A. Forestiero, E. Ferrara, and G. Spezzano, "Real-time jamming detection in wireless IoT networks," *Sensors*, vol. 22, no. 24, art. 9580, 2022.
7. S. Joo, S. Park, H. Shim, Y. Oh, and I. Lee, "Machine learning-based detection and selective mitigation of denial-of-service attacks in wireless sensor networks," *Computers, Materials & Continua*, vol. 82, no. 2, pp. 2475–2494, 2025.

*(Full reference list of 16 sources available in the paper — see `/paper` folder.)*

---

## Author

**Kaviya A**
Electronics & Communication Engineering (2026), Sri Ramakrishna Engineering College, Coimbatore
[LinkedIn](https://www.linkedin.com/in/kaviya-a-236703258) · kaviyaannamalai1@gmail.com

*This repository accompanies an IEEE-format paper prepared for publication.*

