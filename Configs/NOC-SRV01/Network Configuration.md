# Network Configuration (NOC-SRV01)

NOC-SRV01 was deployed as an Ubuntu Server virtual machine in VMware Workstation Pro to provide centralized monitoring and logging for the branch network.

### Network Settings

| Setting | Value |
| --- | --- |
| Hostname | noc-svr01 |
| Platform | Ubuntu Server |
| Virtualization | VMware Workstation Pro |
| Network Mode | Bridged |
| IPv4 Address | `192.168.1.50/24` |
| Default Gateway | `192.168.1.254` |
| Primary DNS | `192.168.1.254` |
| Secondary DNS | `8.8.8.8` |
| Interface | ens33 |

VMware bridged networking allows NOC-SRV01 to operate directly on the upstream `192.168.1.0/24` network while remaining logically separate from the simulated branch network.

---

### Network Configuration

NOC-SRV01 was assigned the static IPv4 address:

```text
192.168.1.50/24
```

The AT&T gateway provides the server's default path toward external networks:

```text
default via 192.168.1.254
```

DNS resolution is provided by the upstream gateway with Google's public DNS server configured as an additional resolver.

[Back to Monitoring & Alerting](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Monitoring%20%26%20Alerting.md)
