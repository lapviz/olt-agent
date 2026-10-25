# \# Open Live Timing agent

# 

# Releases of the \*\*Open Live Timing agent\*\*: it reads the scoreboard output (RMonitor) of the timing software at the

# track and publishes a free live timing page on \[Open Live Timing](https://openlivetiming.com). It only reads the

# output and never writes to the timing software.

# 

# \*\*Guide\*\*: <https://openlivetiming.com/connect/rmonitor>

# 

# \## Download

# 

# \[Latest release](https://github.com/lapviz/olt-agent/releases/latest):

# 

# | System | File |

# |---|---|

# | Windows (timing PC) | `olt-agent-<version>-win-x64.zip`, then `install-agent-windows.ps1` (Windows service) |

# | Linux x64 / ARM64 (mini PC) | `olt-agent-<version>-linux-x64.tar.gz`, `olt-agent-<version>-linux-arm64.tar.gz` |

# | Docker | `ghcr.io/lapviz/olt-agent` (with `agent.compose.yml`, in the Linux archives) |

# 

# `SHA256SUMS` lists the checksums of each release.

# 

# \## In short

# 

# 1\. In the back office of your live timing page, create a pairing code (valid 10 minutes, once).

# 2\. `OpenLiveTiming.Worker pair --code ABCD-EFGH --source 127.0.0.1:50000` (on Windows: `install-agent-windows.ps1 -Code … -Source …`).

# 3\. The agent shows up online in the back office within 30 seconds.

# 

# Help: info@openlivetiming.com

# 

# Product names are trademarks of their respective owners; Open Live Timing is not affiliated with them.

# 

