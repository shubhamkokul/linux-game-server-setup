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

### Why Docker for game servers?

Each game server runs in an isolated container — start, stop, and update independently without touching anything else on the server. Adding a new game is one `docker run` command. Cleanup is clean — `docker rm` and it's gone.

### Install Docker Engine

```bash
# Remove any old versions
sudo apt remove -y docker.io docker-doc docker-compose podman-docker containerd runc

# Add Docker's official GPG key
sudo apt install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add Docker repo
# Note: use 'noble' (24.04 codename) — Docker repo may not have 26.04 entry yet
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu noble stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Add your user to the docker group

```bash
sudo usermod -aG docker <username>
# log out and back in for group change to take effect
```

### Verify

```bash
docker run hello-world
docker --version
docker compose version
```

---

## Phase 3 — Satisfactory Dedicated Server

### Why a dedicated `satisfactory` user?

Same reason we don't use root — if the game server process gets exploited, the attacker only has access to that user's home directory, not the whole system.

### Install SteamCMD

```bash
sudo apt install -y software-properties-common
sudo add-apt-repository multiverse
sudo dpkg --add-architecture i386
sudo apt update
sudo apt install -y steamcmd lib32gcc-s1
```

### Create a dedicated user

```bash
sudo useradd -m -s /bin/bash satisfactory
sudo su - satisfactory
```

### Download the Satisfactory dedicated server

App ID `1690800` is the dedicated server — free, no game ownership required.

```bash
# Stable branch
/usr/games/steamcmd +force_install_dir /home/satisfactory/server +login anonymous +app_update 1690800 validate +quit

# Experimental branch (latest features, more bugs)
/usr/games/steamcmd +force_install_dir /home/satisfactory/server +login anonymous +app_update 1690800 -beta experimental validate +quit
```

To switch branches later, just re-run with the other command — same directory, files get overwritten.

### Test run

```bash
/home/satisfactory/server/FactoryServer.sh -multihome=0.0.0.0
```

Watch for:
```
LogServer: Display: Server API listening on 0.0.0.0:7777
LogNet: IpNetDriver listening on port 7777
```

`Ctrl+C` to stop once confirmed working.

### Open firewall ports

> Note: Satisfactory 1.0+ uses ports 7777 and 8888. The older ports 15000/15777 are no longer needed.

```bash
sudo ufw allow 7777/udp
sudo ufw allow 7777/tcp
sudo ufw allow 8888/tcp
```

### systemd service

Create `/etc/systemd/system/satisfactory.service`:

```ini
[Unit]
Description=Satisfactory Dedicated Server
After=network.target

[Service]
User=satisfactory
WorkingDirectory=/home/satisfactory/server
ExecStart=/home/satisfactory/server/FactoryServer.sh -multihome=0.0.0.0
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now satisfactory
sudo systemctl status satisfactory
```

### Useful commands

```bash
sudo systemctl start satisfactory      # start
sudo systemctl stop satisfactory       # stop
sudo systemctl restart satisfactory    # restart
sudo journalctl -fu satisfactory       # live logs
```

### Convenience scripts

Typing `sudo systemctl` every time gets old. Create wrapper scripts so any admin user can start/stop with a single command.

```bash
sudo vi /usr/local/bin/start-satisfactory
```

```bash
#!/bin/bash
sudo systemctl start satisfactory
echo "Satisfactory server starting..."
sudo systemctl status satisfactory --no-pager
```

```bash
sudo vi /usr/local/bin/stop-satisfactory
```

```bash
#!/bin/bash
sudo systemctl stop satisfactory
echo "Satisfactory server stopped."
```

```bash
sudo chmod +x /usr/local/bin/start-satisfactory
sudo chmod +x /usr/local/bin/stop-satisfactory
```

Allow your admin user to run them without a password prompt:

```bash
sudo visudo
```

Add at the bottom:

```
<username> ALL=(ALL) NOPASSWD: /bin/systemctl start satisfactory, /bin/systemctl stop satisfactory, /bin/systemctl status satisfactory
```

Now `start-satisfactory` and `stop-satisfactory` work from anywhere.

### Connect from the game

1. Open Satisfactory → **Play → Server Manager → Add Server**
2. Enter `<server-ip>:7777`
3. Claim the server — set an admin password on first connection
4. Set a join password in Server Settings (what friends need to enter)
5. Create a new session — enable Creative Mode and Flight here if you want them

---

## Phase 4 — Subnautica 2

> Coming soon
