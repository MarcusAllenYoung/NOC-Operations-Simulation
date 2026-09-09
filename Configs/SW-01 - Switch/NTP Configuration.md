# NTP Configuration (SW-01)

NTP was configured on SW-01 during Phase 3 to synchronize the switch clock with RTR-01 for consistent monitoring and logging timestamps.

### NTP Design

| Setting | Value |
| --- | --- |
| NTP Client | SW-01 |
| NTP Server | RTR-01 |
| NTP Server Address | `10.10.20.1` |
| Purpose | Consistent monitoring and syslog timestamps |

RTR-01 synchronizes with an external NTP source, while SW-01 uses RTR-01 as its internal NTP source.

---

### Configuration Commands

#### NTP Server

```text
ntp server 10.10.20.1
```

---

⬅️ [Back to Monitoring & Alerting](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Monitoring%20&%20Alerting.md)
