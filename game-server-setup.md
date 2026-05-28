# Game Server Setup

Running Satisfactory and Subnautica 2 dedicated servers on a self-hosted Linux VPS. No GPU required — dedicated servers handle game logic and networking, not rendering.

> **Prerequisite:** Complete [SSH Hardening](ssh-hardening.md) before starting this guide.

---

## How a Dedicated Server Works

```mermaid
graph LR
    A[Your PC<br/>renders graphics] -->|sends inputs| S[VPS<br/>game server]
    B[Friend's PC<br/>renders graphics] -->|sends inputs| S
    S -->|sends world state| A
    S -->|sends world state| B

    style S fill:#27AE60,color:#fff
    style A fill:#2980B9,color:#fff
    style B fill:#2980B9,color:#fff
```

The server simulates the world — physics, player positions, saves. Each client renders their own view locally. This is why no GPU is needed on the server.

---

## Phase 2 — Docker Base Stack

> Coming soon

---

## Phase 3 — Satisfactory Dedicated Server

> Coming soon: SteamCMD install, App ID 1690800, systemd service, port configuration

---

## Phase 4 — Subnautica 2

> Coming soon
