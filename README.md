# syscheck

A post-update system health checker for Linux Mint (and Ubuntu-based distros). Scans your system across 15 categories and reports passes, warnings, and failures at a glance — with intelligent noise filtering for known hardware quirks.

```
Linux System Health Check v1.6.4 — Wednesday February 25, 2026 14:28:58
Host: my-machine  |  Kernel: 6.17.0-14-generic  |  User: joshua

══════════════════════════════════════
  1. Disk Usage
══════════════════════════════════════
  [✔] Disk / is 32% full
  [✔] Disk /boot/efi is 7% full
...
══════════════════════════════════════
  SUMMARY
══════════════════════════════════════
  ✔ Passed:   25
  ⚠ Warnings: 0
  ✘ Failed:   0

  ✔ All checks passed! System looks healthy.
```

---

## Features

**Standard checks (always run):**
- Disk & inode usage with thresholds (75% warn / 90% critical)
- RAM & swap pressure
- CPU load vs. core count
- CPU temperature (requires `lm-sensors`)
- Package health — broken configs, held packages, pending updates, autoremovable packages
- Failed systemd services
- Critical journal errors with intelligent noise filtering
- NVIDIA GPU status, VRAM, temperature, and Xid fault detection
- Filesystem mount integrity
- Internet & DNS connectivity
- Log file health — split journal vs. traditional logs with correct fix commands

**Extended checks (`--full`):**
- SMART disk health via `smartctl`
- Listening TCP ports
- World-writable file scan with known-safe exclusions
- SetUID/SetGID binary audit

**Other features:**
- Timestamped logs saved to `/var/log/syscheck/` (with fallback to `~/.local/share/syscheck/logs/`)
- Intelligent filtering of known-harmless hardware noise (ACPI quirks, PCIe ACS violations, etc.)
- Fully `shellcheck` compliant
- Color-coded terminal output

---

## Requirements

- Bash 4+
- Ubuntu / Linux Mint (or any Debian-based distro)
- Standard tools: `df`, `free`, `awk`, `systemctl`, `journalctl`, `ping`, `ss`

**Optional (unlocks additional checks):**
```bash
sudo apt install lm-sensors smartmontools
sudo sensors-detect    # one-time CPU sensor setup
```

---

## Installation

**Quick install:**
```bash
sudo curl -o /usr/local/bin/syscheck \
  https://raw.githubusercontent.com/YOUR_USERNAME/syscheck/main/syscheck
sudo chmod +x /usr/local/bin/syscheck
```

**Manual install:**
```bash
git clone https://github.com/YOUR_USERNAME/syscheck.git
sudo cp syscheck/syscheck /usr/local/bin/syscheck
sudo chmod +x /usr/local/bin/syscheck
```

**Set up log directory (optional but recommended):**
```bash
sudo mkdir -p /var/log/syscheck
sudo chown $USER:adm /var/log/syscheck
sudo chmod 750 /var/log/syscheck
```

**Set up passwordless SMART checks (optional):**
```bash
sudo visudo -f /etc/sudoers.d/syscheck
# Add this line:
# YOUR_USERNAME ALL=(ALL) NOPASSWD: /usr/sbin/smartctl
```

---

## Usage

```
syscheck [OPTION]...
```

| Option | Description |
|--------|-------------|
| *(none)* | Run standard checks |
| `--full` | Run standard + extended checks |
| `--log` | Save output to timestamped log file |
| `--full --log` | Run all checks and save log |
| `--version` | Display version and exit |
| `--help` | Display help and exit |

**Examples:**
```bash
syscheck                 # Quick health check
syscheck --full          # Full check including SMART, ports, security audit
syscheck --log           # Standard check with log saved
syscheck --full --log    # Everything, logged
```

---

## Log Files

Logs are saved to `/var/log/syscheck/` by default:
```
/var/log/syscheck/syscheck_2026-02-25_14-28-58.log
```

If the system log directory isn't accessible, logs fall back to:
```
~/.local/share/syscheck/logs/syscheck_2026-02-25_YYYY-MM-DD_HH-MM-SS.log
```

---

## Hardware-Specific Notes

This script was developed and tested on an **ASUS ROG Strix G16** with an **AMD Ryzen 9** CPU and **NVIDIA RTX 5070 Ti** GPU running Linux Mint. The following known-harmless errors are automatically filtered from the journal error count:

| Pattern | Reason |
|---------|--------|
| `nvidia-wmi-ec-backlight` | ASUS ROG ACPI/backlight BIOS quirk |
| `ACPI BIOS Error` | ASUS BIOS ACPI table mismatches |
| `PCIe ACS/UnsupReq` on `[1022:14db]` | AMD Ryzen dGPU power state transitions |
| `RDSEED32 is broken` | AMD CPU errata, cosmetic |
| `hidpp_root_get_protocol_version` | Logitech HID++ device probe |
| `gkr-pam` keyring errors | Cinnamon + LightDM startup timing |
| Bluetooth SAP profile | Not used on laptop setups |
| PipeWire profile inconsistent | Bluetooth audio device connect |

PCIe ACS errors are never silently dropped — they are counted separately and displayed as an informational line so unusual spikes remain visible.

---

## Exit Codes

| Code | Meaning |
|------|---------|
| `0` | All checks passed |
| `1` | One or more warnings or failures |

---

## Contributing

Issues and PRs welcome. If you have hardware-specific noise patterns that should be filtered, please open an issue with the journal output and hardware details.

---

## License

MIT — see [LICENSE](LICENSE)
