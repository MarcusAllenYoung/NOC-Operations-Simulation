# SNMPv3 Configuration (SW-01)

SNMPv3 was configured on SW-01 during Phase 3 to allow LibreNMS to securely collect operational and performance information from the switch.

### SNMPv3 Settings

| Setting | Value |
| --- | --- |
| Monitoring Server | NOC-SRV01 |
| Monitoring Platform | LibreNMS |
| Security Level | authPriv |
| Authentication | SHA |
| Privacy | AES 128 |
| SNMP Version | SNMPv3 |

SNMPv3 provides authenticated and encrypted monitoring communication between LibreNMS and SW-01.


---

### Configuration Commands

#### SNMPv3 Group

```text
snmp-server group NOC-MONITOR v3 priv
```

#### SNMPv3 User

```text
snmp-server user NOC-USER NOC-MONITOR v3 auth sha <AUTH-PASSWORD> priv aes 128 <PRIVACY-PASSWORD>
```


---

⬅️ [Back to Monitoring & Alerting](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Monitoring%20&%20Alerting.md)
