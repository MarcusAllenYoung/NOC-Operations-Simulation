# Syslog Configuration (SW-01)

Syslog was configured on SW-01 during Phase 3 to forward network-device events to NOC-SRV01 for centralized logging and troubleshooting.

### Syslog Settings

| Setting | Value |
| --- | --- |
| Syslog Server | NOC-SRV01 |
| Server Address | `192.168.1.50` |
| Source Interface | Vlan20 |
| Source Address | `10.10.20.2` |
| Logging Level | Informational |

SW-01 uses its VLAN 20 management interface as the source for syslog messages sent to NOC-SRV01.

---

### Configuration Commands

#### Remote Syslog Server

```text
logging host 192.168.1.50
```

#### Source Interface

```text
logging source-interface Vlan20
```

#### Logging Level

```text
logging trap informational
```

---

⬅️ [Back to Monitoring & Alerting](../../Monitoring%20%26%20Alerting.md#-sw-01-configurations)
