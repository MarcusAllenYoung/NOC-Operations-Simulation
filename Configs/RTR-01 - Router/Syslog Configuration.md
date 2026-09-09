# Syslog Configuration (RTR-01)

Syslog was configured on RTR-01 during Phase 3 to forward network-device events to NOC-SRV01 for centralized logging and troubleshooting.

### Syslog Settings

| Setting | Value |
| --- | --- |
| Syslog Server | NOC-SRV01 |
| Server Address | `192.168.1.50` |
| Source Interface | Gi0/0 |
| Source Address | `192.168.1.86` |
| Logging Level | Informational |

NOC-SRV01 receives and stores RTR-01 syslog messages alongside events from SW-01, providing a centralized source of network-device logs.

---

### Configuration Commands

#### Remote Syslog Server

```text
logging host 192.168.1.50
```

#### Source Interface

```text
logging source-interface GigabitEthernet0/0
```

#### Logging Level

```text
logging trap informational
```

---

⬅️ [Back to Monitoring & Alerting](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Monitoring%20%26%20Alerting.md)
