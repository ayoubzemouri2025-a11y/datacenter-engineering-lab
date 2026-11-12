# Case Study 001 — Zabbix Server Service Recovery

**Status:** Partially verified — service recovery confirmed; CPU soft-lockup investigation pending  
**Environment:** Personal Ubuntu Server 24.04.5 LTS VM on VirtualBox, Zabbix 7.0

## Objective
Investigate why the Zabbix Server service was not running and restore monitoring availability.

## Initial observations
- VM: 2 vCPU; 3.8 GiB RAM, about 3.0 GiB available at inspection.
- Root filesystem: 19 GB, 7.6 GB used, approximately 11 GB available.
- `zabbix-server`: inactive and disabled at boot.
- `zabbix-agent` and `apache2`: active.
- Journal showed prior successful Zabbix starts but did not establish the cause of the inactive state.
- Kernel reported CPU soft-lockup warnings; root cause not yet determined.

## Commands used
```bash
lsb_release -a
free -h
df -hT /
nproc
systemctl is-active zabbix-server
systemctl is-active zabbix-agent
systemctl is-active apache2
sudo systemctl status zabbix-server --no-pager -l
sudo journalctl -u zabbix-server -n 30 --no-pager
sudo systemctl start zabbix-server
systemctl is-active zabbix-server
sudo systemctl enable zabbix-server
systemctl is-enabled zabbix-server
sudo ss -lntp
```

## Verified outcome
- Zabbix Server became **active** after a manual start.
- Automatic startup was reported **enabled**.
- Zabbix frontend was accessible from the Windows host.
- Listening TCP ports observed: 22 (SSH), 80 (Apache), 10050 (Zabbix agent), 10051 (Zabbix server); MySQL bound to localhost on 3306 and 33060.

## Technical interpretation
`active` indicates current service state; `enabled` controls boot-time activation. TCP listening state alone does not prove end-to-end reachability or firewall exposure.

## Open questions / next tests
- Investigate CPU soft-lockup warnings using system logs and VirtualBox resource observations.
- Confirm Zabbix service remains healthy after a controlled VM reboot.
- Verify actual Zabbix data collection and item timestamps.
- Add sanitized screenshots of completed tests.

## Confidentiality
This case uses only personal lab information. Do not publish employer infrastructure, credentials, private IPs, API keys or identifying details.

## Lessons learned
Service state and boot enablement are separate. Inspect logs before changing a service; validate the service and web UI after recovery.
