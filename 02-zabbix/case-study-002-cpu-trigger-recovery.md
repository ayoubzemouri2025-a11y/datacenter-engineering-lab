# Case Study 002 — Zabbix CPU Monitoring, Trigger and Automatic Recovery

**Status:** Completed and verified  
**Platform:** Personal Ubuntu Server VM with Zabbix 7.0.32  
**Scope:** Simulated CPU-load incident on a non-production lab host

## Objective
Create and validate a custom CPU utilization alert, distinguish brief spikes from sustained utilization, and verify automatic incident recovery.

## Baseline
- Zabbix dashboard showed one available monitored host (`Zabbix server`).
- CPU utilization before the sustained test was typically around 5–10%.
- The standard Linux template already included a high-CPU trigger at approximately 90%; this exercise used a separate custom host trigger.

## Trigger configuration
- **Name:** `LAB: CPU utilization above 15% for 2 minutes`
- **Host:** `Zabbix server`
- **Severity:** Warning
- **Expression:**
```text
min(/Zabbix server/system.cpu.util,2m)>15
```
- **PROBLEM event generation:** Single
- **OK event generation:** Expression

### Interpretation
The `min()` function returns the lowest available item value in the preceding two-minute window. The expression becomes true when the lowest sampled CPU utilization is above 15%; it is not a two-minute average. Actual detection timing depends on item collection and evaluation.

## Reproducible procedure
1. Confirm CPU utilization data exists under **Monitoring → Latest data**.
2. Create the custom trigger under **Data collection → Hosts → Zabbix server → Triggers**.
3. Observe baseline utilization and the threshold line on the CPU graph.
4. Perform a short bounded test:
```bash
timeout 30s yes > /dev/null
```
5. Review the CPU graph; a brief spike alone does not satisfy the sustained trigger.
6. Perform a longer, bounded single-process load test:
```bash
timeout 180s yes > /dev/null
```
7. Check **Monitoring → Problems** for the Warning event.
8. After the load ends, check recent/resolved problem history and the CPU graph for recovery.

`yes` generates output continuously, `/dev/null` discards it, and `timeout` stops the test after the specified time. The experiment was performed on a personal lab VM, not production infrastructure.

## Verified evidence
| Measurement | Observed result |
| --- | --- |
| Maximum CPU utilization on test graph | 41.2227% |
| Custom trigger threshold | 15% using `min(...,2m)` |
| PROBLEM event time | 15:00:35, 12 November 2026 |
| Recovery time | 15:01:24, 12 November 2026 |
| Alert duration | 49 seconds |
| Final event status | RESOLVED |

The 49-second duration is the time the **alert** remained in PROBLEM state; it is **not** the duration of generated CPU load. The CPU graph showed utilization dropping back toward baseline, and the event history explicitly showed a recovery timestamp and RESOLVED status.

## Outcome
**PASS** — Zabbix collected CPU metrics, evaluated the custom trigger, generated a Warning event, and automatically resolved it after utilization dropped.

## Limitations and follow-up
- No email, SMS or external notification channel was configured or tested.
- A separate VirtualBox/Ubuntu CPU soft-lockup investigation remains open; a successful monitoring test does not prove the VM timing issue is fixed.
- Next planned exercise: detect and recover from a simulated Apache service outage (not yet performed).
- Screenshots should be sanitized before being added to this public repository.

## Skills demonstrated
Zabbix host/item navigation, CPU metrics, time-window trigger expressions, controlled fault simulation, event history, recovery verification and evidence-based documentation.
