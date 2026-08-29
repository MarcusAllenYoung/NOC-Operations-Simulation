# 02 | WAN & Internet Connectivity 🌐

Extending the internal NOC lab to the Internet while maintaining the segmented network established during Phase 1.

---

## 1. Purpose & Objectives 🎯

The purpose of this phase is to provide Internet connectivity to the NOC Operations Simulation by connecting RTR-01 to the existing home gateway and implementing outbound NAT/PAT.

| Design Goal | Purpose |
| --- | --- |
| WAN connectivity | Connect RTR-01 to the upstream Internet gateway |
| Default routing | Provide a path from the lab toward external networks |
| NAT/PAT | Translate private lab addresses for Internet access |
| Preserve segmentation | Maintain the existing USERS and MANAGEMENT networks |
| Internet validation | Verify end-to-end connectivity from the lab to the Internet |
