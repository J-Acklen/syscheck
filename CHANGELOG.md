# Changelog

All notable changes to syscheck are documented here.

---

## [1.6.4] - 2026-02-25
### Fixed
- Removed remaining unused `LARGE_FOUND` variable (shellcheck SC2034)
- Replaced hardcoded username in SMART sudoers hint with `$USER` for portability

---

## [1.6.3] - 2026-02-25
### Fixed
- SC2126: replaced `grep | wc -l` with `grep -c` in package update check
- SC2034: removed unused `LARGE_FOUND` variable (partial fix — completed in 1.6.4)
- Script is now fully `shellcheck` compliant

---

## [1.6.2] - 2026-02-25
### Fixed
- Added `gkr-pam` keyring unlock errors to known-noise filter — these are a
  confirmed Cinnamon + LightDM startup timing artifact; the keyring functions
  correctly during the session
- Documented filter reason in comment block

---

## [1.6.1] - 2026-02-25
### Fixed
- Removed `sudo -v` pre-check from SMART disk health section — now relies
  directly on the `/etc/sudoers.d/syscheck` NOPASSWD rule, eliminating
  the mid-output password prompt entirely

---

## [1.6.0] - 2026-02-25
### Added
- Journal error check now filters `nvidia-wmi-ec-backlight` with both hyphen
  and underscore variants (previous regex missed kernel-level messages)
- New filters: `RDSEED32 is broken`, `asus_wmi /bin/false`, blank `kernel:` lines
- PCIe ACS/UnsupReq errors now soft-filtered: counted separately and shown as
  an informational line rather than silently dropped or counted as failures
- `/var/log` health check now splits journal vs. traditional text log sizes
  and gives the correct remediation command for each
- Individual log file threshold lowered from 500MB to 100MB
- Journal directory excluded from per-file scan to prevent double-counting

### Fixed
- World-writable scan now excludes `~/.cache/flatpak/` (flatpak index cache)
- Corrected `/var/log` vacuum suggestion — `journalctl --vacuum` does not
  affect traditional syslog/kern.log files; now suggests `logrotate` when
  text logs are the culprit

---

## [1.5.0] - 2026-02-25
### Added
- Journal error filtering for: ACPI BIOS errors, Logitech HID++ probe,
  Bluetooth SAP profile, pam_ecryptfs, PipeWire profile mismatches
- Raw error count and filtered count both shown in output
- PCIe ACS error soft-filter with informational display (initial implementation)

### Fixed
- World-writable scan: added exclusions for `~/.var/app/` (flatpak),
  `~/.steam/`, `~/.local/share/Steam/`, `~/.lmstudio/`, `~/.gradle/jdks/`
- SetUID/setGID threshold raised from 50 to 300 to account for snap packages
  bundling their own binaries (150-250 is normal on Mint with snaps)

---

## [1.4.0] - 2026-02-25
### Added
- `nvidia-persistenced` startup race condition handled — delay override via
  systemd drop-in prevents false failure on boot
- World-writable scan exclusions for snap, flatpak, and user gaming directories

### Fixed
- Systemd failed service parser: changed `awk '{print $1}'` to `awk '{print $2}'`
  with unit name regex filter — previously captured `●` bullet character as a
  false service failure
- NVIDIA dmesg check rewritten to use Xid fault codes only — generic "error"
  string grep produced false positives from benign driver messages
- `/var/log` total threshold now warns at 500MB and fails at 2GB

---

## [1.3.0] - 2026-02-25
### Added
- Version number (`VERSION` variable, `--version` flag, runtime title, help text)
- `--version` flag exits cleanly with `syscheck vX.Y.Z`
- Unrecognized arguments now print a helpful error and suggest `--help`

---

## [1.2.0] - 2026-02-25
### Added
- `--help` flag with standard man-page style output
- Log directory changed from `~/` to `/var/log/syscheck/` with fallback to
  `~/.local/share/syscheck/logs/`
- Automatic log directory creation on first `--log` run

### Fixed
- Log file naming standardized to `syscheck_YYYY-MM-DD_HH-MM-SS.log`

---

## [1.1.0] - 2026-02-25
### Added
- NVIDIA GPU section: driver version, VRAM usage, temperature, Xid fault check
- Log file health section with per-file size warnings
- Snap/flatpak path exclusions in world-writable scan

---

## [1.0.0] - 2026-02-25
### Initial release
- Standard checks: disk, inodes, memory, swap, CPU load & temp, package health,
  systemd services, journal errors, filesystem integrity, network connectivity
- Extended checks (`--full`): SMART, listening ports, world-writable files,
  setUID/setGID audit
- Color-coded output with pass/warn/fail summary
- `--full` and `--log` flags
