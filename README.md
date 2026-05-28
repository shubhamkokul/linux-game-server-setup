# Linux Game Server Setup

A complete guide to setting up a production-ready Linux VPS from scratch — hardened SSH, Docker base stack, and self-hosted game servers (Satisfactory, Subnautica 2).

Built on SSDNodes (32GB RAM / 8 vCPU / Ubuntu 26.04 LTS). Every step documented with diagrams and explanations of why it matters.

---

## What's Covered

```mermaid
graph LR
    A[Fresh VPS] --> B[SSH Hardening]
    B --> C[Docker Base Stack]
    C --> D[Game Server]
    D --> E[Application Server]

    style A fill:#888,color:#fff
    style B fill:#C0392B,color:#fff
    style C fill:#2980B9,color:#fff
    style D fill:#27AE60,color:#fff
    style E fill:#8E44AD,color:#fff
```

| Guide | What it covers | Status |
|---|---|---|
| [SSH Hardening](ssh-hardening.md) | Non-root user, key auth, UFW, fail2ban, port rotation | ✅ Complete |
| [Game Server Setup](game-server-setup.md) | Docker, SteamCMD, Satisfactory, systemd service, rsync backups | ✅ Complete |

---

## Server Specs

| | |
|---|---|
| Provider | SSDNodes |
| Plan | Standard 32GB RAM |
| vCPU | 8 |
| RAM | 32GB |
| Disk | 480GB SSD |
| OS | Ubuntu 26.04 LTS |

---

## Why Self-Host?

Managed game server hosts (Nitrado, GTXGaming) cost $10-15/month for limited specs and zero control. A 32GB VPS at the same price point runs multiple game servers, an application server, and anything else you want — all on your own terms.

The catch: you have to secure it yourself. That's what this guide is for.
