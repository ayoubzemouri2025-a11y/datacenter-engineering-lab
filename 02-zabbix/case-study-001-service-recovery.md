# Case Study 001 — Zabbix Server Service Recovery and VM Diagnostics

**Status:** Zabbix service recovery verified; virtualization timing issue unresolved  
**Environment:** Personal Windows 11 / VirtualBox 7.2.20 / Ubuntu Server 24.04.5 LTS / Zabbix 7.0 lab

## Objective
Restore Zabbix monitoring after finding the server service inactive, then investigate recurring guest CPU soft-lockup and clocksource warnings.

## Initial findings
- VM provisioned with 2 vCPUs, 3.8 GiB RAM, execution cap 100%, PAE/NX enabled, paravirtualization set to Default.
- Root filesystem approximately 19 GB, with about 11 GB free at initial inspection.
- `zabbix-server` was inactive and disabled; `zabbix-agent` and `apache2` were active.
- Earlier successful service starts were present in the journal; the original reason the service was inactive was not established.

## Recovery actions and verification
```bash
systemctl is-active zabbix-server
systemctl is-enabled zabbix-server
sudo systemctl status zabbix-server --no-pager -l
sudo journalctl -u zabbix-server -n 30 --no-pager
sudo systemctl start zabbix-server
sudo systemctl enable zabbix-server
systemctl is-active zabbix-server
systemctl is-enabled zabbix-server
sudo ss -lntp
```
- Zabbix Server became **active**, and boot-time enablement was confirmed.
- Frontend became accessible from the Windows host through the existing NAT port forwarding.
- Listeners observed on TCP 22 (SSH), 80 (Apache), 10050 (agent), 10051 (server); MySQL bound to loopback.
- Zabbix dashboard later showed one available monitored host and active data collection.

## Virtualization and kernel investigation
Observed in Ubuntu:
- Repeated `watchdog: BUG: soft lockup` messages, including durations of 400, 2368 and 1476 seconds.
- Repeated `clocksource: Long readout interval` warnings and workqueue latency messages.
- During a later snapshot CPU was approximately 95% idle, with 2.8 GiB RAM available and no swap used.
- In a subsequent roughly 20-minute observation window, no additional soft lockup was shown, although clocksource and workqueue warnings were present in that boot's logs.

Windows and VirtualBox checks:
- Hardware virtualization enabled; `systemd-detect-virt` returned `oracle`.
- Windows boot `hypervisorlaunchtype` was `Auto`.
- Windows virtualization-based security status was `2` (running); security services running were reported as `{2,7}`.
- Windows Memory Integrity was enabled; VirtualMachinePlatform enabled.
- VirtualBox log included `WHvCapabilityCodeHypervisorPresent is TRUE`, `PGM: Enabling NEM mode`, and `NEMR3Init: Snail execution mode is active!`.

**Interpretation:** VirtualBox is using a Windows-hypervisor-backed, reduced-performance execution path. Host scheduling/timing is a plausible contributor to the guest warnings, **not a proven root cause**. No Windows security feature, VM CPU allocation or kernel watchdog setting was disabled as part of this investigation.

## Useful commands
```bash
sudo journalctl -k -b --no-pager | grep -iE 'soft lockup|watchdog: BUG|clocksource: Long'
sudo journalctl -k -f
top -b -n 1 | head -n 20
free -h
uptime
systemd-detect-virt
```

## Next checks
- Continue observing for recurrence during normal lab use.
- Correlate any new guest timing warnings with host power/scheduling events.
- Consider controlled performance comparisons only with backups and an explicit assessment of Windows security implications.

## Confidentiality
Only personal/simulated lab details. Do not publish credentials, identifying host details, employer systems or unredacted screenshots.

## Lessons learned
Service state and startup enablement are separate. Monitoring recovery must be verified independently of a frontend loading. Kernel soft-lockup warnings cannot be diagnosed from CPU utilization alone; host virtualization behavior may matter.
