# Open Live Timing agent

The **Open Live Timing agent** reads the scoreboard output (RMonitor) of your timing software at the track and
publishes a free live timing page on [Open Live Timing](https://openlivetiming.com) for drivers, teams and
spectators. It only **reads** the output: it never writes to the timing software, and nothing changes in the way you
time the sessions.

Step-by-step guide with your own pages when you are signed in: **<https://openlivetiming.com/connect/rmonitor>**

## Quick start

1. **Live timing page**: sign in on [openlivetiming.com](https://openlivetiming.com), create a live timing page (or
   use yours), then click 🛰️ next to it in the list of your pages.
2. **Pairing code**: accept the terms and create a pairing code. It is valid 10 minutes, once.
3. **Scoreboard output**: in your timing software, turn on the RMonitor scoreboard output (TCP, port 50000 by default
   in MYLAPS Orbits) and note the address of the timing PC.
4. **Install** the agent, on the timing PC or on a computer of the same network, with the code (below).
5. **Check**: the agent shows up as *online* on the 🛰️ page within 30 seconds, connected to the timing software, with
   the session it reads. Share the link of your live timing page.

## Download

[Latest release](https://github.com/lapviz/olt-agent/releases/latest). One native executable, nothing else to install.

| System | File |
|---|---|
| Windows 10/11 (timing PC) | `olt-agent-<version>-win-x64.zip` |
| Linux x64 (mini PC) | `olt-agent-<version>-linux-x64.tar.gz` |
| Linux ARM64 (Raspberry Pi 4/5, ARM mini PC) | `olt-agent-<version>-linux-arm64.tar.gz` |
| Docker (Linux x64 / ARM64) | `ghcr.io/lapviz/olt-agent` |

`SHA256SUMS` lists the checksums of each release.

## Windows: install as a service

Unzip the archive, then in **PowerShell run as administrator**, in the unzipped folder:

```powershell
.\install-agent-windows.ps1 -Code ABCD-EFGH -Source 127.0.0.1:50000
```

- `-Source`: `127.0.0.1:50000` when the timing software runs on the same PC, else `<address of the timing PC>:50000`.
- The script copies the agent to `C:\Program Files\OpenLiveTiming Agent`, pairs it, and installs the Windows service
  **OpenLiveTimingAgent** (starts with Windows, restarts after a failure, runs without administrator rights).
- Running it again with a new code pairs the agent again. Remove it with `.\install-agent-windows.ps1 -Uninstall`.
- Logs: `C:\Program Files\OpenLiveTiming Agent\logs`.

Without the service (a quick test): `.\olt-agent.exe pair --code ABCD-EFGH --source 127.0.0.1:50000`, then `.\olt-agent.exe`.

## Linux

```sh
tar -xzf olt-agent-<version>-linux-arm64.tar.gz && cd olt-agent
./olt-agent pair --code ABCD-EFGH --source 192.168.1.20:50000
./olt-agent
```

Needs OpenSSL and CA certificates, present on any standard installation (`sudo apt install libssl3 ca-certificates`
on a minimal one). To start it with the computer, a systemd service (`/etc/systemd/system/olt-agent.service`, with
the folder of the agent in `/opt/olt-agent`):

```ini
[Unit]
Description=Open Live Timing agent
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/opt/olt-agent/olt-agent agent --config /opt/olt-agent/agent.json
WorkingDirectory=/opt/olt-agent
Restart=always
RestartSec=10
User=olt-agent

[Install]
WantedBy=multi-user.target
```

```sh
sudo useradd --system --no-create-home olt-agent && sudo chown -R olt-agent /opt/olt-agent
sudo systemctl enable --now olt-agent
```

## Docker

`agent.compose.yml` is in the Linux archives:

```sh
mkdir -p data && sudo chown -R 1654:1654 data
docker compose -f agent.compose.yml run --rm agent pair --code ABCD-EFGH --source 192.168.1.20:50000 --config /data/agent.json
docker compose -f agent.compose.yml up -d
```

## Commands

| Command | Does |
|---|---|
| `olt-agent pair --code <code> --source <source> [--server https://olt.live] [--config agent.json] [--name <computer>]` | Exchanges the pairing code for the key of your live timing page and writes `agent.json` |
| `olt-agent` (or `olt-agent agent [--config agent.json]`) | Reads the feed and publishes it; reports its status every 30 seconds |
| `olt-agent rmonitor-capture --source <source> --out capture.ndjson.gz [--minutes 0]` | Records the feed to a file (read only), to send us before a first event |
| `olt-agent --version` | Version and platform |

**Source**: `host:port` of the scoreboard output (port 50000 when omitted), or a serial port: `serial://COM3` on
Windows, `serial:///dev/ttyUSB0` on Linux (`?baud=9600` by default). Options after `?`, joined with `&`:
`tz=Europe/Brussels` (time zone of the timing PC, default that of the agent's computer), `encoding=utf-8`
(default `windows-1252`), `names=last-first`, `runtype=race`.

## Files

Next to the executable (or in the Docker volume):

- `agent.json`: written by `pair`, holds the **key of your live timing page**: keep it private (readable by its owner
  only on Linux, by the service and the administrators on Windows).
- `logs/`: one log file per day, two weeks kept.
- `event_logs/`: the recorded feed of each session, 30 days and 500 MB at most.

## Troubleshooting

| On the 🛰️ page | What to check |
|---|---|
| The agent does not show up | Internet access from the agent's computer (HTTPS going out); the logs |
| *offline* | The agent stopped, or no internet for more than 90 seconds |
| *not connected* to the feed | The scoreboard output is on, its address and port, the firewall of the timing PC when the agent runs elsewhere |
| *revoked* | Create a new code and pair again |

`Unknown, used or expired code`: a code works once, within 10 minutes; create a new one.

## Security

- The agent connects to the scoreboard output like a scoreboard would and never sends anything to the timing software.
- It only connects out (HTTPS to Open Live Timing): no port to open on your network.
- Revoking the agent on the 🛰️ page stops it at once; its key no longer works.

Help: info@openlivetiming.com

Product names are trademarks of their respective owners. Open Live Timing is not affiliated with, endorsed or
sponsored by them; the names only describe compatibility.
