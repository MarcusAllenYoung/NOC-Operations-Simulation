# Syslog Receiver Configuration (NOC-SRV01)

NOC-SRV01 was configured as a centralized syslog receiver for Cisco network devices in the NOC Operations Simulation.

Centralized logging allows events from RTR-01 and SW-01 to be collected on the monitoring server for troubleshooting and incident correlation.

### Syslog Design

| Setting | Value |
| --- | --- |
| Syslog Server | NOC-SRV01 |
| Server Address | `192.168.1.50` |
| Service | rsyslog |
| Protocol | UDP |
| Port | 514 |
| Cisco Log File | /var/log/cisco.log |

---

### Cisco Log Sources

NOC-SRV01 accepts syslog messages from the Cisco devices used in the lab.

| Device | Source Address |
| --- | --- |
| RTR-01 | `192.168.1.86` |
| SW-01 | `10.10.20.2` |

Cisco messages are separated from other system logs and written to:

```text
/var/log/cisco.log
```

---

### rsyslog Configuration

Remote UDP syslog reception was enabled on port `514`.

```text
module(load="imudp")
input(type="imudp" port="514")
```

Cisco source addresses are matched and written to the dedicated Cisco log file.

```text
if ($fromhost-ip == '10.10.20.2' or $fromhost-ip == '192.168.1.86') then {
    action(type="omfile" file="/var/log/cisco.log")
    stop
}
```

The rsyslog service was then used to receive and store network-device events centrally on NOC-SRV01.

⬅️[Back to Monitoring & Alerting](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Monitoring%20%26%20Alerting.md)
