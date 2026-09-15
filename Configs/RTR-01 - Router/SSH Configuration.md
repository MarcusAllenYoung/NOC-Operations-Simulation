## SSH Configuration (RTR-01)

SSH was configured on RTR-01 during Phase 1 to provide secure remote command-line access to the router for network administration and troubleshooting.


### SSH Management Design

| Setting | Configuration |
| --- | --- |
| Hostname | RTR-01 |
| Domain Name | `noc.lab` |
| SSH Version | SSHv2 |
| Authentication | Local user database |
| VTY Lines | 0–4 |
| Allowed Remote Protocol | SSH |

SSH was used instead of Telnet so remote management traffic is encrypted.

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
