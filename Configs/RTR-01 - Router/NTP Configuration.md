# NTP Configuration (RTR-01)

NTP was configured on RTR-01 during Phase 3 to provide synchronized time for network monitoring, logging, and troubleshooting.

### NTP Design

| Setting | Value |
| --- | --- |
| NTP Role | Client / Internal Time Source |
| External Source | time.google.com |
| Internal Client | SW-01 |
| Purpose | Consistent event timestamps |

RTR-01 synchronizes with an external NTP source and provides time synchronization to SW-01.

---

### Configuration Commands

#### DNS Resolution

```text
ip domain lookup
```

DNS lookup was enabled because RTR-01 initially could not resolve the hostname of the configured NTP server.

#### External NTP Source

```text
ntp server time.google.com
```

⬅️ [Back to Monitoring & Alerting](../../Monitoring%20%26%20Alerting.md#-rtr-01-configurations)
