## SSH Configuration (SW-01)

SSH was configured on SW-01 during Phase 1 to provide secure remote command-line access to the switch for network administration and troubleshooting.


### SSH Management Design

| Setting | Configuration |
| --- | --- |
| Hostname | SW-01 |
| Management IP | `10.10.20.2/24` |
| Management VLAN | VLAN 20 |
| Domain Name | `noc.lab` |
| SSH Version | SSHv2 |
| Authentication | Local user database |
| VTY Lines | 0–15 |
| Allowed Remote Protocol | SSH |

Remote SSH connections to SW-01 use the VLAN 20 management SVI configured at `10.10.20.2/24`.

---

### Configuration Commands

#### Domain Name and Local Authentication

```text
ip domain-name noc.lab
username <REDACTED> privilege 15 secret <REDACTED>
```

#### RSA Keys and SSH Version

```text
crypto key generate rsa modulus 2048
ip ssh version 2
```

#### VTY Remote Access

```text
line vty 0 15
 login local
 transport input ssh
 exec-timeout 15 0
```

> Authentication credentials have been redacted from the public documentation.

⬅️ [Back to Network Deployment](../../Network%20Deployment.md#-sw-01---switch)
