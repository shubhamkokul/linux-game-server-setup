# SSDNode VPS — SSH Hardening & Server Setup

Multi-purpose VPS setup: SSH hardening, Docker, game servers (Satisfactory, Subnautica 2), and application server.

## Specs

| | |
|---|---|
| Provider | SSDNodes |
| Plan | Standard 32GB RAM |
| vCPU | 8 |
| RAM | 32GB |
| Disk | 480GB SSD |
| OS | Ubuntu 26.04 LTS |

---

## Phase 1 — SSH Hardening

### Step 1 — Create a non-root sudo user

```bash
adduser <username>
usermod -aG sudo <username>
groups <username>
# expected: <username> : <username> sudo users
```

### Step 2 — Generate SSH key on local machine

```bash
ssh-keygen -t ed25519 -C "<username>@<hostname>"
cat ~/.ssh/id_ed25519.pub
```

### Step 3 — Copy public key to server

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub <username>@<server-ip>
```

### Step 4 — Test key login

```bash
ssh <username>@<server-ip>
# should connect using key, not password
```

### Step 5 — Harden SSH config

```bash
sudo nano /etc/ssh/sshd_config
```

Set the following:

```
PermitRootLogin no
PasswordAuthentication no
Port 2222
```

### Step 6 — Verify config before restarting

```bash
sudo sshd -t
# no output = config valid
```

Open a second terminal and test the new port before restarting:

```bash
ssh -p 2222 <username>@<server-ip>
```

### Step 7 — Restart SSH

```bash
sudo systemctl restart ssh
```

### Step 8 — Configure UFW firewall

```bash
sudo apt install -y ufw
sudo ufw allow 2222/tcp     # SSH on new port
sudo ufw allow 7777/udp     # Satisfactory game
sudo ufw allow 15000/udp    # Satisfactory beacon
sudo ufw allow 15777/udp    # Satisfactory query
sudo ufw enable
sudo ufw status
```

### Step 9 — Install fail2ban

```bash
sudo apt install -y fail2ban
sudo systemctl enable --now fail2ban
sudo fail2ban-client status
```

---

## Phase 2 — Base Stack

> Coming soon: Docker, Nginx

---

## Phase 3 — Game Server (Satisfactory)

> Coming soon: SteamCMD, dedicated server install, systemd service

---

## Phase 4 — Application Server

> TBD
