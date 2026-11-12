# Data Center Engineering Lab

**Hands-on portfolio | Linux · Zabbix · NetBox · Grafana · SCADA**

A personal learning and engineering portfolio documenting reproducible experiments in infrastructure monitoring, asset management, network services, industrial protocols and troubleshooting.

> **Evidence policy:** This repository distinguishes planned work from completed, tested projects. All infrastructure is personal or simulated. No employer or client infrastructure is documented here.

## Verified lab work
- **[Case Study 001 — Zabbix service recovery and VM diagnostics](02-zabbix/case-study-001-service-recovery.md):** Restored and enabled the Zabbix Server service; diagnosed Windows-hypervisor-backed VirtualBox execution. **Service recovery verified; CPU timing root cause unresolved.**
- **[Case Study 002 — CPU monitoring and automatic recovery](02-zabbix/case-study-002-cpu-trigger-recovery.md):** Created a custom 15% CPU trigger, ran bounded load tests, verified a Warning event at 15:00:35 and automatic recovery at 15:01:24 on 12 November 2026. **Completed.**

## Objectives
- Administer and troubleshoot Ubuntu Linux services.
- Configure Zabbix monitoring, templates, triggers, SNMP and alerting.
- Document racks, devices, IP networks and cabling in NetBox.
- Build operational dashboards with Grafana.
- Simulate industrial equipment and Modbus TCP telemetry safely.
- Integrate these tools into a reproducible virtual data center.

## Training projects

| Area | Focus | Status |
| --- | --- | --- |
| [Linux](01-linux/README.md) | SSH, permissions, systemd, logs, networking, backup | In progress — service troubleshooting documented |
| [Zabbix](02-zabbix/README.md) | Host monitoring, triggers, templates, SNMP | In progress — CPU trigger and recovery verified |
| [NetBox](03-netbox/README.md) | Sites, 42U racks, devices, IPAM, cabling, API | Planned |
| [Grafana](04-grafana/README.md) | Zabbix data source, variables, dashboards | Planned |
| [SCADA](05-scada/README.md) | Simulated PLC, Modbus TCP, alarms, HMI | Planned |
| [Integrated lab](06-integrated-project/README.md) | Simulated cooling fault, monitoring, documentation | Planned |

## Environment
Initial lab: Windows 11 host, Oracle VirtualBox 7.2.20, Ubuntu Server 24.04, Zabbix 7.0. Additional components will be installed only after checking capacity and protecting the existing configuration.

## Learning plan
See the [24-week roadmap](docs/ROADMAP.md) and [lab evidence template](docs/LAB-TEMPLATE.md).

## How to evaluate a project
Each completed project should contain: objective, architecture, prerequisites, reproducible steps, configuration or code, sanitized screenshots, test evidence, troubleshooting notes and lessons learned.

## Security and confidentiality
Never commit credentials, tokens, private keys, production IP addresses, employer documentation or sensitive screenshots. Use synthetic data and review every file before publication. See [security guidance](docs/SECURITY.md).

## Certifications
Certification preparation and exam results are separate from practical projects. Only earned certifications with verifiable credentials will be listed as completed.

_Last updated: 12 November 2026._
