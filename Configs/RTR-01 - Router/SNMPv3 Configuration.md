# SNMPv3 Configuration (RTR-01)

SNMPv3 was configured on RTR-01 during Phase 3 to allow LibreNMS to securely collect operational and performance information from the router.

### SNMPv3 Settings

| Setting | Value |
| --- | --- |
| Monitoring Server | NOC-SRV01 |
| Monitoring Platform | LibreNMS |
| Security Level | `authPriv` |
| Authentication | SHA |
| Privacy | AES 128 |
| SNMP Version | SNMPv3 |

SNMPv3 provides authenticated and encrypted monitoring communication between LibreNMS and RTR-01.

Authentication and privacy credentials are intentionally excluded from this repository.

---

### Configuration Commands

#### SNMPv3 Group

```text
snmp-server group NOC-MONITOR v3 priv
```

#### SNMPv3 User

```text
snmp-server user NOC-LIBRENMS NOC-MONITOR v3 auth sha <AUTH-PASSWORD> priv aes 128 <PRIVACY-PASSWORD>
```

`<AUTH-PASSWORD>` and `<PRIVACY-PASSWORD>` are placeholders. Actual credentials are not stored in the repository.

---

⬅️ [Back to Monitoring & Alerting](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Monitoring%20%26%20Alerting.md)
